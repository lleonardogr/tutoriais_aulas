# C#: Sintaxe

## Introdução

O **C#** é uma linguagem de programação moderna, orientada a objetos e desenvolvida pela Microsoft como parte da plataforma .NET. Ao longo dos anos, C# evoluiu significativamente, incorporando novos recursos que aprimoram a produtividade dos desenvolvedores, melhoram a legibilidade do código e aumentam a eficiência das aplicações.

Este guia tem como objetivo apresentar a sintaxe do C# focando nos **recursos mais recentes** da linguagem (até C# 11, conforme o conhecimento disponível até outubro de 2023). Exploraremos desde conceitos básicos até as inovações que tornam o desenvolvimento em C# mais poderoso e expressivo.

## 1. Conceitos Básicos do C#

Antes de mergulharmos nos recursos mais recentes, é importante revisar rapidamente os conceitos básicos da sintaxe do C# para contextualizar as inovações.

### 1.1. Estrutura de um Programa C#

Um programa C# típico consiste em namespaces, classes, métodos e outras estruturas. Veja um exemplo básico:

```C#
using System;

namespace ExemploBasico
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("Olá, Mundo!");
        }
    }
}
```

### 1.2. Tipos de Dados

C# é uma linguagem tipada estaticamente, suportando tipos primitivos como int, double, bool, e tipos complexos como classes, structs e enums.

```C#
int idade = 30;
double salario = 2500.75;
bool ativo = true;
string nome = "João";
```

### 1.### 3. Classes e Objetos

```C#
public class Pessoa
{
    public string Nome { get; set; }
    public int Idade { get; set; }

    public void Apresentar()
    {
        Console.WriteLine($"Olá, meu nome é {Nome} e tenho {Idade} anos.");
    }
}

Pessoa pessoa = new Pessoa { Nome = "Maria", Idade = 25 };
pessoa.Apresentar();
```

## 2. Recursos Recentes do C#

A partir das versões mais recentes do C#, diversos recursos foram introduzidos para aprimorar a linguagem. A seguir, detalhamos os mais significativos até a versão C# 11.

### 2.1. Raw String Literals

