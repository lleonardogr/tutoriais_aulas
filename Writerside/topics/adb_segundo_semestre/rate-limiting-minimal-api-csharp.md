# Como Implementar Rate Limiting em Minimal APIs com C#

## Introdução

Rate limiting (limitação de taxa) é uma técnica essencial para proteger suas APIs contra sobrecarga, ataques de negação de serviço (DoS) e uso abusivo. Com o .NET 7 e versões superiores, implementar rate limiting em Minimal APIs tornou-se significativamente mais simples graças às novas funcionalidades integradas ao framework.

Este guia mostrará como implementar diferentes estratégias de rate limiting em suas Minimal APIs C#.

## Pré-requisitos

- .NET 7 ou superior
- Conhecimento básico de Minimal APIs

## O que é Rate Limiting?

Rate limiting restringe o número de requisições que um cliente pode fazer a uma API em um determinado período. Isso ajuda a:

- Prevenir ataques de negação de serviço
- Garantir uso justo dos recursos entre os clientes
- Manter a estabilidade do sistema sob alta carga
- Controlar custos operacionais

## Implementando Rate Limiting em Minimal APIs

### Passo 1: Instalar os pacotes necessários

Para o .NET 7 e superior, a funcionalidade de rate limiting já está integrada. Certifique-se de que seu projeto tenha as referências necessárias:

```bash
dotnet add package Microsoft.AspNetCore.RateLimiting
```

Para .NET 8+, você não precisa de um pacote adicional, pois a funcionalidade já está no framework.

### Passo 2: Configurar o Rate Limiting no Program.cs

```C#
using System.Threading.RateLimiting;
using Microsoft.AspNetCore.RateLimiting;

var builder = WebApplication.CreateBuilder(args);

// Configurando rate limiting
builder.Services.AddRateLimiter(options =>
{
    options.GlobalLimiter = PartitionedRateLimiter.Create<HttpContext, string>(httpContext =>
        RateLimitPartition.GetFixedWindowLimiter(
            partitionKey: httpContext.Connection.RemoteIpAddress?.ToString() ?? httpContext.Request.Headers.Host.ToString(),
            factory: partition => new FixedWindowRateLimiterOptions
            {
                AutoReplenishment = true,
                PermitLimit = 10,
                QueueLimit = 0,
                Window = TimeSpan.FromSeconds(60)
            }));
            
    options.OnRejected = async (context, token) =>
    {
        context.HttpContext.Response.StatusCode = StatusCodes.Status429TooManyRequests;
        context.HttpContext.Response.ContentType = "text/plain";
        await context.HttpContext.Response.WriteAsync("Muitas requisições. Tente novamente mais tarde.", token);
    };
});

var app = builder.Build();

// Habilitando o middleware de rate limiting
app.UseRateLimiter();

// Definindo rotas
app.MapGet("/", () => "Hello World!").WithName("Home");

app.Run();
```

## Estratégias de Rate Limiting

O .NET oferece várias estratégias de rate limiting que você pode implementar:

### 1. Fixed Window - Janela Fixa

A estratégia mostrada acima. Limita solicitações em um período fixo, como 10 requisições por minuto.

### 2. Sliding Window - Janela Deslizante

```C#
builder.Services.AddRateLimiter(options =>
{
    options.AddSlidingWindowLimiter("SlidingWindow", slidingOptions =>
    {
        slidingOptions.PermitLimit = 10;
        slidingOptions.Window = TimeSpan.FromSeconds(60);
        slidingOptions.SegmentsPerWindow = 6; // Dividindo a janela em 6 segmentos de 10 segundos
        slidingOptions.QueueLimit = 0;
        slidingOptions.AutoReplenishment = true;
    });
});

// Uso em uma rota específica
app.MapGet("/api/produtos", () => "Lista de produtos")
   .RequireRateLimiting("SlidingWindow");
```

### 3. Token Bucket - Balde de Tokens

