# Introdução Minimal API

Minimal API é um estilo de construção de APIs que visa simplificar o processo de criação de aplicações web, especialmente em termos de estrutura e configuração. O termo ganhou destaque no contexto do .NET, mais especificamente no ASP.NET Core 6.0 e versões posteriores, onde a Microsoft introduziu o conceito de Minimal APIs.

## Aqui estão os principais aspectos de uma Minimal API:

**Simplicidade:** Diferente de arquiteturas mais complexas (como MVC), uma Minimal API foca em um design mais enxuto e direto. O código de uma aplicação pode ser definido com poucas linhas, sem a necessidade de uma estrutura extensa.

**Menos Configuração:** Em vez de precisar configurar roteamento, controladores, e outras dependências, a Minimal API permite definir endpoints diretamente no arquivo principal da aplicação (geralmente o Program.cs).

**Ideal para Microserviços:** Minimal APIs são especialmente adequadas para aplicações de menor escala, como microserviços, onde você só precisa de endpoints HTTP simples para executar tarefas específicas.

**Performance:** Com uma estrutura mais leve, a Minimal API tende a ser mais rápida e eficiente do que APIs tradicionais, especialmente em cenários de alto desempenho.

## Pré-requisitos

Para criar um projeto Minimal API no .NET 8, você precisará cumprir alguns pré-requisitos essenciais, tanto em termos de ferramentas quanto de configuração. Aqui estão os principais pré-requisitos:

- **.NET 8 SDK Instalado**
   - **Requisito Fundamental:** Ter o .NET 8 SDK instalado na sua máquina é essencial. O SDK contém todas as ferramentas necessárias para compilar e executar aplicações .NET, incluindo as Minimal APIs.
   - **Download:** Você pode baixar o .NET 8 SDK no site oficial da Microsoft aqui.

- **IDE (Ambiente de Desenvolvimento Integrado)**
   - **Visual Studio 2022 (versão 17.8 ou superior):** O Visual Studio é uma das IDEs mais populares para desenvolvimento em .NET. Certifique-se de que você tenha a versão mais recente, que suporte o .NET 8.
   - Durante a instalação, selecione a carga de trabalho "ASP.NET e desenvolvimento web".
   - **Visual Studio Code:** Outra opção leve, que também suporta o desenvolvimento com Minimal APIs, embora requeira a instalação de extensões, como o C# Extension.
   - **JetBrains Rider:** Se você preferir uma IDE alternativa, o Rider é uma opção compatível com .NET e suporta o .NET 8.

- **Conhecimento Básico de C# e ASP.NET Core**
   - Familiaridade com C# e ASP.NET Core será útil, pois as Minimal APIs são desenvolvidas com base no framework ASP.NET Core e na linguagem C#.

- **Bibliotecas e Dependências**
   - Ao iniciar um projeto de Minimal API, o template incluirá as bibliotecas necessárias para a criação da API. Entretanto, para alguns recursos avançados, você pode precisar instalar pacotes adicionais do NuGet.

- **SDK de Desenvolvimento Web Instalado**
   - O SDK de desenvolvimento web do .NET, que faz parte do .NET 8 SDK, deve estar instalado para que você possa criar APIs e aplicações web.

- **Ferramenta de Linha de Comando (Opcional)**
   - Caso prefira trabalhar com a linha de comando, o CLI do .NET (dotnet) permite criar, compilar e executar projetos sem uma IDE. Certifique-se de que o caminho do SDK esteja corretamente configurado.
   - **Exemplo de criação via CLI:**
     ```bash
     dotnet new webapi --minimal -n MinhaMinimalAPI
     ```

## Passo a passo para criar projeto de Minimal API no Visual Studio

1. Abra o Visual Studio 2022 e selecione a opção ASP.NET Core Web API.
2. Forneça um nome e escolha um local para o seu projeto.
3. Selecione o framework desejado (certifique-se de que está usando .NET 8).
4. Marque as opções "Configurar para HTTPS" e "Habilitar suporte OpenAPI".
5. Deixe a caixa de seleção "Usar controladores" desmarcada.
6. Clique no botão "Criar".

## Exploração do arquivo Program.cs

Vamos agora explorar o arquivo `Program.cs`, onde os diversos componentes da sua Minimal API se integram. Esse arquivo é responsável por orquestrar a configuração dos serviços e a definição dos endpoints da API.

