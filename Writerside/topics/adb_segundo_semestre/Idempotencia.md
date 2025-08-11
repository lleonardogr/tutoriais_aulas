# Idempotência no .NET

## O que é Idempotência?

Idempotência refere-se à propriedade de que o resultado de uma operação não muda quando aplicada uma ou mais vezes. Em termos de desenvolvimento de software, um método idempotente é aquele que sempre retorna o mesmo resultado (ou efeito colateral único), independentemente de quantas vezes seja chamado com os mesmos dados.

### Exemplo simples

- A requisição `GET /produto/3` sempre retornará o produto com ID 3, sendo, portanto, idempotente.

## Por que Idempotência é importante?

Ela é essencial em **arquiteturas distribuídas** e cenários onde podem ocorrer:

- Reenvio automático por timeouts de cliente / gateway
- Falhas de rede (cliente não sabe se o servidor processou)
- Retentativas de mensageria / filas

Operações idempotentes garantem consistência mesmo quando a requisição é enviada múltiplas vezes.

### Exemplo de serviço

Imagine um serviço de pagamento. Se, por falha de rede, o cliente reenviar a confirmação, um endpoint idempotente garante que o pagamento seja processado apenas uma vez.

## Padrão geral de implementação

1. **Identificar requisições unicamente**: Introduza uma chave exclusiva (ex: `Idempotency-Key`) no cabeçalho.
2. **Persistir a chave** junto com a resposta (ou um marcador de processamento) em armazenamento confiável/cache.
3. **Interceptar novas requisições**: Se a chave já existe, retornar a resposta previamente gerada (ou estado final) em vez de processar de novo.

### Boas práticas

- Gere a chave no cliente (ex: UUID) antes da primeira tentativa.
- Defina uma **expiração** adequada: longa o suficiente para cobrir retentativas, curta para não crescer indefinidamente.
- Em alta escala, use cache distribuído (Redis, por exemplo) e não apenas memória local.

## Usando a biblioteca `IdempotentAPI`

[IdempotentAPI](https://www.nuget.org/packages/IdempotentAPI/) facilita a implementação em ASP.NET Core para métodos que normalmente não são idempotentes (POST / PATCH). Ela usa um filtro que serializa os parâmetros do handler para compor a chave e armazenar a resposta.

### Passos básicos

1. **Instalar**:

```bash
dotnet add package IdempotentAPI --version 2.1.0
```

1. **Registrar serviços** em `Program.cs`:

```c#
builder.Services.AddIdempotentAPI();
```

1. **Adicionar cache distribuído** (exemplo in-memory):

```c#
builder.Services.AddIdempotentMinimalAPI(new IdempotencyOptions());
builder.Services.AddDistributedMemoryCache();
builder.Services.AddIdempotentAPIUsingDistributedCache();
```

1. **Marcar objetos de resposta como [Serializable]** (quando necessário):

```c#
[Serializable]
public record CardAddOrUpdateModel
{
   public string? Name { get; init; }
   public string? Description { get; init; }
   public string? ImageUrl { get; init; }
}
```

1. **Opcional**: marcar controllers:

```c#
[Idempotent(ExpireHours = 48)]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase { }
```

1. **Minimal API**:

```c#
cardGroup.MapPost("/", (CardAddOrUpdateModel model) =>
   {
      cards.Add(model.MapToCard());
      return Results.Created("/cards", cards);
   })
   .Accepts<Card>("application/json")
   .AddEndpointFilter<IdempotentAPIEndpointFilter>()
   .WithName("Add card")
   .WithOpenApi();
```

### Testando (fluxo genérico)

1. Chame sem `Idempotency-Key` (deve falhar se configurado para exigir).
1. Chame com uma chave: ex. `1234`.
1. Repita com a mesma chave: resposta reaproveitada.

---

## Troubleshooting: Erro 500 (Referências Circulares) com IdempotentAPI

### Cenário do problema

Ao chamar `POST /todoitems/` com cabeçalho `Idempotency-Key`, a aplicação retornava **HTTP 500**. A causa: falha de serialização por **referências circulares** quando o filtro da biblioteca tentava serializar todos os parâmetros do handler.

Handler original (inadequado):

```c#
(TodoModel todoModel, TodoDb db) => { /* ... */ }
```

O `DbContext` (`TodoDb`) contém um grafo complexo (ChangeTracker, Services, Proxies) que inclui referências circulares. Ao entrar na serialização para gerar a chave / armazenar a resposta, ocorre a exceção.

### Correção aplicada

| Arquivo | Alteração | Motivo |
|---------|-----------|--------|
| `Program.cs` | Removido `TodoDb db` dos parâmetros do `MapPost`; resolvido via escopo interno (`CreateScope`) | Evita serializar o DbContext |
| `Program.cs` | Ajustado `Results.Created($"/todoitems/{todo.Id}", todo)` | Corrigir `Location` |

### Handler após fix

```c#
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

### Como reproduzir o erro antigo

### Validando o fix

1. Subir a app:

```bash
dotnet run --project TodoApp/TodoApp.csproj --urls http://localhost:5190
```

1. Requisição inicial:

```bash
curl -i -H "Content-Type: application/json" -H "Idempotency-Key: k1" \
   -d '{"title":"Teste","isComplete":false}' http://localhost:5190/todoitems/
```

Esperado: 201 Created.


1. Repetir (mesma chave): reutiliza resposta (cache idempotente).

1. Nova chave:

```bash
curl -i -H "Content-Type: application/json" -H "Idempotency-Key: k2" \
   -d '{"title":"Outro","isComplete":true}' http://localhost:5190/todoitems/
```

Esperado: novo registro.


### Por que funciona

- O filtro agora só serializa o corpo simples (`TodoModel`).
- Objetos EF Core não entram na serialização/hash.
- Elimina ciclos e a exceção.


### Boas práticas extras

- Evitar injetar diretamente objetos pesados (DbContext, HttpContext, services) em handlers quando a lib serializa parâmetros.
- Usar DTOs/records para inputs e outputs.
- Criar gerador de chave custom se precisar normalizar corpo (ordenar propriedades, remover campos voláteis, etc.).


### Melhorias futuras

- Testes de integração (WebApplicationFactory) cobrindo: primeira execução, repetição mesma chave, nova chave.
- Logs estruturados indicando: HIT / MISS de idempotência.
- Estratégia de invalidação / TTL configurável via `IdempotencyOptions`.


---

## Conclusão

Idempotência reduz efeitos colaterais indesejados em operações não idempotentes e aumenta robustez de APIs. A biblioteca `IdempotentAPI` abstrai o mecanismo de caching e repetição, mas requer atenção aos parâmetros serializados. Evitar incluir serviços complexos garante funcionamento estável.

## Referências

- [GitHub do IdempotentAPI](https://github.com/leventozturk/IdempotentAPI)
- [NuGet Package](https://www.nuget.org/packages/IdempotentAPI/)
- [Minimal APIs Docs](https://learn.microsoft.com/aspnet/core/fundamentals/minimal-apis)