```C#
builder.Services.AddRateLimiter(options =>
{
    options.AddTokenBucketLimiter("TokenBucket", tokenOptions =>
    {
        tokenOptions.TokenLimit = 10;
        tokenOptions.QueueLimit = 0;
        tokenOptions.ReplenishmentPeriod = TimeSpan.FromSeconds(60);
        tokenOptions.TokensPerPeriod = 10;
        tokenOptions.AutoReplenishment = true;
    });
});

// Uso em uma rota específica
app.MapGet("/api/usuarios", () => "Lista de usuários")
   .RequireRateLimiting("TokenBucket");
```

### 4. Concurrency - Concorrência

```C#
builder.Services.AddRateLimiter(options =>
{
    options.AddConcurrencyLimiter("Concurrency", concurrencyOptions =>
    {
        concurrencyOptions.PermitLimit = 10;
        concurrencyOptions.QueueLimit = 5;
    });
});

// Uso em uma rota específica
app.MapGet("/api/relatorios", () => "Gerando relatório...")
   .RequireRateLimiting("Concurrency");
```

## Rate Limiting para Endpoints Específicos

Você pode aplicar diferentes políticas de rate limiting para endpoints específicos:

```C#
// Definição de políticas
builder.Services.AddRateLimiter(options =>
{
    // Política padrão
    options.GlobalLimiter = PartitionedRateLimiter.Create<HttpContext, string>(httpContext =>
        RateLimitPartition.GetFixedWindowLimiter(
            partitionKey: httpContext.Connection.RemoteIpAddress?.ToString() ?? httpContext.Request.Headers.Host.ToString(),
            factory: partition => new FixedWindowRateLimiterOptions
            {
                AutoReplenishment = true,
                PermitLimit = 10,
                QueueLimit = 0,
                Window = TimeSpan.FromSeconds(60)
            }));
            
    // Política mais restritiva para APIs sensíveis
    options.AddPolicy("ApiRigida", httpContext =>
        RateLimitPartition.GetFixedWindowLimiter(
            partitionKey: httpContext.Connection.RemoteIpAddress?.ToString() ?? httpContext.Request.Headers.Host.ToString(),
            factory: partition => new FixedWindowRateLimiterOptions
            {
                AutoReplenishment = true,
                PermitLimit = 5,
                QueueLimit = 0,
                Window = TimeSpan.FromMinutes(5)
            }));
            
    // Política mais generosa para APIs públicas
    options.AddPolicy("ApiGenerosa", httpContext =>
        RateLimitPartition.GetFixedWindowLimiter(
            partitionKey: httpContext.Connection.RemoteIpAddress?.ToString() ?? httpContext.Request.Headers.Host.ToString(),
            factory: partition => new FixedWindowRateLimiterOptions
            {
                AutoReplenishment = true,
                PermitLimit = 100,
                QueueLimit = 0,
                Window = TimeSpan.FromMinutes(1)
            }));
            
    options.OnRejected = async (context, token) =>
    {
        context.HttpContext.Response.StatusCode = StatusCodes.Status429TooManyRequests;
        context.HttpContext.Response.ContentType = "text/plain";
        await context.HttpContext.Response.WriteAsync("Muitas requisições. Tente novamente mais tarde.", token);
    };
});

// Aplicando as políticas
app.MapGet("/api/admin", () => "Área administrativa")
   .RequireRateLimiting("ApiRigida");

app.MapGet("/api/public", () => "Dados públicos")
   .RequireRateLimiting("ApiGenerosa");
```

## Rate Limiting Baseado em Usuário

É possível implementar rate limiting baseado no usuário autenticado:

```C#
builder.Services.AddRateLimiter(options =>
{
    options.AddPolicy("PorUsuario", httpContext =>
    {
        // Obtém o ID do usuário autenticado ou usa o IP como fallback
        var userId = httpContext.User.Identity?.IsAuthenticated == true
            ? httpContext.User.FindFirst(ClaimTypes.NameIdentifier)?.Value
            : httpContext.Connection.RemoteIpAddress?.ToString();
            
        return RateLimitPartition.GetFixedWindowLimiter(
            partitionKey: userId ?? "anônimo",
            factory: _ => new FixedWindowRateLimiterOptions
            {
                AutoReplenishment = true,
                PermitLimit = 20,
                QueueLimit = 0,
                Window = TimeSpan.FromMinutes(1)
            });
    });
});

app.MapGet("/api/perfil", () => "Perfil do usuário")
   .RequireRateLimiting("PorUsuario")
   .RequireAuthorization();
```

