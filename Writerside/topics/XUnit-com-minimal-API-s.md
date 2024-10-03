# xUnit.net - Parte 2: Testando Minimal APIs

## Introdução

Com o lançamento do .NET 6, a Microsoft introduziu as **Minimal APIs**, uma forma simplificada e leve de criar APIs RESTful usando menos código e sem a necessidade de classes de controlador. Esta abordagem facilita o desenvolvimento rápido de serviços HTTP e é ideal para microsserviços, APIs simples ou aplicações que não necessitam da estrutura completa do ASP.NET Core MVC.

Neste guia, daremos continuidade ao estudo do **xUnit.net**, focando agora em como testar Minimal APIs. Exploraremos como configurar testes para endpoints HTTP, utilizar o **WebApplicationFactory** para hospedar a aplicação em memória durante os testes, e escrever testes que verificam o comportamento da API.

---

## O que são Minimal APIs?

As **Minimal APIs** permitem que você crie endpoints HTTP diretamente no método `Program.cs`, sem a necessidade de classes de controlador ou ações separadas. Elas utilizam os mesmos fundamentos do ASP.NET Core, mas com uma sintaxe mais concisa.

**Exemplo de uma Minimal API:**

```C#
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

app.MapGet("/hello", () => "Hello World!");

app.Run();
```

Neste exemplo, o endpoint `/hello` retorna a string "Hello World!".

---

## Configurando o Ambiente de Testes

### Pré-requisitos

- **SDK do .NET 6 ou superior**: Certifique-se de ter o SDK do .NET 6 instalado.
- **IDE**: Visual Studio 2022, Visual Studio Code ou outro editor de sua preferência.

### Estrutura do Projeto

Vamos criar uma solução com dois projetos:

1. **Projeto da API**: Contém a Minimal API.
2. **Projeto de Testes**: Contém os testes unitários usando xUnit.net.

### Passo 1: Criar a Solução e o Projeto da API

```bash
dotnet new sln -o MinimalApiExample
cd MinimalApiExample
dotnet new web -o MinimalApi
dotnet sln add MinimalApi/MinimalApi.csproj
```

### Passo 2: Criar o Projeto de Testes

```bash
dotnet new xunit -o MinimalApi.Tests
dotnet add MinimalApi.Tests/MinimalApi.Tests.csproj reference MinimalApi/MinimalApi.csproj
dotnet sln add MinimalApi.Tests/MinimalApi.Tests.csproj
```

---

## Configurando o Projeto da API

Para o propósito deste guia, criaremos uma Minimal API simples que expõe endpoints para manipular um recurso de "Produtos".

### Definindo o Modelo

```C#
// MinimalApi/Models/Product.cs
namespace MinimalApi.Models
{
    public record Product(int Id, string Name, decimal Price);
}
```

### Configurando os Endpoints

No `Program.cs`, vamos adicionar endpoints para CRUD básico.

```C#
// MinimalApi/Program.cs
using MinimalApi.Models;

var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

// Dados em memória para o exemplo
var products = new List<Product>
{
    new Product(1, "Teclado", 100.0m),
    new Product(2, "Mouse", 50.0m),
    new Product(3, "Monitor", 500.0m)
};

// Endpoints
app.MapGet("/products", () => products);

app.MapGet("/products/{id}", (int id) =>
{
    var product = products.FirstOrDefault(p => p.Id == id);
    return product is not null ? Results.Ok(product) : Results.NotFound();
});

app.MapPost("/products", (Product product) =>
{
    products.Add(product);
    return Results.Created($"/products/{product.Id}", product);
});

app.MapPut("/products/{id}", (int id, Product updatedProduct) =>
{
    var product = products.FirstOrDefault(p => p.Id == id);
    if (product is null)
        return Results.NotFound();

    products.Remove(product);
    products.Add(updatedProduct);
    return Results.NoContent();
});

app.MapDelete("/products/{id}", (int id) =>
{
    var product = products.FirstOrDefault(p => p.Id == id);
    if (product is not null)
    {
        products.Remove(product);
        return Results.NoContent();
    }
    return Results.NotFound();
});

app.Run();
```

---