```C#
using Microsoft.AspNetCore.Builder;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

var builder = WebApplication.CreateBuilder(args);

// Add services to the container.
builder.Services.AddControllers();
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();
builder.Services.AddSingleton<IBookService, BookService>();

var app = builder.Build();

// Configure the HTTP request pipeline.
if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}

app.UseHttpsRedirection();

// Hello World
app.MapGet("/", () => "Hello, World!");

// Get all books
app.MapGet("/books", (IBookService bookService) =>
    TypedResults.Ok(bookService.GetBooks()))
    .WithName("GetBooks");

// Get a specific book by ID
app.MapGet("/books/{id}", Results<Ok<Book>, NotFound>(IBookService bookService, int id) =>
{
    var book = bookService.GetBook(id);
    return book is not null ? TypedResults.Ok(book) : TypedResults.NotFound();
}).WithName("GetBookById");

// Add a new book with validation
app.MapPost("/books", (IBookService bookService, Book newBook) =>
{
    if (string.IsNullOrWhiteSpace(newBook.Title))
    {
        return TypedResults.BadRequest("Title cannot be empty");
    }

    bookService.AddBook(newBook);
    return TypedResults.Created($"/books/{newBook.Id}", newBook);
}).WithName("AddBook");

// Update an existing book with validation
app.MapPut("/books/{id}", (IBookService bookService, int id, Book updatedBook) =>
{
    if (string.IsNullOrWhiteSpace(updatedBook.Title))
    {
        return TypedResults.BadRequest("Title cannot be empty");
    }

    var existingBook = bookService.GetBook(id);
    if (existingBook is null)
    {
        return TypedResults.NotFound();
    }

    bookService.UpdateBook(id, updatedBook);
    return TypedResults.Ok();
}).WithName("UpdateBook");

// Delete a book by ID
app.MapDelete("/books/{id}", (IBookService bookService, int id) =>
{
    var existingBook = bookService.GetBook(id);
    if (existingBook is null)
    {
        return TypedResults.NotFound();
    }

    bookService.DeleteBook(id);
    return TypedResults.NoContent();
}).WithName("DeleteBook");

app.Run();
```

## Exemplo de BookService e IBookService

O código a seguir demonstra uma classe chamada `BookService`, responsável por gerenciar uma coleção de livros, enquanto a interface `IBookService` define o contrato para interação com esse serviço. Este é um exemplo simples de um serviço que executa operações CRUD em uma coleção de livros.

```C#
using System.Collections.Generic;
using System.Linq;

namespace MinimalApi
{
    public interface IBookService
    {
        IReadOnlyList<Book> GetBooks();
        Book? GetBook(int id);
        void AddBook(Book book);
        void UpdateBook(int id, Book updatedBook);
        void DeleteBook(int id);
    }

    public class BookService : IBookService
    {
        private readonly Dictionary<int, Book> _books;
        private int _nextId;

        public BookService()
        {
            _books = new Dictionary<int, Book>();
            _nextId = 1;

            // Initial books
            AddBook(new Book { Id = _nextId++, Title = "Clean Code: A Handbook of Agile Software Craftsmanship", Author = "Robert C. Martin" });
            AddBook(new Book { Id = _nextId++, Title = "Design Patterns: Elements of Reusable Object-Oriented Software", Author = "Erich Gamma, Richard Helm, Ralph Johnson, John Vlissides" });
            AddBook(new Book { Id = _nextId++, Title = "Refactoring: Improving the Design of Existing Code", Author = "Martin Fowler" });
            AddBook(new Book { Id = _nextId++, Title = "Code Complete: A Practical Handbook of Software Construction", Author = "Steve McConnell" });
        }

        public IReadOnlyList<Book> GetBooks()
        {
            return _books.Values.ToList();
        }

        public Book? GetBook(int id)
        {
            _books.TryGetValue(id, out var book);
            return book;
        }

        public void AddBook(Book book)
        {
            if (string.IsNullOrWhiteSpace(book.Title) || string.IsNullOrWhiteSpace(book.Author))
                throw new ArgumentException("Book title and author cannot be empty.");

            book.Id = _nextId++;
            _books[book.Id] = book;
        }

        public void UpdateBook(int id, Book updatedBook)
        {
            if (string.IsNullOrWhiteSpace(updatedBook.Title) || string.IsNullOrWhiteSpace(updatedBook.Author))
                throw new ArgumentException("Book title and author cannot be empty.");

            if (_books.ContainsKey(id))
            {
                updatedBook.Id = id; // Preserve the existing ID
                _books[id] = updatedBook;
            }
            else
            {
                throw new KeyNotFoundException("Book not found.");
            }
        }

        public void DeleteBook(int id)
        {
            if (!_books.Remove(id))
            {
                throw new KeyNotFoundException("Book not found.");
            }
        }
    }

    public class Book
    {
        public int Id { get; set; }
        public string Title { get; set; } = string.Empty;
        public string Author { get; set; } = string.Empty;
    }
}
```

# Tópicos Complementares

## 1. API Rate Limiting
- **O que é**: Limita o número de solicitações que um cliente pode fazer a uma API em um período de tempo específico.
- **Como implementar**: Pode ser feito usando middleware ou bibliotecas como AspNetCoreRateLimit. Configura-se o limite de requisições no `Startup.cs` ou `Program.cs`.

```C#
builder.Services.AddMemoryCache();
builder.Services.AddInMemoryRateLimiting();
builder.Services.AddSingleton<IRateLimitConfiguration, RateLimitConfiguration>();
```

## 2. Error Handling
- **O que é**: Tratamento de exceções e envio de mensagens de erro apropriadas para os clientes.
- **Como implementar**: Pode ser feito através de middleware de tratamento de erros.

