# OpenAPI e Swagger com Minimal API

## 1. Introdução ao OpenAPI e Scalar:

**OpenAPI**: Um padrão de especificação para definir APIs RESTful, permitindo a descrição de endpoints, parâmetros, respostas e mais.

**Scalar**: Conjunto de ferramentas que implementa a especificação OpenAPI, oferecendo funcionalidades para documentação e testes de APIs, substitui o swagger.

## 2. Configuração do Scalar e OpenAPI em uma Minimal API:

**Adicionar pacotes necessários**: Inclua os pacotes NuGet necessários para o Scalar no seu projeto.

```bash
dotnet add package Microsoft.AspNetCore.OpenApi
dotnet add package Scalar.AspNetCore
```

**Configurar Swagger no Program.cs**: Adicione a configuração do Scalar no pipeline de serviços e no middleware.

```C#
var builder = WebApplication.CreateBuilder(args);


var app = builder.Build();

// Configura o pipeline de requisições HTTP.
if (app.Environment.IsDevelopment())
{
    app.MapOpenApi();
    app.MapScalarApiReference();
}

// Outros middlewares e endpoints

app.Run();

```

### 3. Incluir metadados dos endpoints com OpenAPI
### 3.1 Remover Endpoint da Definição Swagger:

Para adicionar informações como descrição, tags e outras metadados aos endpoints, utilize os métodos WithDescription, WithTags, etc.

```C#
todoItemsGroup.MapGet("/", GetAllTodos)
            .WithSummary("Pega todas as tarefas cadastradas")
            .WithDescription("Acessa o banco de dados, e retorna todas
             as tarefas cadastradas");
        
todoItemsGroup.MapGet("/complete", 
            GetCompleteTodos)
            .WithSummary("Pega todas as tarefas cadastradas filtradas 
            por apenas as completas")
            .WithDescription("Acessa o banco de dados, e retorna todas 
            as tarefas cadastradas sendo apenas as completas");
       
```

### 3.2 Esconder Endpoint da especificação:

Para excluir um endpoint da definição do Swagger, é necessário utilizar a extensão ExcludeFromDescription no método do endpoint:

```C#
app.MapGet("/remover-da-doc", () => 
"Este endpoint não aparecerá na documentação.")
.ExcludeFromDescription();
```

### 3.3 Marcar Endpoint como Obsoleto:

Para marcar um endpoint como obsoleto no Swagger, utilize o atributo Deprecated nas opções da rota:

```C#
pp.MapGet("/rota-obsoleta", () => "Esta rota é obsoleta.")
.WithOpenApi(operation => new(operation)
    {
        Deprecated = true
    })
```


### 3.4 Adicionar Tags às Rotas:

As tags podem ser adicionadas para agrupar endpoints e melhorar a organização da documentação Swagger. Use a seguinte abordagem para adicionar tags às rotas:

```C#
app.MapGet("/minha-rota", () => "Conteúdo da rota.")
    .WithTags("Grupo de Rotas 1");
```

### 3.5 Adicionar Descrição das Rotas e Retornos:
Você pode utilizar tanto explicitamente quanto por typed results para adicionar descrições detalhadas de rotas e seus retornos com OpenAPI.

```C#
todoItemsGroup.MapPost("/", CreateTodo)
            .WithSummary("Cria uma nova tarefa")
            .WithDescription("Recebe os dados da tarefa, salva no 
            banco de dados e retorna a tarefa criada com seu ID")
            .Produces<TodoItemDTO>(201)
            .ProducesValidationProblem()
            .WithOpenApi();
            
 static async Task<Results<Ok<TodoItemDTO>, NotFound>> GetTodo(
 [Description("Id da tarefa a ser buscada")] int id, TodoDb db)
    {
        return await db.Todos.FindAsync(id)
            is { } todo
            ? TypedResults.Ok(new TodoItemDTO(todo))
            : TypedResults.NotFound();
    }
    
    static async Task<Results<Created<TodoItemDTO>, 
    InternalServerError>> 
    CreateTodo(TodoItemDTO todoItemDTO, TodoDb db)
    {
        var todoItem = new Todo
        {
            IsComplete = todoItemDTO.IsComplete,
            Name = todoItemDTO.Name
        };
    
        db.Todos.Add(todoItem);
        await db.SaveChangesAsync();
    
        todoItemDTO = new TodoItemDTO(todoItem);
    
        return TypedResults.Created($"/todoitems/{todoItem.Id}", 
        todoItemDTO);
    }           
```

## 6. Benefícios da Integração com OpenAPI e Swagger:

**Documentação Automática**: Geração automática de documentação baseada no código.

**Testes Interativos**: Capacidade de testar a API diretamente na UI do Scalar.
Facilidade de Integração: Integração com outras ferramentas e serviços baseados na especificação OpenAPI.

<seealso>
    <category ref="wrs">
        <a href="https://learn.microsoft.com/en-us/aspnet/core/fundamentals/openapi/aspnetcore-openapi"></a>
    </category>
</seealso>