## Testando a Minimal API com xUnit.net

Para testar a Minimal API, precisamos hospedar a aplicação em um servidor de teste em memória. O **Microsoft.AspNetCore.Mvc.Testing** fornece a classe `WebApplicationFactory<TEntryPoint>` que facilita esse processo.

### Passo 1: Instalar Pacotes Necessários

No projeto de testes, instale o pacote **Microsoft.AspNetCore.Mvc.Testing**:

```bash
dotnet add MinimalApi.Tests/MinimalApi.Tests.csproj package Microsoft.AspNetCore.Mvc.Testing
```

### Passo 2: Criar a Classe de Teste

```C#
// MinimalApi.Tests/ProductApiTests.cs
using System.Net;
using System.Net.Http.Json;
using MinimalApi.Models;
using Xunit;

namespace MinimalApi.Tests
{
    public class ProductApiTests : IClassFixture<WebApplicationFactory<Program>>
    {
        private readonly HttpClient _client;

        public ProductApiTests(WebApplicationFactory<Program> factory)
        {
            _client = factory.CreateClient();
        }

        [Fact]
        public async Task GetProducts_ReturnsListOfProducts()
        {
            // Act
            var response = await _client.GetAsync("/products");

            // Assert
            response.EnsureSuccessStatusCode();
            var products = await response.Content.ReadFromJsonAsync<IEnumerable<Product>>();

            Assert.NotNull(products);
            Assert.NotEmpty(products);
        }

        [Fact]
        public async Task GetProductById_ReturnsProduct_WhenProductExists()
        {
            // Arrange
            int productId = 1;

            // Act
            var response = await _client.GetAsync($"/products/{productId}");

            // Assert
            response.EnsureSuccessStatusCode();
            var product = await response.Content.ReadFromJsonAsync<Product>();

            Assert.NotNull(product);
            Assert.Equal(productId, product.Id);
        }

        [Fact]
        public async Task GetProductById_ReturnsNotFound_WhenProductDoesNotExist()
        {
            // Arrange
            int productId = 999;

            // Act
            var response = await _client.GetAsync($"/products/{productId}");

            // Assert
            Assert.Equal(HttpStatusCode.NotFound, response.StatusCode);
        }

        [Fact]
        public async Task CreateProduct_ReturnsCreatedProduct()
        {
            // Arrange
            var newProduct = new Product(4, "Webcam", 200.0m);

            // Act
            var response = await _client.PostAsJsonAsync("/products", newProduct);

            // Assert
            response.EnsureSuccessStatusCode();
            Assert.Equal(HttpStatusCode.Created, response.StatusCode);

            var createdProduct = await response.Content.ReadFromJsonAsync<Product>();
            Assert.Equal(newProduct.Id, createdProduct.Id);
            Assert.Equal(newProduct.Name, createdProduct.Name);
            Assert.Equal(newProduct.Price, createdProduct.Price);
        }

        [Fact]
        public async Task UpdateProduct_ReturnsNoContent_WhenProductExists()
        {
            // Arrange
            int productId = 1;
            var updatedProduct = new Product(productId, "Teclado Mecânico", 150.0m);

            // Act
            var response = await _client.PutAsJsonAsync($"/products/{productId}", updatedProduct);

            // Assert
            Assert.Equal(HttpStatusCode.NoContent, response.StatusCode);
        }

        [Fact]
        public async Task DeleteProduct_ReturnsNoContent_WhenProductExists()
        {
            // Arrange
            int productId = 2;

            // Act
            var response = await _client.DeleteAsync($"/products/{productId}");

            // Assert
            Assert.Equal(HttpStatusCode.NoContent, response.StatusCode);
        }
    }
}
```

---

## Explicando o Código de Teste

### Classe `ProductApiTests`

- Implementa `IClassFixture<WebApplicationFactory<Program>>`, que fornece uma instância de `HttpClient` configurada para testar a aplicação.
- O `WebApplicationFactory<TEntryPoint>` hospeda a aplicação em memória para testes.

### Métodos de Teste

#### `GetProducts_ReturnsListOfProducts`