## Headers de Rate Limit

Para melhorar a experiência do cliente, é recomendável adicionar headers de rate limit nas respostas:

```C#
app.Use(async (context, next) =>
{
    await next();
    
    // Apenas se a resposta foi bem-sucedida
    if (context.Response.StatusCode < 400)
    {
        var endpoint = context.GetEndpoint();
        var rateLimiterPolicy = endpoint?.Metadata.GetMetadata<EnableRateLimitingAttribute>();
        
        if (rateLimiterPolicy != null)
        {
            context.Response.Headers["X-RateLimit-Limit"] = "10";
            context.Response.Headers["X-RateLimit-Remaining"] = "5"; // Este valor deveria ser calculado dinamicamente
            context.Response.Headers["X-RateLimit-Reset"] = DateTimeOffset.UtcNow.AddMinutes(1).ToUnixTimeSeconds().ToString();
        }
    }
});
```

## Exemplo Completo

Aqui está um exemplo completo de uma Minimal API com rate limiting:

```C#
using System.Security.Claims;
using System.Threading.RateLimiting;
using Microsoft.AspNetCore.RateLimiting;

var builder = WebApplication.CreateBuilder(args);

// Adicionando autenticação e autorização (opcional)
builder.Services.AddAuthentication().AddJwtBearer();
builder.Services.AddAuthorization();

// Configurando rate limiting
builder.Services.AddRateLimiter(options =>
{
    // Política global
    options.GlobalLimiter = PartitionedRateLimiter.Create<HttpContext, string>(httpContext =>
        RateLimitPartition.GetFixedWindowLimiter(
            partitionKey: httpContext.Connection.RemoteIpAddress?.ToString() ?? httpContext.Request.Headers.Host.ToString(),
            factory: partition => new FixedWindowRateLimiterOptions
            {
                AutoReplenishment = true,
                PermitLimit = 10,
                QueueLimit = 0,
                Window = TimeSpan.FromSeconds(60)
            }));
            
    // Política para API pública
    options.AddPolicy("ApiPublica", httpContext =>
        RateLimitPartition.GetFixedWindowLimiter(
            partitionKey: httpContext.Connection.RemoteIpAddress?.ToString() ?? httpContext.Request.Headers.Host.ToString(),
            factory: partition => new FixedWindowRateLimiterOptions
            {
                AutoReplenishment = true,
                PermitLimit = 100,
                QueueLimit = 0,
                Window = TimeSpan.FromMinutes(1)
            }));
            
    // Política para API autenticada baseada no usuário
    options.AddPolicy("ApiUsuario", httpContext =>
    {
        var userId = httpContext.User.Identity?.IsAuthenticated == true
            ? httpContext.User.FindFirst(ClaimTypes.NameIdentifier)?.Value
            : httpContext.Connection.RemoteIpAddress?.ToString();
            
        return RateLimitPartition.GetFixedWindowLimiter(
            partitionKey: userId ?? "anônimo",
            factory: _ => new FixedWindowRateLimiterOptions
            {
                AutoReplenishment = true,
                PermitLimit = 20,
                QueueLimit = 0,
                Window = TimeSpan.FromMinutes(1)
            });
    });
            
    // Configuração de resposta para requisições rejeitadas
    options.OnRejected = async (context, token) =>
    {
        context.HttpContext.Response.StatusCode = StatusCodes.Status429TooManyRequests;
        context.HttpContext.Response.ContentType = "application/json";
        
        var mensagem = new
        {
            mensagem = "Muitas requisições. Por favor, tente novamente após algum tempo.",
            limitResetEmSegundos = 60 // Este valor deveria ser calculado com base na política atual
        };
        
        await context.HttpContext.Response.WriteAsJsonAsync(mensagem, token);
    };
});

var app = builder.Build();

// Habilitando os middlewares necessários
app.UseAuthentication();
app.UseAuthorization();
app.UseRateLimiter();

// Definindo rotas
app.MapGet("/", () => "API com Rate Limiting").WithName("Home");

app.MapGet("/api/public", () => "Dados públicos")
   .RequireRateLimiting("ApiPublica")
   .WithName("PublicEndpoint");

app.MapGet("/api/perfil", (ClaimsPrincipal user) => $"Olá, {user.Identity?.Name}")
   .RequireRateLimiting("ApiUsuario")
   .RequireAuthorization()
   .WithName("UserProfile");

app.Run();
```

