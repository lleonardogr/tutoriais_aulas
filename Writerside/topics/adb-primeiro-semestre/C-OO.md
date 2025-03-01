# C#: OO

## Introdução

A **Programação Orientada a Objetos (POO)** é um paradigma de programação que utiliza "objetos" para modelar dados e funcionalidades. O **C#** é uma linguagem que suporta plenamente a POO, oferecendo recursos poderosos para criar aplicações robustas, escaláveis e de fácil manutenção.

Este guia atualizado abrange os conceitos fundamentais da POO em C#, bem como os recursos mais recentes da linguagem que aprimoram a programação orientada a objetos. Exploraremos desde os princípios básicos até as práticas avançadas, incluindo exemplos práticos para consolidar o entendimento.

## 1. Princípios Fundamentais da POO

### 1.1. Abstração

**Abstração** é o processo de ocultar detalhes complexos e expor apenas os essenciais. Em C#, isso é alcançado através de classes, interfaces e métodos abstratos.

**Exemplo:**

```C#
// Abstração através de uma classe abstrata
public abstract class Forma
{
    public abstract double CalcularArea();
}
```

1.## 2. Encapsulamento

Encapsulamento envolve esconder os dados internos de um objeto e expor apenas o necessário através de métodos públicos. Isso protege o estado interno do objeto de alterações externas indevidas.

Exemplo:

```C#
public class ContaBancaria
{
    private decimal saldo;

    public decimal Saldo
    {
        get { return saldo; }
        private set
        {
            if (value >= 0)
                saldo = value;
        }
    }

    public void Depositar(decimal valor)
    {
        if (valor > 0)
            Saldo += valor;
    }

    public void Sacar(decimal valor)
    {
        if (valor > 0 && Saldo >= valor)
            Saldo -= valor;
    }
}
```

1.3. Herança

Herança permite que uma classe (derivada) herde propriedades e métodos de outra classe (base). Isso promove a reutilização de código e a criação de hierarquias lógicas.

Exemplo:

```C#
public class Animal
{
    public void Respirar()
    {
        Console.WriteLine("Respirando...");
    }
}

public class Cachorro : Animal
{
    public void Latir()
    {
        Console.WriteLine("Latindo...");
    }
}

// Uso
Cachorro cachorro = new Cachorro();
cachorro.Respirar(); // Herança da classe Animal
cachorro.Latir();
```

1.4. Polimorfismo

Polimorfismo permite que objetos de diferentes classes sejam tratados de forma uniforme através de uma interface comum. Existem dois tipos principais: polimorfismo de tempo de compilação (sobrecarga) e de tempo de execução (substituição).

Exemplo de Polimorfismo de Substituição:

```C#
public abstract class Forma
{
    public abstract double CalcularArea();
}

public class Circulo : Forma
{
    public double Raio { get; set; }

    public override double CalcularArea()
    {
        return Math.PI * Raio * Raio;
    }
}

public class Retangulo : Forma
{
    public double Largura { get; set; }
    public double Altura { get; set; }

    public override double CalcularArea()
    {
        return Largura * Altura;
    }
}

// Uso
List<Forma> formas = new List<Forma>
{
    new Circulo { Raio = 5 },
    new Retangulo { Largura = 4, Altura = 6 }
};

foreach (var forma in formas)
{
    Console.WriteLine($"Área: {forma.CalcularArea()}");
}

```

## 2. Conceitos Avançados da POO em C#

## 2.1. Classes e Objetos

Em C#, classes são moldes para criar objetos. Elas contêm campos, propriedades, métodos e eventos.

Exemplo:

```C#

public class Carro
{
    // Propriedades
    public string Modelo { get; set; }
    public string Cor { get; set; }
    public int Ano { get; set; }

    // Método
    public void Acelerar()
    {
        Console.WriteLine($"{Modelo} está acelerando.");
    }
}

// Criação de objeto
Carro meuCarro = new Carro
{
    Modelo = "Toyota Corolla",
    Cor = "Prata",
    Ano = 2020
};

meuCarro.Acelerar();
```

## 2.2. Interfaces

Interfaces definem contratos que as classes podem implementar. Elas contêm apenas a assinatura dos métodos, propriedades, eventos ou indexadores.

Exemplo:
```C#
public interface IRepositorio<T>
{
    void Adicionar(T item);
    void Remover(T item);
    T ObterPorId(int id);
    IEnumerable<T> ObterTodos();
}

public class RepositorioEmMemoria<T> : IRepositorio<T>
{
    private List<T> itens = new List<T>();

    public void Adicionar(T item) => itens.Add(item);

    public void Remover(T item) => itens.Remove(item);

    public T ObterPorId(int id)
    {
        // Implementação simplificada
        return itens.ElementAtOrDefault(id);
    }

    public IEnumerable<T> ObterTodos() => itens;
}
```