- **Objetivo**: Verificar se o endpoint `/products` retorna uma lista de produtos.
- **Passos**:
    - Faz uma requisição GET para `/products`.
    - Verifica se a resposta tem status de sucesso.
    - Lê o conteúdo da resposta como uma coleção de `Product`.
    - Verifica se a coleção não é nula e não está vazia.

#### `GetProductById_ReturnsProduct_WhenProductExists`

- **Objetivo**: Verificar se o endpoint `/products/{id}` retorna um produto existente.
- **Passos**:
    - Define um `productId` existente.
    - Faz uma requisição GET para `/products/{productId}`.
    - Verifica se a resposta tem status de sucesso.
    - Lê o conteúdo da resposta como um `Product`.
    - Verifica se o produto não é nulo e se o `Id` corresponde.

#### `GetProductById_ReturnsNotFound_WhenProductDoesNotExist`

- **Objetivo**: Verificar se o endpoint `/products/{id}` retorna 404 quando o produto não existe.
- **Passos**:
    - Define um `productId` que não existe.
    - Faz uma requisição GET para `/products/{productId}`.
    - Verifica se a resposta tem status 404 Not Found.

#### `CreateProduct_ReturnsCreatedProduct`

- **Objetivo**: Verificar se é possível criar um novo produto.
- **Passos**:
    - Define um novo produto.
    - Faz uma requisição POST para `/products` com o produto no corpo.
    - Verifica se a resposta tem status 201 Created.
    - Lê o conteúdo da resposta como um `Product`.
    - Verifica se os dados do produto criado correspondem aos enviados.

#### `UpdateProduct_ReturnsNoContent_WhenProductExists`

- **Objetivo**: Verificar se é possível atualizar um produto existente.
- **Passos**:
    - Define um `productId` existente e um produto atualizado.
    - Faz uma requisição PUT para `/products/{productId}` com o produto atualizado.
    - Verifica se a resposta tem status 204 No Content.

#### `DeleteProduct_ReturnsNoContent_WhenProductExists`

- **Objetivo**: Verificar se é possível deletar um produto existente.
- **Passos**:
    - Define um `productId` existente.
    - Faz uma requisição DELETE para `/products/{productId}`.
    - Verifica se a resposta tem status 204 No Content.

---

## Executando os Testes

Para executar os testes, utilize o comando:

```bash
dotnet test
```

**Saída Esperada:**

```
Test Run Successful.
Total tests: 6
     Passed: 6
```

---

## Personalizando o `WebApplicationFactory`

Em alguns casos, você pode precisar personalizar a configuração da aplicação durante os testes, por exemplo, para usar um banco de dados em memória ou substituir serviços.

### Criando uma Subclasse de `WebApplicationFactory`

```C#
// MinimalApi.Tests/CustomWebApplicationFactory.cs
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.Mvc.Testing;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

namespace MinimalApi.Tests
{
    public class CustomWebApplicationFactory : WebApplicationFactory<Program>
    {
        protected override IHost CreateHost(IHostBuilder builder)
        {
            builder.ConfigureServices(services =>
            {
                // Configurações de serviços para testes
                // Por exemplo, substituir serviços por mocks
            });

            return base.CreateHost(builder);
        }
    }
}
```

### Usando a Classe Personalizada nos Testes

```C#
public class ProductApiTests : IClassFixture<CustomWebApplicationFactory>
{
    private readonly HttpClient _client;

    public ProductApiTests(CustomWebApplicationFactory factory)
    {
        _client = factory.CreateClient();
    }

    // Testes...
}
```

---

## Dicas e Boas Práticas

### Isolamento dos Testes

- Certifique-se de que cada teste é independente dos outros.
- Se os dados usados nos testes são compartilhados (como a lista de produtos em memória), isso pode causar interferência entre os testes.
- Considere redefinir o estado antes de cada teste ou usar escopos de serviço para criar instâncias separadas.

### Uso de Banco de Dados em Memória

- Para testar interações com o banco de dados, você pode usar um banco de dados em memória como o **SQLite in-memory** ou o **InMemoryDatabase** do Entity Framework Core.

**Exemplo com Entity Framework Core:**

```C#
builder.Services.AddDbContext<AppDbContext>(options =>
    options.UseInMemoryDatabase("TestDb"));
```