## Melhores Práticas

1. **Ajuste os limites cuidadosamente**: Comece com limites generosos e ajuste conforme necessário.
2. **Comunique claramente os limites**: Use headers para informar aos clientes sobre os limites.
3. **Forneça feedback útil**: Quando uma requisição for rejeitada, informe ao cliente quando ele poderá tentar novamente.
4. **Monitore o uso**: Registre quando os limites forem atingidos para identificar clientes problemáticos.
5. **Considere diferentes limites para diferentes endpoints**: Proteja mais os endpoints críticos.
6. **Implemente caching**: Reduz a necessidade de repetir requisições idênticas.

## Possíveis Problemas e Soluções

### Problema: Aplicações por trás de um balanceador de carga

Se sua aplicação estiver atrás de um balanceador de carga, todos os pedidos podem aparecer com o mesmo IP.

**Solução**: Use headers como `X-Forwarded-For` e configure seu balanceador para encaminhá-los:

```C#
builder.Services.AddRateLimiter(options =>
{
    options.GlobalLimiter = PartitionedRateLimiter.Create<HttpContext, string>(httpContext =>
    {
        // Tenta obter o IP real do cliente através do X-Forwarded-For
        var clientIp = httpContext.Request.Headers["X-Forwarded-For"].FirstOrDefault() ??
                      httpContext.Connection.RemoteIpAddress?.ToString() ??
                      "unknown";
                      
        return RateLimitPartition.GetFixedWindowLimiter(
            partitionKey: clientIp,
            factory: partition => new FixedWindowRateLimiterOptions
            {
                AutoReplenishment = true,
                PermitLimit = 10,
                QueueLimit = 0,
                Window = TimeSpan.FromSeconds(60)
            });
    });
});
```

### Problema: Diferentes requisitos para diferentes clientes

Você pode ter clientes com diferentes necessidades de acesso.

**Solução**: Implementar rate limiting baseado em chaves de API:

```C#
builder.Services.AddRateLimiter(options =>
{
    options.AddPolicy("PorChaveAPI", httpContext =>
    {
        // Obtém a chave de API do header
        var apiKey = httpContext.Request.Headers["X-API-Key"].ToString();
        
        // Determina o limite com base na chave
        var permitLimit = apiKey switch
        {
            "cliente-premium" => 1000,
            "cliente-basico" => 100,
            _ => 10 // Clientes sem chave ou desconhecidos
        };
        
        return RateLimitPartition.GetFixedWindowLimiter(
            partitionKey: string.IsNullOrEmpty(apiKey) ? "sem-chave" : apiKey,
            factory: _ => new FixedWindowRateLimiterOptions
            {
                AutoReplenishment = true,
                PermitLimit = permitLimit,
                QueueLimit = 0,
                Window = TimeSpan.FromMinutes(1)
            });
    });
});
```

## Conclusão

O rate limiting é uma técnica essencial para proteger suas APIs e garantir um serviço estável para todos os clientes. Com as Minimal APIs do .NET, implementar essa funcionalidade tornou-se mais simples e direta, oferecendo vários algoritmos e opções de configuração para atender às necessidades específicas do seu projeto.

A implementação adequada de rate limiting não apenas protege seus recursos, mas também melhora a experiência do usuário ao garantir tempos de resposta consistentes mesmo sob condições de carga elevada.

---

Espero que este guia ajude você a implementar rate limiting eficaz em suas aplicações Minimal API com C#!
