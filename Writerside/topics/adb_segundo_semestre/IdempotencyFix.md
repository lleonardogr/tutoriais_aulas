# Idempotent POST 500 (Circular Reference) – Fix Summary

## Problema Original

Ao chamar o endpoint `POST /todoitems/` com cabeçalho `Idempotency-Key`, a aplicação retornava **HTTP 500**. A causa raiz era uma exceção de serialização por **referências circulares** ao usar a biblioteca *IdempotentAPI*.

O filtro de idempotência serializa os parâmetros do delegate do endpoint para gerar a chave / armazenar a resposta. Como o handler tinha a assinatura:

```csharp
(TodoModel todoModel, TodoDb db) => { ... }
```

O parâmetro `TodoDb` (DbContext do EF Core) era incluído no objeto a ser serializado. O `DbContext` possui um grafo complexo (ChangeTracker, Services, Proxies) com referências cíclicas, causando falha de serialização e consequentemente o 500.

## Mudanças Realizadas

| Arquivo | Alteração | Motivo |
|---------|-----------|--------|
| `Program.cs` | Removido `TodoDb db` dos parâmetros do `MapPost` e resolvido via `app.Services.CreateScope()` dentro do delegate | Evitar que o IdempotentAPI tente serializar o DbContext (elimina ciclo) |
| `Program.cs` | Ajustado `Results.Created($"/todoitems/{todo.Id}", todo)` | Corrigir `Location` (antes faltava a barra) |
| `Program.cs` | Endpoint `/static` simplificado (remoção de `async` desnecessário) | Limpeza de warning irrelevante |
| `Todo.cs` | `ContentType?.Contains("xml", OrdinalIgnoreCase)` | Evitar `NullReferenceException` em requisições sem `Content-Type` |
| `Todo.cs` | `Title` inicializado com `string.Empty` | Remover warning de propriedade não inicializada (nullable enable) |
| (Root) | Limpeza da pasta `.sonarqube/` | Build falhava devido a analyzers órfãos referenciados em diretório temporário inexistente |

## Código Principal do Handler (Após Fix)

```csharp
todogroup.MapPost("/", async (TodoModel todoModel) =>
{
    using var scope = app.Services.CreateScope();
    var db = scope.ServiceProvider.GetRequiredService<TodoDb>();

    var todo = new Todo { Title = todoModel.Title, IsComplete = todoModel.IsComplete };
    db.Todos.Add(todo);
    await db.SaveChangesAsync();

    return Results.Created($"/todoitems/{todo.Id}", todo);
})
.Accepts<TodoModel>("application/json", "application/xml")
.AddEndpointFilter<IdempotentAPIEndpointFilter>()
.WithSummary("Adiciona uma nova tarefa")
.Produces<Todo>(StatusCodes.Status201Created);
```

## Como Reproduzir o Erro (Estado Antigo)

1. Endpoint tinha assinatura com `TodoDb` como parâmetro.
2. Executar:

   ```bash
   curl -i -H "Content-Type: application/json" -H "Idempotency-Key: k1" \
        -d '{"title":"Teste","isComplete":false}' http://localhost:5190/todoitems/
   ```

3. Resultado: 500 + log interno com falha de serialização (referência circular).

## Como Validar o Fix

1. Subir a aplicação em porta livre:

   ```bash
   dotnet run --project TodoApp/TodoApp.csproj --urls http://localhost:5190
   ```

2. Primeira requisição (criação):

   ```bash
   curl -i -H "Content-Type: application/json" -H "Idempotency-Key: k1" \
        -d '{"title":"Teste","isComplete":false}' http://localhost:5190/todoitems/
   ```

   Esperado: 201 Created + JSON do todo.

3. Repetir o mesmo comando (mesma `Idempotency-Key`):

   - Esperado: resposta idempotente (reutilizada) – sem 500.

4. Fazer outra com nova chave:

   ```bash
   curl -i -H "Content-Type: application/json" -H "Idempotency-Key: k2" \
        -d '{"title":"Outro","isComplete":true}' http://localhost:5190/todoitems/
   ```

   Esperado: novo registro.

## Por Que a Abordagem Funciona

- O IdempotentAPI só vê agora o corpo simples (`TodoModel`) como entrada.
- Objetos EF Core não entram mais no processo de serialização/hash.
- Ciclos internos do `DbContext` são completamente ignorados.

## Boas Práticas Relacionadas

- Evitar expor entidades de domínio diretamente em endpoints (usar DTOs / records de request e response).
- Manter handlers idempotentes livres de serviços pesados nos parâmetros quando a lib de idempotência serializa automaticamente.
- Se precisar muito da injeção padrão, criar um filtro/custom key generator que ignore tipos específicos (ex: `DbContext`).

## Melhorias Futuras (Opcional)

- Criar testes de integração (WebApplicationFactory) para validar idempotência.
- Adicionar custom policy de geração de chave se quiser normalizar corpo (ex: ordenar propriedades / remover campos voláteis).
- Implementar logs estruturados para hits idempotentes (cache vs execução nova).

## Referências

- IdempotentAPI: <https://github.com/mguinness/IdempotentAPI> (ou repositório oficial equivalente)
- Docs Minimal APIs: <https://learn.microsoft.com/aspnet/core/fundamentals/minimal-apis>
- EF Core InMemory: <https://learn.microsoft.com/ef/core/testing/in-memory>

---

Se precisar, posso adicionar testes automatizados ou criar um gerador de chave custom. Basta pedir.