### 2.3. Classes Abstratas

Classes Abstratas não podem ser instanciadas diretamente e podem conter métodos abstratos que devem ser implementados pelas classes derivadas.

Exemplo:
```C#
public abstract class Veiculo
{
    public string Marca { get; set; }

    public abstract void Mover();
}

public class Bicicleta : Veiculo
{
    public override void Mover()
    {
        Console.WriteLine("Pedalando a bicicleta.");
    }
}

public class Carro : Veiculo
{
    public override void Mover()
    {
        Console.WriteLine("Dirigindo o carro.");
    }
}

// Uso
List<Veiculo> veiculos = new List<Veiculo>
{
    new Bicicleta { Marca = "Caloi" },
    new Carro { Marca = "Ford" }
};

foreach (var veiculo in veiculos)
{
    Console.Write($"{veiculo.Marca} está ");
    veiculo.Mover();
}
```

### 2.4. Herança Múltipla com Interfaces

C# não suporta herança múltipla de classes, mas permite que uma classe implemente múltiplas interfaces, proporcionando flexibilidade.

Exemplo:
```C#
public interface IAquatico
{
    void Nadar();
}

public interface IAnimal
{
    void Comer();
}

public class Golfinho : IAquatico, IAnimal
{
    public void Nadar()
    {
        Console.WriteLine("Golfinho está nadando.");
    }

    public void Comer()
    {
        Console.WriteLine("Golfinho está comendo.");
    }
}

// Uso
Golfinho golfinho = new Golfinho();
golfinho.Nadar();
golfinho.Comer();
```

## 2.5. Sobrecarga e Substituição de Métodos

Sobrecarga permite que uma classe tenha múltiplos métodos com o mesmo nome, mas com diferentes parâmetros. Substituição permite que uma classe derivada forneça uma implementação específica para um método virtual ou abstrato da classe base.

Exemplo de Sobrecarga:
```C#
public class Calculadora
{
    public int Somar(int a, int b) => a + b;

    public double Somar(double a, double b) => a + b;

    public int Somar(int a, int b, int c) => a + b + c;
}

// Uso
Calculadora calc = new Calculadora();
Console.WriteLine(calc.Somar(2, 3));       // Saída: 5
Console.WriteLine(calc.Somar(## 2.5, 3.5));   // Saída: 6.0
Console.WriteLine(calc.Somar(1, 2, 3));    // Saída: 6
```

Exemplo de Substituição:

```C#

public class Animal
{
    public virtual void EmitirSom()
    {
        Console.WriteLine("Som genérico de animal.");
    }
}

public class Gato : Animal
{
    public override void EmitirSom()
    {
        Console.WriteLine("Miau!");
    }
}

public class Cachorro : Animal
{
    public override void EmitirSom()
    {
        Console.WriteLine("Au Au!");
    }
}

// Uso
List<Animal> animais = new List<Animal>
{
    new Gato(),
    new Cachorro()
};

foreach (var animal in animais)
{
    animal.EmitirSom();
}
```

## 3. Recursos Modernos do C# para POO

### 3.1. Record Types

Introduzidos no C# 9, Records são tipos de referência imutáveis que facilitam a criação de objetos de valor. Eles fornecem uma implementação automática de métodos como Equals, GetHashCode e ToString.

Exemplo:

```C#
public record Pessoa(string Nome, int Idade);

// Uso
Pessoa pessoa1 = new Pessoa("Ana", 28);
Pessoa pessoa2 = new Pessoa("Ana", 28);

Console.WriteLine(pessoa1); // Saída: Pessoa { Nome = Ana, Idade = 28 }
Console.WriteLine(pessoa1 == pessoa2); // Saída: True
```

Benefícios:

	•	Imutabilidade: Facilita a criação de objetos que não podem ser alterados após a criação.
	•	Concorrência: Reduz problemas relacionados a estados compartilhados.
	•	Simplificação: Menos código para definir propriedades e métodos padrão.

### 3.2. Init-Only Properties

Introduzidos no C# 9, Init-Only Properties permitem que as propriedades sejam definidas apenas durante a inicialização do objeto, promovendo a imutabilidade parcial.

Exemplo:

```C#
public class Produto
{
    public int Id { get; init; }
    public string Nome { get; init; }
    public decimal Preco { get; init; }
}

// Uso
Produto produto = new Produto
{
    Id = 1,
    Nome = "Notebook",
    Preco = 3500.00m
};

// Tentativa de modificação após a inicialização (compilação falha)
// produto.Preco = 3000.00m;
```

Benefícios:

	•	Imutabilidade Controlada: Permite modificações apenas no momento da criação.
	•	Segurança: Evita alterações acidentais no estado do objeto.

### 3.3. Pattern Matching

Pattern Matching aprimorou a forma de verificar e extrair dados de objetos. Combinado com switch expressions e patterns, torna o código mais conciso e expressivo.

Exemplo:

```C#
public void DescreverObjeto(object obj)
{
    switch (obj)
    {
        case int i when i > 0:
            Console.WriteLine("Número inteiro positivo.");
            break;
        case string s:
            Console.WriteLine($"Texto: {s}");
            break;
        case Pessoa p:
            Console.WriteLine($"Pessoa: {p.Nome}, {p.Idade} anos.");
            break;
        default:
            Console.WriteLine("Tipo desconhecido.");
            break;
    }
}

// Uso
DescreverObjeto(10); // Saída: Número inteiro positivo.
DescreverObjeto("Olá"); // Saída: Texto: Olá
DescreverObjeto(new Pessoa("Carlos", 35)); // Saída: Pessoa: Carlos, 35 anos.
DescreverObjeto(3.14); // Saída: Tipo desconhecido.
```

Benefícios:

	•	Legibilidade: Menos código boilerplate para verificações de tipo.
	•	Flexibilidade: Permite condições adicionais (guards) para correspondência de padrões.

### 3.4. Static Abstract Members in Interfaces

Introduzidos no C# 10, Static Abstract Members in Interfaces permitem que interfaces definam membros estáticos abstratos que devem ser implementados pelas classes que as implementam. Isso é particularmente útil para Generic Math e operações estáticas.

Exemplo:

```C#
public interface IOperacao<T>
{
    static abstract T Adicionar(T a, T b);
}

public class OperacaoInt : IOperacao<int>
{
    public static int Adicionar(int a, int b) => a + b;
}

public class OperacaoDouble : IOperacao<double>
{
    public static double Adicionar(double a, double b) => a + b;
}

public class Calculadora
{
    public T Somar<T>(T a, T b) where T : IOperacao<T>
    {
        return T.Adicionar(a, b);
    }
}

// Uso
Calculadora calc = new Calculadora();
int somaInt = calc.Somar(5, 10);          // Resultado: 15
double somaDouble = calc.Somar(5.5, 10.2); // Resultado: 15.7

Console.WriteLine($"Soma Int: {somaInt}");
Console.WriteLine($"Soma Double: {somaDouble}");
```

Benefícios:

	•	Genéricos Mais Poderosos: Permite operações estáticas em tipos genéricos.
	•	Flexibilidade: Facilita a implementação de métodos estáticos específicos para cada tipo.

### 3.5. Default Interface Methods

Também introduzidos no C# 8, Default Interface Methods permitem que interfaces contenham implementações padrão para seus métodos. Isso facilita a evolução de interfaces sem quebrar implementações existentes.

Exemplo:

```C#
public interface ILogger
{
    void Log(string mensagem);

    // Método com implementação padrão
    void LogErro(string mensagem)
    {
        Log($"ERRO: {mensagem}");
    }
}

public class ConsoleLogger : ILogger
{
    public void Log(string mensagem)
    {
        Console.WriteLine(mensagem);
    }
}

// Uso
ILogger logger = new ConsoleLogger();
logger.Log("Aplicação iniciada.");
logger.LogErro("Falha ao conectar ao banco de dados.");
```

Benefícios:

	•	Evolução de Interfaces: Adiciona novos métodos sem impactar implementações existentes.
	•	Reutilização de Código: Fornece implementações padrão que podem ser reutilizadas ou sobrescritas.

## 4. Comparação com Versões Anteriores

## 4.1. Record Types vs. Classes Tradicionais