```C#
app.UseExceptionHandler("/error");
```
```C#
app.Map("/error", (HttpContext httpContext) =>
{
    var exception = httpContext.Features.Get<IExceptionHandlerFeature>();
    return TypedResults.Problem(title: "An error occurred", detail: exception?.Error.Message);
});
```

## 3. Caching
- **O que é**: Armazenamento de dados temporários para reduzir a carga em recursos e melhorar o desempenho.
- **Como implementar**: Usando middleware de cache ou serviços de cache como Redis ou Memcached.

```C#
builder.Services.AddResponseCaching();
app.UseResponseCaching();
```
```C#
app.MapGet("/books", async (IBookService bookService, HttpContext httpContext) =>
{
    var cacheKey = "allBooks";
    var cache = httpContext.RequestServices.GetRequiredService<IMemoryCache>();
    if (!cache.TryGetValue(cacheKey, out List<Book> books))
    {
        books = bookService.GetBooks();
        var cacheEntryOptions = new MemoryCacheEntryOptions().SetSlidingExpiration(TimeSpan.FromMinutes(1));
        cache.Set(cacheKey, books, cacheEntryOptions);
    }
    return TypedResults.Ok(books);
});
```

## 4. Request and Response Compression
- **O que é**: Compressão dos dados de solicitação e resposta para reduzir o uso de largura de banda e melhorar o desempenho.
- **Como implementar**: Usando middleware de compressão.

```C#
builder.Services.AddResponseCompression(options =>
{
    options.EnableForHttps = true;
    options.MimeTypes = ResponseCompressionDefaults.MimeTypes.Concat(new[] { "application/json" });
});
app.UseResponseCompression();
```
## 5. Authentication and Authorization
- **O que é**: Controle de acesso à API, garantindo que apenas usuários autenticados e autorizados possam acessar certos recursos.
- **Como implementar**: Usando middleware de autenticação e autorização, e configurando esquemas de autenticação (e.g., JWT).

```C#
builder.Services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(options =>
    {
        options.TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = true,
            ValidateAudience = true,
            ValidateLifetime = true,
            ValidateIssuerSigningKey = true,
            ValidIssuer = "yourIssuer",
            ValidAudience = "yourAudience",
            IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes("yourSecretKey"))
        };
    });
builder.Services.AddAuthorization();
app.UseAuthentication();
app.UseAuthorization();
```

## Principais tópicos abordados

1. **Configuração do WebApplication**:
   - Criação e configuração do aplicativo usando `WebApplication.CreateBuilder` e `builder.Build()`.
   - Adição de serviços ao contêiner com `builder.Services.Add...`.

2. **Mapeamento de Endpoints**:
   - Definição de rotas HTTP com `app.MapGet`, `app.MapPost`, `app.MapPut` e `app.MapDelete`.
   - Uso de métodos de resultados tipados como `TypedResults.Ok()`, `TypedResults.Created()`, `TypedResults.NoContent()`, e `TypedResults.NotFound()`.

3. **Injeção de Dependências**:
   - Registro de serviços no contêiner usando `builder.Services.AddSingleton` ou outras variações.

4. **Ambiente de Desenvolvimento**:
   - Configuração condicional para ambientes de desenvolvimento (`app.Environment.IsDevelopment()`).
   - Configuração de Swagger para documentação da API.

5. **Configuração de Middleware**:
   - Configuração do pipeline de middleware com métodos como `app.UseHttpsRedirection()`.

6. **Definição de Modelos**:
   - Criação de classes de modelo (`Book`) e interfaces (`IBookService`).

7. **Manipulação de Dados**:
   - Implementação de serviços para manipulação de dados com métodos para adicionar, atualizar, e excluir dados.

8. **Respostas HTTP e Erros**:
   - Manipulação de erros e validações usando retornos apropriados e tratamento de exceções.

9. **Uso de IReadOnlyList**:
   - Implementação de coleções imutáveis para garantir a integridade dos dados.

10. **Uso de Dictionary para Armazenamento de Dados**:
    - Utilização de `Dictionary` para armazenamento e acesso rápido a dados com base em IDs.

11. **API Rate Limiting**:
    - Limitação do número de solicitações de um cliente usando middleware ou bibliotecas como `AspNetCoreRateLimit`.

12. **Error Handling**:
    - Tratamento de exceções e envio de mensagens de erro apropriadas através de middleware.

13. **Caching**:
    - Armazenamento temporário de dados para melhorar o desempenho usando middleware de cache ou serviços como Redis ou Memcached.

14. **Request and Response Compression**:
    - Compressão de dados de solicitação e resposta para reduzir o uso de largura de banda e melhorar o desempenho.

15. **Authentication and Authorization**:
    - Controle de acesso à API usando middleware de autenticação e autorização, e configuração de esquemas de autenticação (e.g., JWT).

<seealso>
    <category ref="wrs">
        <a href="https://medium.com/@alibenchaabene/minimal-api-in-net-8-a-simplfied-approach-to-build-web-apis-6b772059f17c"></a>
        <a href="https://learn.microsoft.com/pt-br/aspnet/core/performance/rate-limit?view=aspnetcore-8.0"></a>
    </category>
</seealso>