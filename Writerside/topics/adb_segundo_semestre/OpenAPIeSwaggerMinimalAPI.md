# OpenAPI e Swagger com Minimal API

## 1. Introdução ao OpenAPI e Swagger:

**OpenAPI**: Um padrão de especificação para definir APIs RESTful, permitindo a descrição de endpoints, parâmetros, respostas e mais.

**Swagger**: Conjunto de ferramentas que implementa a especificação OpenAPI, oferecendo funcionalidades para documentação e testes de APIs.

## 2. Configuração do Swagger em uma Minimal API:

**Adicionar pacotes necessários**: Inclua os pacotes NuGet necessários para Swagger no seu projeto.

```bash
dotnet add package Swashbuckle.AspNetCore
```

**Configurar Swagger no Program.cs**: Adicione a configuração do Swagger no pipeline de serviços e no middleware.

```csharp
var builder = WebApplication.CreateBuilder(args);

// Adiciona serviços ao contêiner.
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();

var app = builder.Build();

// Configura o pipeline de requisições HTTP.
if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}

app.UseHttpsRedirection();
// Outros middlewares e endpoints

app.Run();

```

## Personalização da Documentação Swagger:

**Configurar opções do Swagger**: Personalize a documentação gerada adicionando informações sobre o título, descrição e versão da API.

```csharp
builder.Services.AddSwaggerGen(options =>
{
    options.SwaggerDoc("v1", new OpenApiInfo
    {
        Title = "Minha API",
        Version = "v1",
        Description = "Uma descrição detalhada da minha API",
        Contact = new OpenApiContact
        {
            Name = "Nome do projeto",
            Email = "contato@example.com"
        }
    });
});

```
**Configurar exemplos e modelos**: Forneça exemplos de requisições e respostas para melhorar a documentação.

```csharp
options.ExampleFilters();
```

## 4. Uso do Swagger UI:

**Acesso à UI**: Quando o Swagger UI é configurado, ele está disponível em http://localhost:<port>/swagger por padrão. Você pode visualizar e testar a API diretamente na interface web.

**Testar Endpoints**: Utilize a interface interativa para enviar solicitações aos endpoints da API e ver as respostas diretamente no navegador.

## 5. Gerar Documentação OpenAPI:

**Gerar e Exportar Especificação**: Swagger gera uma especificação OpenAPI que pode ser exportada em formatos como JSON ou YAML.
A URL para a especificação JSON geralmente é http://localhost:<port>/swagger/v1/swagger.json.

**Integrar com Ferramentas**: Use ferramentas como Swagger Editor ou SwaggerHub para editar e compartilhar a especificação OpenAPI.

## 6. Benefícios da Integração com OpenAPI e Swagger:

**Documentação Automática**: Geração automática de documentação baseada no código.

**Testes Interativos**: Capacidade de testar a API diretamente na UI do Swagger.
Facilidade de Integração: Integração com outras ferramentas e serviços baseados na especificação OpenAPI.
