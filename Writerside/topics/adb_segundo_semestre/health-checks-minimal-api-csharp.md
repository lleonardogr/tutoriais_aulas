# Como Implementar Health Checks em Minimal APIs com C# para Oracle e APIs Externas

## Introdução

Health Checks (verificações de integridade) são um componente essencial para aplicações modernas, permitindo monitorar o estado de saúde da aplicação e suas dependências. Com o ASP.NET Core, é possível implementar health checks de forma simples e eficiente utilizando a família de pacotes `AspNetCore.HealthChecks`.

Neste guia, você aprenderá como configurar health checks em Minimal APIs para monitorar:
- A conexão com banco de dados Oracle
- A disponibilidade de uma API externa

Estas verificações são fundamentais para sistemas em produção, auxiliando na detecção precoce de problemas e permitindo estratégias de recuperação automática ou alertas.

## Pré-requisitos

- .NET 6.0 ou superior
- Conhecimento básico de Minimal APIs
- Acesso a um banco de dados Oracle (para teste local ou em desenvolvimento)
- Uma API externa para testar (pode ser um endpoint público ou um serviço de teste)

## Pacotes Necessários

Para implementar health checks completos, precisaremos instalar os seguintes pacotes NuGet:

```bash
# Pacote base para Health Checks
dotnet add package AspNetCore.HealthChecks.UI.Client

# Para monitorar banco de dados Oracle
dotnet add package AspNetCore.HealthChecks.Oracle

# Para monitorar APIs externas
dotnet add package AspNetCore.HealthChecks.Uris

# Opcional: Para UI de visualização
dotnet add package AspNetCore.HealthChecks.UI
dotnet add package AspNetCore.HealthChecks.UI.InMemory.Storage
```

## Configuração Básica de Health Checks

Vamos começar com uma configuração básica de health checks em uma Minimal API:

```C#
using Microsoft.AspNetCore.Diagnostics.HealthChecks;
using HealthChecks.UI.Client;

var builder = WebApplication.CreateBuilder(args);

// Adiciona o serviço de health checks
builder.Services.AddHealthChecks();

var app = builder.Build();

// Endpoint para verificação de saúde básica
app.MapHealthChecks("/health", new HealthCheckOptions
{
    ResponseWriter = UIResponseWriter.WriteHealthCheckUIResponse
});

app.MapGet("/", () => "Health Checks API");

app.Run();
```

Este é o esqueleto mais básico. O endpoint `/health` retornará um status 200 OK se tudo estiver funcionando corretamente.

## Monitorando Banco de Dados Oracle

Agora, vamos adicionar um health check específico para verificar a conexão com o banco de dados Oracle:

```C#
var connectionString = builder.Configuration.GetConnectionString("OracleConnection");

builder.Services.AddHealthChecks()
    .AddOracle(
        connectionString: connectionString,
        name: "oracle-database",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "db", "oracle", "sql" },
        timeout: TimeSpan.FromSeconds(10)
    );
```

Este código:
- Adiciona um health check para o Oracle Database
- Define um nome amigável para identificação
- Configura o status como "Degraded" (em vez de "Unhealthy") caso a verificação falhe
- Adiciona tags para categorização
- Define um timeout para evitar esperas muito longas

## Monitorando uma API Externa

Para monitorar uma API externa, vamos adicionar outro health check:

```C#
builder.Services.AddHealthChecks()
    .AddUrlGroup(
        uri: new Uri("https://api.exemplo.com/health"),
        name: "api-externa",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "api", "external" },
        timeout: TimeSpan.FromSeconds(5)
    )
    .AddUrlGroup(
        uri: new Uri("https://outra-api.exemplo.com/status"),
        name: "outra-api-externa",
        httpMethod: HttpMethod.Get,
        setup: options =>
        {
            options.UseTimeout(TimeSpan.FromSeconds(3));
            options.AllowCachingResponses(TimeSpan.FromMinutes(5));
        }
    );
```

Este código verifica duas APIs diferentes:
- A primeira usa configuração básica com tags e timeout
- A segunda mostra configurações mais avançadas com método HTTP personalizado e cache de respostas

## Configurando Verificações Personalizadas

Além dos checks predefinidos, você pode criar verificações personalizadas:

```C#
builder.Services.AddHealthChecks()
    .AddCheck("Serviço-Personalizado", () =>
    {
        // Lógica de verificação personalizada
        bool servicoFuncionando = true; // Substituir por lógica real
        
        if (servicoFuncionando)
            return HealthCheckResult.Healthy("O serviço está funcionando corretamente");
        else
            return HealthCheckResult.Unhealthy("O serviço não está respondendo");
    });
```

