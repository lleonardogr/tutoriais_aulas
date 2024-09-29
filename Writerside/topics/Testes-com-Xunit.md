# Testes Unitários com xUnit.net

## Introdução

O **xUnit.net** é um dos frameworks de testes unitários mais populares para a plataforma .NET. Projetado para ser simples, extensível e moderno, ele facilita a escrita de testes automatizados para garantir a qualidade e a confiabilidade do código.

Este guia irá explorar o uso do xUnit.net, desde a configuração do ambiente até a criação e execução de testes. Abordaremos os conceitos fundamentais, as melhores práticas e forneceremos exemplos práticos para ajudá-lo a integrar testes unitários em seus projetos .NET.

---

## O que é o xUnit.net?

O **xUnit.net** é um framework de código aberto para testes unitários em .NET, desenvolvido por membros da comunidade e mantido por líderes da comunidade .NET, incluindo Brad Wilson e Jim Newkirk, que também estiveram envolvidos no desenvolvimento do NUnit e do MSTest.

### Características Principais

- **Leve e Extensível**: Projetado para ser fácil de usar e estender.
- **Suporte a .NET Core e .NET Framework**: Compatível com várias versões da plataforma .NET.
- **Integração com Ferramentas**: Funciona com várias ferramentas e IDEs, incluindo Visual Studio, Visual Studio Code e JetBrains Rider.
- **Paralelismo**: Suporte a execução paralela de testes para melhorar a performance.
- **Comunidade Ativa**: Mantido e atualizado regularmente com feedback da comunidade.

---

## Configurando o Ambiente

### Pré-requisitos