Raw String Literals permitem a criação de strings multilineares e com caracteres especiais sem a necessidade de escape. Utilizam aspas triplas """.

Exemplo:

```C#
string json = """
{
    "nome": "João",
    "idade": 30,
    "cidade": "São Paulo"
}
""";

Console.WriteLine(json);
```

Benefícios:

	•	Melhora a legibilidade de strings complexas, como JSON, XML ou expressões regulares.
	•	Reduz a necessidade de caracteres de escape.

### 2.2. Required Members

O recurso Required Members permite que propriedades ou campos de uma classe sejam obrigatórios durante a inicialização do objeto, reforçando a integridade dos dados.

Exemplo:

```C#
public class Produto
{
    public required int Id { get; init; }
    public required string Nome { get; init; }
    public double Preco { get; init; }
}

var produto = new Produto
{
    Id = 1,
    Nome = "Laptop",
    Preco = 1500.00
};
```


Benefícios:

	•	Garante que certos membros sejam sempre inicializados.
	•	Evita erros de runtime relacionados a propriedades não inicializadas.

### 2.### 3. List Patterns

List Patterns facilitam a correspondência e desestruturação de coleções (como listas ou arrays) em expressões condicionais.

Exemplo:

```C#
int[] numeros = { 1, 2, 3, 4, 5 };

if (numeros is [1, 2, 3, ..])
{
    Console.WriteLine("A lista começa com 1, 2, ### 3.");
}
```

Benefícios:

	•	Simplifica a verificação de estruturas específicas em coleções.
	•	Torna o código mais declarativo e legível.

### 2.4. Generic Math

O Generic Math introduz interfaces que permitem a criação de métodos matemáticos genéricos, facilitando operações com diferentes tipos numéricos.

Exemplo:

```C#
public T Adicionar<T>(T a, T b) where T : INumber<T>
{
    return a + b;
}

int somaInt = Adicionar(5, 10);
double somaDouble = Adicionar(5.5, 10.2);
```

Benefícios:

	•	Aumenta a flexibilidade de métodos matemáticos.
	•	Reduz a duplicação de código para diferentes tipos numéricos.

### 2.5. File-Scoped Namespaces

File-Scoped Namespaces permitem declarar um namespace que se aplica a todo o arquivo, reduzindo a indentação e melhorando a legibilidade.

Exemplo:

```C#
namespace MeuProjeto;

public class MinhaClasse
{
    // Código da classe
}
```

Benefícios:

	•	Menos indentação, tornando o código mais limpo.
	•	Facilita a manutenção e leitura do código.

### 2.6. Global Using Directives

Global Using Directives permitem definir diretivas de using que se aplicam a todos os arquivos do projeto, evitando repetições.

Exemplo:

```C#
// Arquivo: GlobalUsings.cs
global using System;
global using System.Collections.Generic;
global using MeuProjeto.Models;
```

Benefícios:

	•	Reduz a redundância de using em múltiplos arquivos.
	•	Simplifica a configuração de namespaces globais.

### 2.7. Static Abstract Members in Interfaces

Este recurso permite que interfaces contenham membros static abstract, possibilitando que tipos implementem métodos estáticos de forma abstrata.

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

public T ExecutarAdicionar<T>(T a, T b) where T : IOperacao<T>
{
    return T.Adicionar(a, b);
}

int somaInt = ExecutarAdicionar(5, 10);            // Resultado: 15
double somaDouble = ExecutarAdicionar(5.5, 10.2);  // Resultado: 15.7

Console.WriteLine($"Soma Int: {somaInt}");
Console.WriteLine($"Soma Double: {somaDouble}");
```

Benefícios:

	•	Permite a definição de métodos estáticos genéricos nas interfaces.
	•	Facilita a implementação de operações estáticas específicas para tipos.

### 2.8. Improved Pattern Matching

As melhorias em Pattern Matching incluem novos tipos de padrões e expressões mais flexíveis.

Exemplo:

```C#
object valor = 10;

switch (valor)
{
    case int i when i > 0:
        Console.WriteLine("Número positivo.");
        break;
    case int i:
        Console.WriteLine("Número.");
        break;
    case string s:
        Console.WriteLine("Texto: " + s);
        break;
    default:
        Console.WriteLine("Outro tipo.");
        break;
}
```

Benefícios:

	•	Permite condições mais específicas durante a correspondência de padrões.
	•	Torna o switch mais poderoso e expressivo.


## 3. Exemplos Práticos {id="3-exemplos-pr-ticos_1"}

Para consolidar o entendimento dos recursos recentes, vejamos alguns exemplos práticos que demonstram como aplicá-los no desenvolvimento de aplicações C#.

### 3.1. Uso de Raw String Literals

```C#
Objetivo: Criar uma string JSON multilinear sem caracteres de escape.

string json = """
{
    "nome": "João",
    "idade": 30,
    "endereco": {
        "rua": "Rua das Flores",
        "numero": 123
    }
}
""";

Console.WriteLine(json);

// Saída:

// {
//    "nome": "João",
//    "idade": 30,
//    "endereco": {
//        "rua": "Rua das Flores",
//        "numero": 123
//    }
// }

```

### 3.2. Implementação de Required Members

Objetivo: Garantir que certas propriedades sejam inicializadas ao criar um objeto.

```C#
public class Cliente
{
    public required int Id { get; init; }
    public required string Nome { get; init; }
    public string Email { get; init; }
}

var cliente = new Cliente
{
    Id = 1,
    Nome = "Maria",
    Email = "maria@example.com"
};

//Tentativa sem inicializar membros obrigatórios:

var clienteInvalido = new Cliente
{
    Nome = "Pedro"
    // Falta a propriedade Id
};
```

Resultado: Erro de compilação, garantindo a inicialização correta.

### 3.3. List Patterns

Objetivo: Verificar se uma lista começa com determinados elementos.

```C#
int[] numeros = { 1, 2, 3, 4, 5 };

if (numeros is [1, 2, 3, ..])
{
    Console.WriteLine("A lista começa com 1, 2, ### 3.");
}
else
{
    Console.WriteLine("A lista não começa com 1, 2, ### 3.");
}

// Saída:

// A lista começa com 1, 2, ### 3.
```

### 3.4. Generic Math

Objetivo: Criar um método genérico que soma dois números de qualquer tipo numérico.

```C#
using System.Numerics;

public T Somar<T>(T a, T b) where T : INumber<T>
{
    return a + b;
}

int somaInt = Somar(5, 10);            // Resultado: 15
double somaDouble = Somar(5.5, 10.2);  // Resultado: 15.7

Console.WriteLine($"Soma Int: {somaInt}");
Console.WriteLine($"Soma Double: {somaDouble}");

// Saída:

// Soma Int: 15
// Soma Double: 15.7
```

### 3.5. File-Scoped Namespaces

Objetivo: Definir um namespace que se aplica a todo o arquivo sem indentação adicional.

```C#
namespace MeuProjeto.Models;

public class Produto
{
    public int Id { get; set; }
    public string Nome { get; set; }
}
```

### 3.6. Global Using Directives

Objetivo: Definir diretivas de using que se aplicam a todos os arquivos do projeto.

```C#
// Arquivo: GlobalUsings.cs
global using System;
global using System.Collections.Generic;
global using MeuProjeto.Models;

Uso em outros arquivos:

// Arquivo: Program.cs
public class Program
{
    public static void Main(string[] args)
    {
        Produto produto = new Produto { Id = 1, Nome = "Caneta" };
        Console.WriteLine($"Produto: {produto.Nome}");
    }
}
```

### 3.7. Static Abstract Members in Interfaces

Objetivo: Definir métodos estáticos abstratos em interfaces para operações genéricas.

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

public T ExecutarAdicionar<T>(T a, T b) where T : IOperacao<T>
{
    return T.Adicionar(a, b);
}

int somaInt = ExecutarAdicionar(5, 10);            // Resultado: 15
double somaDouble = ExecutarAdicionar(5.5, 10.2);  // Resultado: 15.7

Console.WriteLine($"Soma Int: {somaInt}");
Console.WriteLine($"Soma Double: {somaDouble}");

// Saída:

// Soma Int: 15
// Soma Double: 15.7
```

### 3.8. Improved Pattern Matching

Objetivo: Utilizar padrões avançados com condições adicionais.

```C#
object valor = 10;

switch (valor)
{
    case int i when i > 0:
        Console.WriteLine("Número positivo.");
        break;
    case int i:
        Console.WriteLine("Número.");
        break;
    case string s:
        Console.WriteLine("Texto: " + s);
        break;
    default:
        Console.WriteLine("Outro tipo.");
        break;
}

// Saída:

// Número positivo.
```

## 4. Comparação com Versões Anteriores

Vamos comparar alguns dos recursos recentes com como eles eram implementados em versões anteriores do C#, destacando as melhorias em termos de sintaxe e funcionalidade.

4.1. Inicialização de Objetos com required Members

Antes (C# 10 e anteriores):

Para garantir que certas propriedades sejam inicializadas, era comum usar construtores ou validações explícitas.

```C#
public class Cliente
{
    public int Id { get; set; }
    public string Nome { get; set; }
    public string Email { get; set; }

    public Cliente(int id, string nome)
    {
        Id = id;
        Nome = nome;
    }
}

var cliente = new Cliente(1, "Maria") { Email = "maria@example.com" };
```

Depois (C# 11):

Com required members, é possível definir propriedades obrigatórias diretamente na classe.

```C#
public class Cliente
{
    public required int Id { get; init; }
    public required string Nome { get; init; }
    public string Email { get; init; }
}

var cliente = new Cliente
{
    Id = 1,
    Nome = "Maria",
    Email = "maria@example.com"
};
```

Benefícios:

	•	Menos Código Boilerplate: Elimina a necessidade de construtores específicos.
	•	Maior Clareza: Define claramente quais membros são obrigatórios.

4.2. Uso de global using vs. using Tradicional

Antes (C# 10 e anteriores):

Cada arquivo precisava declarar seus próprios using.

```C#
using System;
using System.Collections.Generic;
using MeuProjeto.Models;

namespace MeuProjeto
{
    // Código
}
```

Depois (C# 11):

Definição global de using reduz redundâncias.

```C#
// Arquivo: GlobalUsings.cs
global using System;
global using System.Collections.Generic;
global using MeuProjeto.Models;

// Outros arquivos
namespace MeuProjeto
{
    // Código sem necessidade de repetir os using
}
```

Benefícios:

	•	Redução de Redundância: Menos declarações repetitivas em múltiplos arquivos.
	•	Melhoria na Legibilidade: Foco no código principal sem distrações de múltiplos using.

### 4.3. Raw String Literals vs. Strings Tradicionais

Antes (C# 10 e anteriores):

Uso intensivo de caracteres de escape para strings complexas.

```C#
string json = "{\n\t\"nome\": \"João\",\n\t\"idade\": 30\n}";
```
Depois (C# 11):

Uso de aspas triplas para strings multilineares.

```C#
string json = """
{
    "nome": "João",
    "idade": 30
}
""";
```

Benefícios:

	•	Melhor Legibilidade: Strings complexas são mais fáceis de ler e escrever.
	•	Menos Erros de Escape: Reduz a chance de erros ao lidar com caracteres especiais.

## 5. Boas Práticas

Ao utilizar os recursos mais recentes do C#, é fundamental seguir boas práticas para garantir código limpo, eficiente e sustentável.

### 5.1. Aproveitar os Recursos Modernos para Melhorar a Legibilidade

	•	Utilize Raw String Literals para strings complexas, como JSON ou XML, melhorando a legibilidade.
	•	Use File-Scoped Namespaces para reduzir a indentação e tornar o código mais limpo.

### 5.2. Garantir a Integridade dos Dados com Required Members

	•	Defina propriedades obrigatórias usando required para evitar estados inválidos nos objetos.

### 5.3. Maximizar a Flexibilidade com Generic Math

	•	Utilize Generic Math para criar métodos matemáticos reutilizáveis e genéricos, reduzindo a duplicação de código.

### 5.4. Centralizar Diretivas de Usando com Global Usings

	•	Defina global using para namespaces frequentemente utilizados, reduzindo a redundância e melhorando a organização do código.

### 5.5. Implementar Interfaces com Static Abstract Members

	•	Utilize static abstract members in interfaces para criar contratos que permitam operações estáticas genéricas, aumentando a flexibilidade dos componentes.

### 5.6. Simplificar a Correspondência de Padrões com List Patterns

	•	Utilize List Patterns para verificar estruturas específicas em coleções de forma concisa e legível.

## Conclusão

Os recursos mais recentes do C# oferecem ferramentas poderosas que aumentam a produtividade dos desenvolvedores e melhoram a qualidade do código. Ao compreender e aplicar esses recursos de forma adequada, você pode escrever aplicações mais eficientes, legíveis e robustas.

Este guia apresentou uma visão geral dos recursos mais recentes do C#, incluindo Raw String Literals, Required Members, List Patterns, Generic Math, File-Scoped Namespaces, Global Using Directives, Static Abstract Members in Interfaces e Improved Pattern Matching. Além disso, fornecemos exemplos práticos e comparações com versões anteriores para ilustrar as melhorias trazidas por esses recursos.

## Dicas Finais:

	•	Pratique Regularmente: A melhor maneira de dominar os novos recursos é aplicá-los em projetos reais ou exercícios.
	•	Mantenha-se Atualizado: O C# continua evoluindo, portanto, acompanhe as atualizações e novas versões da linguagem.
	•	Compartilhe Conhecimento: Troque experiências com a comunidade e participe de fóruns para aprender e ensinar sobre os novos recursos.

