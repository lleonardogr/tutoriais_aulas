# Implementando JWT em uma Minimal API com ASP.NET Core 

Este guia passo a passo irá ajudá-lo a implementar a autenticação JWT em uma Minimal API. 
Você aprenderá a adicionar uma chave secreta, configurar a autenticação e criar endpoints protegidos.

## **Passo 1: Criar um novo projeto de Minimal API**

Crie um novo projeto usando o comando:

```Bash
dotnet new web -o MinhaApiJwtStart typing here...
```

## **Passo 2: Adicionar o pacote de autenticação JWT**

Navegue até o diretório do projeto e adicione o pacote necessário:

```Bash
cd MinhaApiJwt
dotnet add package Microsoft.AspNetCore.Authentication.JwtBearer
```

## **Passo 3: Configurar a chave secreta no appsettings.json**

```JSON
{
  "Jwt": {
    "Key": "sua-chave-secreta-super-segura",
    "Issuer": "sua-issuer",
    "Audience": "sua-audience"
  }
}
```

## **Passo 4: Configurar os serviços de autenticação no Program.cs**

Edite o arquivo Program.cs para configurar a autenticação JWT:

```C#
using Microsoft.AspNetCore.Authentication.JwtBearer;
using Microsoft.IdentityModel.Tokens;
using System.Text;

var builder = WebApplication.CreateBuilder(args);

// Adiciona a autenticação JWT
builder.Services.AddAuthentication(options =>
{
    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
})
.AddJwtBearer(options =>
{
    var key = Encoding.UTF8.GetBytes(builder.Configuration["Jwt:Key"]);
    options.SaveToken = true;
    options.TokenValidationParameters = new TokenValidationParameters
    {
        ValidateIssuer = true,
        ValidateAudience = true,
        ValidateLifetime = true,
        ValidateIssuerSigningKey = true,
        ValidIssuer = builder.Configuration["Jwt:Issuer"],
        ValidAudience = builder.Configuration["Jwt:Audience"],
        IssuerSigningKey = new SymmetricSecurityKey(key)
    };
});

var app = builder.Build();

// Usa autenticação e autorização
app.UseAuthentication();
app.UseAuthorization();
```

Explicação das opções de configuração:

	•	Key (Chave): Esta é a chave secreta usada para assinar o token JWT. Deve ser uma string complexa e segura para evitar que seja facilmente descoberta.
	•	Issuer (Emissor): Representa o emissor do token. Geralmente é o nome ou URL da sua aplicação ou serviço.
	•	Audience (Audiência): Indica quem pode usar ou consumir o token. Pode ser o nome da sua aplicação, serviço ou clientes específicos.

Essas configurações são usadas tanto na geração quanto na validação dos tokens JWT.


## **Passo 5: Criar um endpoint para gerar o token JWT**

Ainda no Program.cs, adicione um endpoint POST que gera o token:

```C#
using System.IdentityModel.Tokens.Jwt;
using System.Security.Claims;

app.MapPost("/gerar-token", (IConfiguration config) =>
{
    // Normalmente, você validaria as credenciais do usuário aqui
    // Para simplificar, vamos assumir que o usuário é válido

    var key = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(config["Jwt:Key"]));
    var creds = new SigningCredentials(key, SecurityAlgorithms.HmacSha256);

    var claims = new[]
    {
        new Claim(JwtRegisteredClaimNames.Sub, "usuarioTeste"),
        new Claim(JwtRegisteredClaimNames.Jti, Guid.NewGuid().ToString())
    };

    var token = new JwtSecurityToken(
        issuer: config["Jwt:Issuer"],
        audience: config["Jwt:Audience"],
        claims: claims,
        expires: DateTime.UtcNow.AddHours(1),
        signingCredentials: creds);

    var tokenString = new JwtSecurityTokenHandler().WriteToken(token);

    return Results.Ok(new { token = tokenString });
});
```

## **Passo 6: Proteger endpoints com [Authorize] ou RequireAuthorization()**

Adicione um endpoint protegido:

```C#
using Microsoft.AspNetCore.Authorization;

app.MapGet("/dados-seguros", [Authorize] () =>
{
    return "Este é um dado protegido por JWT!";
});
```

```C#
using Microsoft.AspNetCore.Authorization;

app.MapGet("/dados-seguros", () =>
{
    return "Este é um dado protegido por JWT!";
}).RequireAuthorization();
```

## **Passo 7: Executar e testar a API**

1. Obtenha o token JWT:
Faça uma requisição POST para https://localhost:5001/gerar-token usando o Postman, Insomnia ou curl. Você receberá um token JWT.
2. Acesse o endpoint protegido:
Use o token recebido para fazer uma requisição GET para https://localhost:5001/dados-seguros, adicionando o header Authorization: Bearer {seu_token}.
Exemplo com curl:

```Bash
curl -H "Authorization: Bearer {seu_token}" https://localhost:5001/dados-seguros
```

## **Passo 8: Integrar o JWT ao Swagger**

Para testar endpoints protegidos via Swagger, é necessário configurar o Swagger para suportar autenticação JWT.

### 8.1 Adicionar pacotes necessários

Adicione os pacotes do Swagger ao projeto:

```Bash
dotnet add package Swashbuckle.AspNetCore
```

### 8.2 Configurar o Swagger no Program.cs

Adicione as seguintes linhas no Program.cs:

Após adicionar os serviços de autenticação:

```C#
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen(options =>
{
    // Configura o Swagger para usar o JWT
    options.AddSecurityDefinition("Bearer", new Microsoft.OpenApi.Models.OpenApiSecurityScheme
    {
        Name = "Authorization",
        Type = Microsoft.OpenApi.Models.SecuritySchemeType.Http,
        Scheme = "Bearer",
        BearerFormat = "JWT",
        In = Microsoft.OpenApi.Models.ParameterLocation.Header,
        Description = "Insira o token JWT no formato Bearer {seu token}"
    });
    options.AddSecurityRequirement(new Microsoft.OpenApi.Models.OpenApiSecurityRequirement
    {
        {
            new Microsoft.OpenApi.Models.OpenApiSecurityScheme
            {
                Reference = new Microsoft.OpenApi.Models.OpenApiReference
                {
                    Type = Microsoft.OpenApi.Models.ReferenceType.SecurityScheme,
                    Id = "Bearer"
                }
            },
            new string[]{}
        }
    });
});

if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}
```

1. Acesse o Swagger UI: Navegue até https://localhost:5001/swagger/index.html.
2. Obtenha o token JWT: Use o Swagger para fazer uma requisição POST em /gerar-token e copie o token recebido.
3. Autentique-se no Swagger: Clique no botão Authorize no Swagger UI, insira o token no campo Authorization no formato Bearer {seu_token} e clique em Authorize.
4. Acesse o endpoint protegido: Agora, você pode testar o endpoint /dados-seguros diretamente pelo Swagger UI.

Conclusão

Você configurou com sucesso a autenticação JWT em sua Minimal API. 
Agora, você tem um endpoint que gera tokens e um endpoint protegido que requer um token válido para acesso.

<seealso>
<category ref="wrs">

</category>
</seealso>