- **SDK do .NET**: Certifique-se de ter o .NET SDK instalado. Você pode baixá-lo em [https://dotnet.microsoft.com/download](https://dotnet.microsoft.com/download).
- **IDE**: Visual Studio, Visual Studio Code ou qualquer outro editor de sua preferência.

### Criando um Projeto de Testes com xUnit.net

#### Passo 1: Criar a Solução e o Projeto Principal

```bash
dotnet new sln -o ExemploXUnit
cd ExemploXUnit
dotnet new classlib -o CalculadoraApp
dotnet sln add CalculadoraApp/CalculadoraApp.csproj
```

#### Passo 2: Criar o Projeto de Testes

```bash
dotnet new xunit -o CalculadoraApp.Tests
dotnet sln add CalculadoraApp.Tests/CalculadoraApp.Tests.csproj
```

#### Passo 3: Adicionar Referência ao Projeto Principal

```bash
dotnet add CalculadoraApp.Tests/CalculadoraApp.Tests.csproj reference CalculadoraApp/CalculadoraApp.csproj
```

---

## Escrevendo Testes com xUnit.net

### Estrutura Básica de um Teste

No xUnit.net, os testes são métodos dentro de classes que podem ser detectadas pelo runner de testes. Para que um método seja reconhecido como um teste, ele deve ser marcado com o atributo `[Fact]` ou `[Theory]`.

### Exemplo: Testando uma Classe Calculadora

#### Passo 1: Implementar a Classe Calculadora

```C#
// CalculadoraApp/Calculadora.cs
namespace CalculadoraApp
{
    public class Calculadora
    {
        public int Somar(int a, int b)
        {
            return a + b;
        }

        public int Subtrair(int a, int b)
        {
            return a - b;
        }
    }
}
```

#### Passo 2: Escrever Testes para a Calculadora

```C#
// CalculadoraApp.Tests/CalculadoraTests.cs
using Xunit;
using CalculadoraApp;

namespace CalculadoraApp.Tests
{
    public class CalculadoraTests
    {
        [Fact]
        public void Somar_DoisNumerosRetornaResultadoCorreto()
        {
            // Arrange
            var calculadora = new Calculadora();

            // Act
            var resultado = calculadora.Somar(2, 3);

            // Assert
            Assert.Equal(5, resultado);
        }

        [Fact]
        public void Subtrair_DoisNumerosRetornaResultadoCorreto()
        {
            // Arrange
            var calculadora = new Calculadora();

            // Act
            var resultado = calculadora.Subtrair(5, 3);

            // Assert
            Assert.Equal(2, resultado);
        }
    }
}
```

---

## Executando os Testes

### Usando o CLI do .NET

```bash
dotnet test
```

**Saída Esperada**:

```
Test Run Successful.
Total tests: 2
     Passed: 2
```

### Usando o Visual Studio

- Abra a solução no Visual Studio.
- Vá para **Test > Run All Tests**.
- Os resultados serão exibidos na janela **Test Explorer**.

---

## Atributos de Teste

### `[Fact]`

- Indica um método de teste que não aceita dados de entrada.
- Usado para testes que sempre devem produzir o mesmo resultado.

**Exemplo**:

```C#
[Fact]
public void TesteSemParametros()
{
    // Teste aqui
}
```

### `[Theory]` e `[InlineData]`

- `[Theory]` é usado para testes parametrizados que podem ser executados várias vezes com diferentes dados de entrada.
- `[InlineData]` fornece os dados de entrada para o teste.

**Exemplo**:

```C#
[Theory]
[InlineData(2, 3, 5)]
[InlineData(-1, 4, 3)]
[InlineData(0, 0, 0)]
public void Somar_VariosNumerosRetornaResultadoCorreto(int a, int b, int esperado)
{
    // Arrange
    var calculadora = new Calculadora();

    // Act
    var resultado = calculadora.Somar(a, b);

    // Assert
    Assert.Equal(esperado, resultado);
}
```

---

## Assertivas (Asserts)

O xUnit.net fornece uma variedade de métodos de assertiva para verificar condições nos testes.

### Assert.Equal

Verifica se dois valores são iguais.

```C#
Assert.Equal(esperado, atual);
```

### Assert.NotEqual

Verifica se dois valores não são iguais.

```C#
Assert.NotEqual(naoEsperado, atual);
```

### Assert.True e Assert.False

Verifica se uma condição é verdadeira ou falsa.

```C#
Assert.True(condicao);
Assert.False(condicao);
```

### Assert.Throws

Verifica se uma exceção específica é lançada.

```C#
Assert.Throws<ArgumentException>(() => metodoQueLancaExcecao());
```

**Exemplo**:

```C#
[Fact]
public void Dividir_DivisaoPorZeroLancaExcecao()
{
    // Arrange
    var calculadora = new Calculadora();

    // Act & Assert
    Assert.Throws<DivideByZeroException>(() => calculadora.Dividir(10, 0));
}
```

---

## Organizando os Testes

### Classes de Teste

- Organize os testes em classes que correspondam às classes ou métodos sendo testados.
- Use nomes descritivos para facilitar a manutenção.

### Métodos de Teste

- Use o padrão **Arrange-Act-Assert**:
    - **Arrange**: Configurar o ambiente e os dados necessários.
    - **Act**: Executar o método ou ação a ser testada.
    - **Assert**: Verificar se o resultado é o esperado.

### Nomenclatura de Testes

- Use nomes claros que descrevam o que está sendo testado e o resultado esperado.

**Exemplo**:

```C#
public void NomeDoMetodo_CondiçãoEsperada_ResultaEmComportamentoEsperado()
```

---

## Configuração e Limpeza de Testes

O xUnit.net utiliza construtores e o método `Dispose` para gerenciar a configuração e limpeza dos recursos dos testes.

### Usando o Construtor e Dispose

```C#
public class CalculadoraFixture : IDisposable
{
    public Calculadora Calculadora { get; private set; }

    public CalculadoraFixture()
    {
        // Configuração
        Calculadora = new Calculadora();
    }

    public void Dispose()
    {
        // Limpeza
    }
}

public class CalculadoraTests : IClassFixture<CalculadoraFixture>
{
    private readonly CalculadoraFixture _fixture;

    public CalculadoraTests(CalculadoraFixture fixture)
    {
        _fixture = fixture;
    }

    [Fact]
    public void Somar_DoisNumerosRetornaResultadoCorreto()
    {
        var resultado = _fixture.Calculadora.Somar(2, 3);
        Assert.Equal(5, resultado);
    }
}
```

### Interfaces de Configuração

- **IClassFixture TFixture**: Compartilha uma instância de `TFixture` entre os testes da classe.
- **ICollectionFixture TFixture**: Compartilha uma instância de `TFixture` entre várias classes de teste.
- **[CollectionDefinition]**: Define uma coleção de classes de teste que compartilham um fixture comum.

---

## Executando Testes em Paralelo

Por padrão, o xUnit.net executa testes em paralelo para melhorar a performance.

### Controlando o Paralelismo

- Para desabilitar o paralelismo:

```C#
// AssemblyInfo.cs ou no arquivo de testes
[assembly: CollectionBehavior(DisableTestParallelization = true)]
```

- Para limitar o grau de paralelismo:

```C#
[assembly: CollectionBehavior(MaxParallelThreads = 4)]
```

### Considerações sobre Paralelismo

- Certifique-se de que os testes são independentes e não compartilham estado que possa causar condições de corrida.
- Use fixtures compartilhados com cuidado.

---

## Integração com Mocks

Para testar componentes isoladamente, frequentemente é necessário simular dependências. Bibliotecas como **Moq** facilitam a criação de objetos mock.

### Instalando o Moq

```bash
dotnet add package Moq
```

### Exemplo de Uso com Moq

```C#
using Moq;

public interface IEmailService
{
    void EnviarEmail(string destinatario, string mensagem);
}

public class Notificador
{
    private readonly IEmailService _emailService;

    public Notificador(IEmailService emailService)
    {
        _emailService = emailService;
    }

    public void Notificar(string mensagem)
    {
        _emailService.EnviarEmail("usuario@example.com", mensagem);
    }
}

public class NotificadorTests
{
    [Fact]
    public void Notificar_ChamaEnviarEmail()
    {
        // Arrange
        var mockEmailService = new Mock<IEmailService>();
        var notificador = new Notificador(mockEmailService.Object);

        // Act
        notificador.Notificar("Olá!");

        // Assert
        mockEmailService.Verify(es => es.EnviarEmail("usuario@example.com", "Olá!"), Times.Once);
    }
}
```

---

## Testando Código Assíncrono

O xUnit.net suporta testes assíncronos usando `async` e `await`.

### Exemplo de Teste Assíncrono

```C#
[Fact]
public async Task ObterDadosAsync_RetornaDadosCorretos()
{
    // Arrange
    var servico = new ServicoDados();

    // Act
    var dados = await servico.ObterDadosAsync();

    // Assert
    Assert.NotNull(dados);
    Assert.Equal("Dados de exemplo", dados);
}
```

---

## Testando Exceções e Comportamentos Específicos

### Verificando Exceções com Mensagem Específica

```C#
[Fact]
public void Metodo_LancaExcecaoComMensagem()
{
    var ex = Assert.Throws<ArgumentException>(() => metodoInvalido());
    Assert.Equal("Mensagem de erro esperada", ex.Message);
}
```

### Usando `Record.Exception`

Captura qualquer exceção lançada pelo código.

```C#
[Fact]
public void Metodo_NaoDeveLancarExcecao()
{
    var ex = Record.Exception(() => metodoQueNaoDeveFalhar());
    Assert.Null(ex);
}
```

---

## Integração Contínua e xUnit.net

### Gerando Relatórios de Cobertura

- Use ferramentas como **Coverlet** para gerar relatórios de cobertura de código.

```bash
dotnet add package coverlet.msbuild
dotnet test /p:CollectCoverage=true
```

### Integração com Pipelines

- Configure seus pipelines de CI/CD para executar testes automaticamente.
- Exemplo de configuração com GitHub Actions ou Azure DevOps.

---

## Dicas e Boas Práticas

- **Isolamento de Testes**: Cada teste deve ser independente dos outros.
- **Teste de Unidade vs. Teste de Integração**: Separe testes que verificam funcionalidades isoladas daqueles que dependem de recursos externos.
- **Nomenclatura Clara**: Use nomes descritivos para facilitar a compreensão do que o teste verifica.
- **Cobertura de Código**: Use ferramentas para monitorar a cobertura, mas foque na qualidade dos testes, não apenas na quantidade.
- **Refatoração**: Mantenha os testes atualizados e refatore-os junto com o código de produção.

---

## Recursos Adicionais

- **Documentação Oficial**: [https://xunit.net/](https://xunit.net/)
- **Repositório GitHub**: [https://github.com/xunit/xunit](https://github.com/xunit/xunit)
- **Livro**: *The Art of Unit Testing* - Roy Osherove
- **Comunidades**: Participar de fóruns como Stack Overflow e comunidades no GitHub para trocar experiências.

---

## Exercícios Práticos

1. **Criar Testes para uma API REST**:
    - Implemente testes que verificam a lógica de uma API REST simulando dependências com mocks.

2. **Testar Comportamentos com Dados Dinâmicos**:
    - Use `[Theory]` e `[MemberData]` para fornecer conjuntos de dados complexos aos testes.

3. **Integrar com um Banco de Dados em Memória**:
    - Configure testes que utilizam um banco de dados em memória (como o SQLite in-memory) para testar interações com o banco sem afetar o ambiente de produção.

---

## Conclusão

O xUnit.net é uma ferramenta poderosa e flexível para a escrita de testes unitários em projetos .NET. Com uma sintaxe clara e recursos avançados, ele facilita a incorporação de práticas de testes no fluxo de desenvolvimento, contribuindo para a criação de software de alta qualidade.

Este guia buscou fornecer uma visão abrangente sobre o xUnit.net, desde os conceitos básicos até as técnicas avançadas. Com a prática e o aprofundamento contínuo, você estará apto a aproveitar todo o potencial desta ferramenta em seus projetos.

---

## Observações Finais

- **Prática Constante**: Aprofunde seus conhecimentos escrevendo testes regularmente.
- **Atualização Contínua**: Mantenha-se atualizado com as novas versões e recursos do xUnit.net.
- **Comunicação**: Compartilhe conhecimento com sua equipe e incentive a cultura de testes.

Espero que este guia tenha sido útil e que você esteja mais confiante para implementar testes unitários com o xUnit.net em seus projetos!