### Testes Assíncronos

- Use os métodos assíncronos do `HttpClient` e `xUnit` para garantir que os testes não bloqueiem o thread.

### Verificação de Conteúdo

- Além de verificar o status da resposta, verifique o conteúdo para garantir que a API está retornando os dados corretos.
- Use `Assert.Equal` para comparar valores esperados e reais.

---

## Testando Erros e Exceções

Para garantir que sua API lida corretamente com erros, escreva testes que simulem condições de erro.

**Exemplo:**

```C#
[Fact]
public async Task UpdateProduct_ReturnsNotFound_WhenProductDoesNotExist()
{
    // Arrange
    int productId = 999;
    var updatedProduct = new Product(productId, "Produto Inexistente", 0.0m);

    // Act
    var response = await _client.PutAsJsonAsync($"/products/{productId}", updatedProduct);

    // Assert
    Assert.Equal(HttpStatusCode.NotFound, response.StatusCode);
}
```

---

## Mockando Serviços Externos

Se sua Minimal API depende de serviços externos, você pode usar mocks para isolar os testes.

**Exemplo:**

- Suponha que sua API use um serviço de envio de e-mails.
- Crie uma interface `IEmailService` e uma implementação de mock para os testes.
- No `CustomWebApplicationFactory`, substitua o serviço real pelo mock.

---

## Testando Autenticação e Autorização

Se sua API usa autenticação e autorização, você precisa configurar o contexto de segurança nos testes.

### Simulando um Usuário Autenticado

- Use middleware personalizado ou configure o `HttpClient` para incluir tokens de autenticação.

**Exemplo:**

```C#
_client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", "token_de_teste");
```

### Configurando Serviços de Autenticação

- No `CustomWebApplicationFactory`, configure serviços de autenticação para os testes.

---

## Recursos Adicionais

- **Documentação Oficial do xUnit.net**: [https://xunit.net/](https://xunit.net/)
- **Testes de Integração no ASP.NET Core**: [Microsoft Docs](https://docs.microsoft.com/pt-br/aspnet/core/test/integration-tests)
- **Minimal APIs Overview**: [Microsoft Docs](https://docs.microsoft.com/pt-br/aspnet/core/fundamentals/minimal-apis)

---

## Exercícios Práticos

1. **Adicionar Validações à API**:
    - Implemente validações nos endpoints, como verificar se o nome do produto não é nulo.
    - Escreva testes para verificar se a API retorna erros adequados quando as validações falham.

2. **Testar Paginação e Filtros**:
    - Adicione suporte a paginação e filtros nos endpoints.
    - Escreva testes que verifiquem se a paginação e os filtros funcionam corretamente.

3. **Implementar Testes de Performance**:
    - Use ferramentas como o BenchmarkDotNet para medir o desempenho de sua API.
    - Analise os resultados e otimize o código conforme necessário.

---

## Conclusão

Testar Minimal APIs com xUnit.net é um processo direto que utiliza as mesmas ferramentas e conceitos dos testes em aplicações ASP.NET Core convencionais. Ao hospedar a aplicação em memória usando o `WebApplicationFactory`, podemos escrever testes que verificam os endpoints HTTP de forma eficaz.

Neste guia, cobrimos:

- Como configurar o ambiente de testes para Minimal APIs.
- Escrever testes para endpoints GET, POST, PUT e DELETE.
- Personalizar o `WebApplicationFactory` para cenários avançados.
- Boas práticas para garantir a confiabilidade e a manutenção dos testes.

A incorporação de testes automatizados em seu fluxo de desenvolvimento melhora a qualidade do software e facilita a detecção precoce de bugs e regressões.

---

## Observações Finais

- **Atualização Contínua**: Mantenha seus testes atualizados conforme a API evolui.
- **Cobertura de Testes**: Busque uma cobertura abrangente, incluindo cenários de erro e validações.
- **Colaboração**: Compartilhe conhecimento com sua equipe e incentive a cultura de testes.

Espero que este guia tenha expandido seu conhecimento sobre testes com xUnit.net e que você esteja mais preparado para testar Minimal APIs em seus projetos!