## Configuração Completa com Oracle e API Externa

Vamos juntar tudo em um exemplo completo:

```C#
using Microsoft.AspNetCore.Diagnostics.HealthChecks;
using Microsoft.Extensions.Diagnostics.HealthChecks;
using HealthChecks.UI.Client;

var builder = WebApplication.CreateBuilder(args);

// Obtém as configurações do appsettings.json
var connectionString = builder.Configuration.GetConnectionString("OracleConnection");
var apiExternaUrl = builder.Configuration["ServicosExternos:ApiPrincipal"];

// Registra os serviços de saúde
builder.Services.AddHealthChecks()
    // Verifica o Oracle Database
    .AddOracle(
        connectionString: connectionString,
        name: "banco-oracle",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "db", "oracle", "sql" },
        timeout: TimeSpan.FromSeconds(10)
    )
    // Verifica a API externa
    .AddUrlGroup(
        uri: new Uri(apiExternaUrl),
        name: "api-parceiro",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "api", "external" },
        timeout: TimeSpan.FromSeconds(5)
    )
    // Verificação personalizada
    .AddCheck("sistema-arquivos", () =>
    {
        try
        {
            // Verifica se um diretório crítico existe e está acessível
            var diretorioLogs = Path.Combine(Directory.GetCurrentDirectory(), "logs");
            if (!Directory.Exists(diretorioLogs))
                Directory.CreateDirectory(diretorioLogs);
                
            return HealthCheckResult.Healthy("Sistema de arquivos OK");
        }
        catch (Exception ex)
        {
            return HealthCheckResult.Unhealthy("Erro ao acessar sistema de arquivos", ex);
        }
    });

// Opcional: Adiciona a UI para visualização dos health checks
builder.Services.AddHealthChecksUI(options =>
{
    options.SetEvaluationTimeInSeconds(15);  // Define o intervalo de verificação
    options.MaximumHistoryEntriesPerEndpoint(50);  // Armazena 50 verificações históricas
    options.SetApiMaxActiveRequests(1);  // Limita verificações paralelas
})
.AddInMemoryStorage();  // Armazena resultados em memória

var app = builder.Build();

// Configurando endpoints de health checks
app.MapHealthChecks("/health", new HealthCheckOptions
{
    ResponseWriter = UIResponseWriter.WriteHealthCheckUIResponse
});

// Endpoint que filtra apenas checks de banco de dados
app.MapHealthChecks("/health/database", new HealthCheckOptions
{
    Predicate = (check) => check.Tags.Contains("db"),
    ResponseWriter = UIResponseWriter.WriteHealthCheckUIResponse
});

// Endpoint que filtra apenas checks de APIs externas
app.MapHealthChecks("/health/api", new HealthCheckOptions
{
    Predicate = (check) => check.Tags.Contains("api"),
    ResponseWriter = UIResponseWriter.WriteHealthCheckUIResponse
});

// Opcional: UI para visualização dos health checks
app.MapHealthChecksUI(options => 
{
    options.UIPath = "/health-ui";  // Caminho para acessar a UI
    options.AddCustomStylesheet("healthchecks.css");  // Estilo personalizado opcional
});

app.MapGet("/", () => "Aplicação com Health Checks");

app.Run();
```

## Adicionando Autenticação para Health Checks

Em produção, você provavelmente desejará proteger seus endpoints de health checks:

```C#
app.MapHealthChecks("/health/secure", new HealthCheckOptions
{
    ResponseWriter = UIResponseWriter.WriteHealthCheckUIResponse
}).RequireAuthorization("HealthCheckPolicy");

// Configura a política de autorização
builder.Services.AddAuthorization(options =>
{
    options.AddPolicy("HealthCheckPolicy", policy =>
        policy.RequireRole("Admin", "Monitoring"));
});
```

## Respondendo com Informações Detalhadas

Para APIs, é útil ter respostas personalizadas:

```C#
app.MapHealthChecks("/health/detalhado", new HealthCheckOptions
{
    ResultStatusCodes =
    {
        [HealthStatus.Healthy] = StatusCodes.Status200OK,
        [HealthStatus.Degraded] = StatusCodes.Status200OK,
        [HealthStatus.Unhealthy] = StatusCodes.Status503ServiceUnavailable
    },
    ResponseWriter = async (context, report) =>
    {
        context.Response.ContentType = "application/json";
        
        var result = new
        {
            status = report.Status.ToString(),
            totalDuration = report.TotalDuration.TotalMilliseconds,
            checks = report.Entries.Select(e => new
            {
                name = e.Key,
                status = e.Value.Status.ToString(),
                duration = e.Value.Duration.TotalMilliseconds,
                description = e.Value.Description,
                data = e.Value.Data
            }),
            timestamp = DateTime.Now
        };
        
        await context.Response.WriteAsJsonAsync(result);
    }
});
```

