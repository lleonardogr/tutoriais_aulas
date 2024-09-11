# Idempotência no .NET

## O que é Idempotência?

Idempotência refere-se à propriedade de que o resultado de uma operação não muda quando aplicada uma ou mais vezes. Em termos de desenvolvimento de software, um método idempotente é aquele que sempre retorna o mesmo resultado, independentemente de quantas vezes seja chamado.

### Exemplo:
- A requisição `GET /produto/3` sempre retornará o produto com ID 3, sendo, portanto, idempotente.

## Por que Idempotência é Importante?

A importância da idempotência se destaca em **arquiteturas distribuídas**. Nessas arquiteturas, a mesma requisição pode ser enviada várias vezes devido a falhas de rede, perdas de requisição ou problemas de coordenação entre serviços. Operações idempotentes garantem que o estado do sistema permaneça consistente, mesmo quando uma requisição é repetida.

### Exemplo de service:
- Imagine um serviço de pagamento. Se o cliente enviar várias confirmações de pagamento por falha de rede, um método de pagamento idempotente garantirá que o pagamento seja processado apenas uma vez, mesmo que a requisição seja enviada várias vezes.

## Implementando Idempotência no .NET

### Passos:

1. **Identificar Requisições Unicamente**: Cada requisição precisa ser única para saber quais já foram processadas.
    - Utilize um parâmetro exclusivo no cabeçalho da requisição (chave de idempotência).

2. **Salvar o Parâmetro**: Ao processar a requisição, salve esse parâmetro para identificar se ela já foi tratada.

3. **Verificação de Idempotência**: Verifique o parâmetro em cada nova requisição. Dependendo da lógica da aplicação, retorne a resposta já processada ou uma nova.

### Detalhes:
- **Cuidado com a Criação de Chaves**: Chaves duplicadas podem causar problemas, especialmente em transações críticas, como pagamentos.
- **Cache Distribuído**: Em APIs intensivas, o uso de cache distribuído para armazenar a chave pode ser a melhor escolha.
- **Expiração do Cache**: O tempo de vida do cache deve ser equilibrado, de forma a não criar sobrecarga nem expirar antes do necessário.

## Usando a Biblioteca `IdempotentAPI`

[IdempotentAPI](https://www.nuget.org/packages/IdempotentAPI/) é uma biblioteca open-source que facilita a implementação de APIs idempotentes no ASP.NET Core, lidando com operações HTTP como POST e PATCH.

### Implementação com `IdempotentAPI`:

1. **Instalar a Biblioteca**:

    ```bash
    PM> Install-Package IdempotentAPI -Version 2.1.0
    ```

2. **Registrar os Serviços** em `Program.cs`:

    ```C#
    builder.Services.AddIdempotentAPI();
    ```

3. **Adicionar o Cache Distribuído**:

    ```C#
    builder.Services.AddIdempotentMinimalAPI(new IdempotencyOptions());
    builder.Services.AddDistributedMemoryCache();
    builder.Services.AddIdempotentAPIUsingDistributedCache();
    ```

4. **Marcar Objetos de Resposta como [Serializable]**:

    ```C#
    [Serializable]
    public record CardAddOrUpdateModel()
    {
    public string? Name { get; init; }
    public string? Description { get; init; }
    public string? ImageUrl { get; init; }
    };
    ```

5. **Marcar o Controller como Idempotente** (opcional):

    ```C#
    [Idempotent(ExpireHours = 48)]
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase { }
    ```

6. **Marcar rotas em minimal API**:

    ```C#
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

### Testando a API:

1. Faça uma requisição sem a chave `IdempotencyKey` e você verá uma exceção.
2. Adicione a chave no cabeçalho, por exemplo `1234`, e execute a requisição.
3. Execute novamente com a mesma chave e veja que o resultado é o mesmo.

### Conclusão

Idempotência é essencial em arquiteturas distribuídas para manter a consistência do sistema. A biblioteca `IdempotentAPI` facilita a implementação desse padrão no ASP.NET Core, especialmente para métodos POST e PATCH, garantindo que requisições repetidas não causem resultados inesperados.

---

Referências:

- [GitHub do IdempotentAPI](https://github.com/leventozturk/IdempotentAPI)
- [NuGet Package](https://www.nuget.org/packages/IdempotentAPI/)