Antes (C# 8 e anteriores):

Para criar objetos de valor imutáveis, era necessário implementar manualmente métodos como Equals, GetHashCode e definir propriedades somente leitura.

public class Pessoa
{
    public string Nome { get; }
    public int Idade { get; }

    public Pessoa(string nome, int idade)
    {
        Nome = nome;
        Idade = idade;
    }

    public override bool Equals(object obj)
    {
        if (obj is Pessoa outra)
            return Nome == outra.Nome && Idade == outra.Idade;
        return false;
    }

    public override int GetHashCode() => HashCode.Combine(Nome, Idade);

    public override string ToString() => $"Pessoa {{ Nome = {Nome}, Idade = {Idade} }}";
}

Depois (C# 9+):

Com Record Types, tudo isso é automatizado.

public record Pessoa(string Nome, int Idade);

// Uso
Pessoa pessoa1 = new Pessoa("Ana", 28);
Pessoa pessoa2 = new Pessoa("Ana", 28);

Console.WriteLine(pessoa1); // Saída: Pessoa { Nome = Ana, Idade = 28 }
Console.WriteLine(pessoa1 == pessoa2); // Saída: True

Benefícios:

	•	Menos Código Boilerplate: Reduz significativamente a quantidade de código necessário.
	•	Imutabilidade: Por padrão, os records são imutáveis, promovendo melhores práticas de programação.

## 4.2 Init-Only Properties vs. Propriedades Tradicionais

Antes (C# 8 e anteriores):

Para criar objetos parcialmente imutáveis, era comum definir setters privados ou usar construtores.

public class Produto
{
    public int Id { get; private set; }
    public string Nome { get; private set; }
    public decimal Preco { get; private set; }

    public Produto(int id, string nome, decimal preco)
    {
        Id = id;
        Nome = nome;
        Preco = preco;
    }
}

Depois (C# 9+):

Com Init-Only Properties, a imutabilidade pode ser facilmente aplicada durante a inicialização.

```C#
public class Produto
{
    public int Id { get; init; }
    public string Nome { get; init; }
    public decimal Preco { get; init; }
}

// Uso
Produto produto = new Produto
{
    Id = 1,
    Nome = "Notebook",
    Preco = 3500.00m
};

// Tentativa de modificação após a inicialização (compilação falha)
// produto.Preco = 3000.00m;
```

Benefícios:

	•	Facilidade de Uso: Simplifica a criação de objetos imutáveis.
	•	Flexibilidade: Permite a construção de objetos com propriedades definidas em diferentes locais do código.

### 6.3. Static Abstract Members in Interfaces vs. Métodos Estáticos Tradicionais

Antes (C# 10 e anteriores):

Para operações estáticas em interfaces, era necessário contornar a falta de suporte com classes auxiliares ou métodos de extensão.

```C#
public interface IOperacao
{
    // Não suportado: static T Somar(T a, T b);
}

public static class OperacaoExtensions
{
    public static int Somar(this IOperacao op, int a, int b) => a + b;
}
```
Depois (C# 10+):

Com Static Abstract Members in Interfaces, é possível definir métodos estáticos abstratos diretamente nas interfaces.

```C#
public interface IOperacao<T>
{
    static abstract T Somar(T a, T b);
}

public class OperacaoInt : IOperacao<int>
{
    public static int Somar(int a, int b) => a + b;
}

public class Calculadora
{
    public T Adicionar<T>(T a, T b) where T : IOperacao<T>
    {
        return T.Somar(a, b);
    }
}

// Uso
Calculadora calc = new Calculadora();
int resultado = calc.Adicionar<int>(5, 10);
Console.WriteLine($"Soma: {resultado}"); // Saída: Soma: 15
```

Benefícios:

	•	Type Safety: Garante que as classes implementem os métodos estáticos conforme o contrato.
	•	Generics Mais Poderosos: Permite operações estáticas em tipos genéricos de maneira segura.

## 7. Recursos Adicionais

## Conclusão

A Programação Orientada a Objetos em C# é amplamente suportada e continuamente aprimorada com recursos modernos que facilitam a criação de aplicações robustas e de fácil manutenção. Ao dominar os princípios fundamentais da POO e aproveitar os recursos mais recentes da linguagem, desenvolvedores podem escrever código mais limpo, eficiente e expressivo.

Este guia apresentou uma visão abrangente dos conceitos básicos e avançados da POO em C#, destacando como os recursos recentes da linguagem complementam e aprimoram a programação orientada a objetos. A aplicação consciente de princípios como SOLID e padrões de design garante que o código seja sustentável e escalável, atendendo às demandas crescentes do desenvolvimento de software moderno.

Dicas Finais:

	•	Pratique Regularmente: A melhor forma de internalizar os conceitos de POO é aplicá-los em projetos reais ou exercícios práticos.
	•	Mantenha-se Atualizado: C# continua a evoluir, portanto, acompanhe as novidades e atualizações da linguagem.
	•	Refatore o Código: Revise e melhore continuamente o código existente para aderir às melhores práticas de POO.
	•	Participe da Comunidade: Engaje-se com outros desenvolvedores para trocar experiências e aprender novas abordagens.