## Configurando o Appsettings.json

Para referência, aqui está um exemplo de como estruturar suas configurações:

```json
{
  "ConnectionStrings": {
    "OracleConnection": "User Id=usuario;Password=senha;Data Source=localhost:1521/XEPDB1;"
  },
  "ServicosExternos": {
    "ApiPrincipal": "https://api.exemplo.com/health",
    "ApiSecundaria": "https://outra-api.exemplo.com/status"
  },
  "HealthChecksUI": {
    "HealthChecks": [
      {
        "Name": "Aplicação Principal",
        "Uri": "https://minha-aplicacao.com/health"
      },
      {
        "Name": "Apenas Database",
        "Uri": "https://minha-aplicacao.com/health/database"
      }
    ],
    "EvaluationTimeInSeconds": 10,
    "MinimumSecondsBetweenFailureNotifications": 60
  }
}
```

## Monitorando Health Checks de Forma Externa

Para monitoramento externo, você pode:

1. Configurar ferramentas como Prometheus e Grafana:

```C#
// Adicione ao seu projeto:
// dotnet add package AspNetCore.HealthChecks.Prometheus.Metrics

builder.Services.AddHealthChecks()
    .AddOracle(connectionString)
    .AddUrlGroup(new Uri(apiExternaUrl))
    .ForwardToPrometheus(); // Expõe métricas no formato Prometheus
```

2. Integrar com sistemas de notificação:

```C#
// Adicione ao seu projeto:
// dotnet add package AspNetCore.HealthChecks.UI.Client.Net7
// dotnet add package AspNetCore.HealthChecks.UI.SendGrid.Notifications

builder.Services.AddHealthChecksUI()
    .AddSendGridNotifier(options =>
    {
        options.ApiKey = "sua-chave-sendgrid";
        options.SourceEmail = "saude@suaaplicacao.com";
        options.SourceName = "Monitor de Saúde";
        options.DestinationEmails = new[] { "admin@suaempresa.com" };
    });
```

## Melhores Práticas

1. **Use tags para categorizar checks**: Facilita o filtro em diferentes endpoints.
2. **Configure timeouts adequados**: Evite que uma verificação lenta impacte as demais.
3. **Diferencie entre Degraded e Unhealthy**: Use "Degraded" para problemas que não comprometem totalmente o sistema.
4. **Limitar informações sensíveis**: Em ambientes de produção, não exponha detalhes de conectividade ou credenciais.
5. **Configure verificações em camadas**: Tenha endpoints separados para diferentes níveis de verificação (rápido, completo, etc).
6. **Inclua descrições informativas**: Quando um check falha, a descrição deve ajudar no diagnóstico.
7. **Adicione authentication/authorization**: Proteja seus endpoints de health check em produção.

## Exemplo de Uso em Kubernetes/Docker

Health checks são particularmente úteis em ambientes orquestrados como Kubernetes:

```yaml
# Exemplo de configuração no Kubernetes
livenessProbe:
  httpGet:
    path: /health
    port: 80
  initialDelaySeconds: 30
  periodSeconds: 10
  
readinessProbe:
  httpGet:
    path: /health/ready
    port: 80
  initialDelaySeconds: 15
  periodSeconds: 5
```

Para isso, você pode configurar endpoints específicos:

```C#
// Health check simples para liveness (se a aplicação está rodando)
app.MapHealthChecks("/health", new HealthCheckOptions
{
    Predicate = _ => false, // Não executa nenhum check, apenas verifica se o app responde
    ResponseWriter = (context, report) => 
        Task.FromResult(context.Response.WriteAsync("Healthy"))
});

// Health check completo para readiness (se a aplicação está pronta para receber tráfego)
app.MapHealthChecks("/health/ready", new HealthCheckOptions
{
    ResponseWriter = UIResponseWriter.WriteHealthCheckUIResponse
});
```

## Conclusão

Health checks são uma parte crucial de qualquer aplicação em produção, permitindo monitorar proativamente a saúde de seus serviços e dependências. Com o ASP.NET Core e a família de pacotes AspNetCore.HealthChecks, você pode implementar verificações robustas para bancos de dados Oracle, APIs externas e outros componentes críticos de forma simples e flexível.

Utilizar essa abordagem não apenas melhora a observabilidade do seu sistema, mas também facilita a integração com ferramentas de monitoramento e orquestração, contribuindo para uma maior confiabilidade e disponibilidade de seus serviços.

---

Espero que este guia ajude você a implementar health checks eficazes em suas aplicações Minimal API com C#!
