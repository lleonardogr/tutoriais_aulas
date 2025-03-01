# Guia Introdutório ao LINQ em C#

## Introdução

**LINQ** (Language Integrated Query) é uma poderosa ferramenta do C# que permite realizar consultas diretamente em coleções de dados, como arrays, listas e até mesmo fontes de dados externas, de maneira integrada à linguagem. Isso proporciona uma sintaxe consistente e legível para manipulação de dados, eliminando a necessidade de aprender linguagens de consulta específicas para cada tipo de fonte de dados.

---

## Estrutura de uma Consulta LINQ

Uma consulta LINQ típica é composta por três etapas principais:

1. **Obter a fonte de dados**: A fonte de dados deve implementar a interface `IEnumerable<T>` ou `IQueryable<T>`.
2. **Criar a consulta**: Definir a lógica da consulta utilizando expressões de consulta ou sintaxe de método.
3. **Executar a consulta**: A execução pode ser imediata ou adiada, dependendo de como a consulta é definida e utilizada.

---

## Exemplo Prático: Consultando uma Lista de Estudantes

Vamos ilustrar o uso do LINQ com um exemplo prático. Suponha que temos uma lista de estudantes, cada um com um nome, sobrenome, ID e uma lista de notas. Nosso objetivo é consultar essa lista para obter informações específicas.

### 1️⃣ Definindo a Classe `Student`

```C#
public class Student
{
    public string First { get; set; }
    public string Last { get; set; }
    public int ID { get; set; }
    public List<int> Scores { get; set; }
}
```

---

### 2️⃣ Criando a Fonte de Dados

```C#
List<Student> students = new List<Student>
{
    new Student {First="Svetlana", Last="Omelchenko", ID=111, Scores= new List<int> {97, 92, 81, 60}},
    new Student {First="Claire", Last="O'Donnell", ID=112, Scores= new List<int> {75, 84, 91, 39}},
    new Student {First="Sven", Last="Mortensen", ID=113, Scores= new List<int> {88, 94, 65, 91}}
};
```

---

### 3️⃣ Escrevendo Consultas LINQ

#### 🔹 Selecionar Estudantes com Nota Alta no Primeiro Teste

```C#
var highScores = from student in students
                 where student.Scores[0] > 90
                 select student;
```

#### 🔹 Ordenar Estudantes pelo Sobrenome

```C#
var sortedStudents = from student in students
                     orderby student.Last
                     select student;
```

#### 🔹 Selecionar Nomes Completos dos Estudantes

```C#
var fullNames = from student in students
                select $"{student.First} {student.Last}";
```

#### 🔹 Calcular a Média de Notas de Cada Estudante

```C#
var studentAverages = from student in students
                      select new
                      {
                          Name = $"{student.First} {student.Last}",
                          AverageScore = student.Scores.Average()
                      };
```

#### 🔹 Agrupar Estudantes pelo Sobrenome

```C#
var studentsByLastName = from student in students
                         group student by student.Last into studentGroup
                         select studentGroup;
```

---

### 4️⃣ Executando as Consultas

```C#
foreach (var student in highScores)
{
    Console.WriteLine($"{student.First} {student.Last}");
}
```

---

## Conclusão

O LINQ integra poderosas capacidades de consulta diretamente na linguagem C#, permitindo manipular e consultar dados de maneira eficiente e concisa. Com uma sintaxe clara e expressiva, o LINQ simplifica operações complexas de manipulação de dados, tornando o código mais legível e fácil de manter.

Para aprofundar seus conhecimentos e explorar mais funcionalidades do LINQ, consulte a [documentação oficial da Microsoft](https://learn.microsoft.com/pt-br/dotnet/csharp/linq/get-started/walkthrough-writing-queries-linq).