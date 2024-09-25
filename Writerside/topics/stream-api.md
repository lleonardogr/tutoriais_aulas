# Stream API e Manipulação de Listas e Arrays

## Introdução

A **Stream API** introduzida no Java 8 revolucionou a forma como manipulamos coleções de dados. Ela permite processar conjuntos de dados de maneira declarativa, expressando o **"o quê"** ao invés do **"como"**. Isso resulta em um código mais conciso, legível e fácil de manter.

Neste guia, exploraremos como a Stream API funciona, como manipulá-la com **listas** e **arrays**, e forneceremos exemplos práticos para ilustrar seu uso.

---

## O que é a Stream API?

A **Stream API** é um conjunto de classes e interfaces que permite processar dados de coleções (como List, Set, Map) e arrays de forma funcional e paralela. Uma **Stream** representa uma sequência de elementos suportando operações sequenciais e paralelas.

### Características Principais

- **Declaratividade**: Permite descrever **o que** fazer, não **como** fazer.
- **Pipeline de Operações**: As Streams permitem encadear operações intermediárias e terminais.
- **Lazy Evaluation**: Operações intermediárias são avaliadas apenas quando necessário.
- **Possibilidade de Paralelismo**: Fácil conversão para processamento paralelo.

---

## Como Funcionam as Streams?

As Streams funcionam em três etapas principais:

1. **Fonte (Source)**: Origem dos dados, como coleções ou arrays.
2. **Operações Intermediárias**: Transformam a Stream em outra Stream (e.g., `filter`, `map`).
3. **Operação Terminal**: Finaliza o processamento e retorna um resultado (e.g., `collect`, `forEach`).

### Fluxo Básico

```java
Source -> Operações Intermediárias -> Operação Terminal
```

---

## Manipulação de Listas com Stream API

### Exemplo 1: Filtrar e Mapear uma Lista de Números

```java
import java.util.Arrays;
import java.util.List;
import java.util.stream.Collectors;

public class ExemploStream {
    public static void main(String[] args) {
        List<Integer> numeros = Arrays.asList(1, 2, 3, 4, 5, 6);

        List<Integer> numerosParesAoQuadrado = numeros.stream()
                .filter(n -> n % 2 == 0)           // Operação Intermediária: Filtrar números pares
                .map(n -> n * n)                   // Operação Intermediária: Elevar ao quadrado
                .collect(Collectors.toList());     // Operação Terminal: Coletar em uma lista

        System.out.println(numerosParesAoQuadrado); // Saída: [4, 16, 36]
    }
}
```

### Operações Comuns

- **filter(Predicate T  predicate)**: Seleciona elementos que correspondem a um predicado.
- **map(Function<T, R> mapper)**: Transforma cada elemento em outro.
- **collect(Collector<T, A, R> collector)**: Reúne os elementos finais em uma coleção ou outro tipo de resultado.

---

## Manipulação de Arrays com Stream API

### Exemplo 2: Processar um Array de Strings

```java
import java.util.Arrays;

public class ExemploStreamArray {
    public static void main(String[] args) {
        String[] nomes = {"Ana", "Bruno", "Carlos", "Amanda", "Beatriz"};

        Arrays.stream(nomes)
                .filter(nome -> nome.startsWith("A"))  // Filtrar nomes que começam com 'A'
                .sorted()                              // Ordenar
                .forEach(System.out::println);         // Imprimir cada nome

        // Saída:
        // Amanda
        // Ana
    }
}
```

### Criando Streams a partir de Arrays

- **Arrays.stream(array)**: Cria uma Stream a partir de um array.

---

## Operações Intermediárias em Detalhe

### filter()

Filtra elementos com base em um predicado.

```java
List<String> palavras = Arrays.asList("java", "stream", "api", "lambda");
palavras.stream()
        .filter(p -> p.length() > 4)
        .forEach(System.out::println);
// Saída:
// stream
// lambda
```

### map()

Transforma elementos em outros tipos ou formatos.

```java
List<String> palavras = Arrays.asList("java", "stream", "api");
palavras.stream()
        .map(String::toUpperCase)
        .forEach(System.out::println);
// Saída:
// JAVA
// STREAM
// API
```

### flatMap()

Desenvolve uma função que retorna uma Stream para cada elemento e as concatena.

```java
List<List<Integer>> listas = Arrays.asList(
        Arrays.asList(1, 2),
        Arrays.asList(3, 4),
        Arrays.asList(5, 6)
);

listas.stream()
        .flatMap(List::stream)
        .forEach(System.out::println);
// Saída:
// 1 2 3 4 5 6
```

### sorted()

Ordena os elementos.

```java
List<String> palavras = Arrays.asList("banana", "abacate", "laranja");
palavras.stream()
        .sorted()
        .forEach(System.out::println);
// Saída:
// abacate
// banana
// laranja
```

---

## Operações Terminais em Detalhe

### collect()

Coleta os elementos em uma coleção ou outro tipo de resultado.

```java
List<String> palavras = Arrays.asList("java", "stream", "api");
List<String> maiusculas = palavras.stream()
        .map(String::toUpperCase)
        .collect(Collectors.toList());
```

### forEach()

Executa uma ação para cada elemento.

```java
List<Integer> numeros = Arrays.asList(1, 2, 3);
numeros.stream()
        .forEach(n -> System.out.println(n));
// Saída:
// 1
// 2
// 3
```

### reduce()

Combina os elementos em um único resultado.

```java
List<Integer> numeros = Arrays.asList(1, 2, 3, 4);
int soma = numeros.stream()
        .reduce(0, Integer::sum);
System.out.println(soma); // Saída: 10
```

### anyMatch(), allMatch(), noneMatch()

Testam se algum, todos ou nenhum elemento correspondem a um predicado.

```java
List<String> palavras = Arrays.asList("java", "stream", "api");
boolean algumComecaComA = palavras.stream()
        .anyMatch(p -> p.startsWith("a"));
System.out.println(algumComecaComA); // Saída: false
```

---

## Processamento Paralelo com Streams

É possível paralelizar o processamento simplesmente chamando `parallelStream()` ao invés de `stream()`.

### Exemplo

```java
List<Integer> numeros = Arrays.asList(1, 2, 3, 4, 5, 6);

numeros.parallelStream()
        .map(n -> n * n)
        .forEach(System.out::println);
```

**Nota:** O processamento paralelo pode melhorar a performance em conjuntos de dados grandes, mas deve ser usado com cuidado devido a questões de thread safety.

---

## Exemplo Prático Completo

### Cenário: Processar uma Lista de Produtos

Vamos supor que temos uma lista de produtos e queremos aplicar descontos, filtrar produtos em promoção e coletar os nomes em uma lista.

```java
import java.util.Arrays;
import java.util.List;
import java.util.stream.Collectors;

public class Produto {
    private String nome;
    private double preco;
    private boolean promocao;

    // Construtor
    public Produto(String nome, double preco, boolean promocao) {
        this.nome = nome;
        this.preco = preco;
        this.promocao = promocao;
    }

    // Getters
    public String getNome() {
        return nome;
    }

    public double getPreco() {
        return preco;
    }

    public boolean isPromocao() {
        return promocao;
    }
}

public class ExemploProduto {
    public static void main(String[] args) {
        List<Produto> produtos = Arrays.asList(
                new Produto("Notebook", 3000.0, false),
                new Produto("Smartphone", 2000.0, true),
                new Produto("TV", 2500.0, true),
                new Produto("Mouse", 150.0, false)
        );

        List<String> nomesEmPromocao = produtos.stream()
                .filter(Produto::isPromocao)          // Filtrar produtos em promoção
                .map(Produto::getNome)                // Obter nomes dos produtos
                .collect(Collectors.toList());        // Coletar em uma lista

        System.out.println("Produtos em promoção: " + nomesEmPromocao);
        // Saída: Produtos em promoção: [Smartphone, TV]
    }
}
```

---

## Convertendo Arrays em Listas e vice-versa

### De Array para Lista

```java
String[] array = {"A", "B", "C"};
List<String> lista = Arrays.asList(array);
```

### De Lista para Array

```java
List<String> lista = Arrays.asList("A", "B", "C");
String[] array = lista.toArray(new String[0]);
```

### Usando Streams

```java
// De Array para Stream
String[] array = {"A", "B", "C"};
Stream<String> stream = Arrays.stream(array);

// De Lista para Stream
List<String> lista = Arrays.asList("A", "B", "C");
Stream<String> streamLista = lista.stream();
```

---

## Boas Práticas

- **Evitar Estados Mutáveis**: Evite modificar o estado de objetos dentro das operações de Stream.
- **Operações de Curto-Circuito**: Use `findFirst`, `findAny`, `anyMatch`, etc., para eficiência.
- **Limpar e Conciso**: Mantenha as expressões lambda simples e legíveis.
- **Evitar Processamento Paralelo Desnecessário**: Use `parallelStream` apenas quando apropriado.

---

## Comparação com Abordagem Imperativa

### Abordagem Imperativa

```java
List<String> nomes = Arrays.asList("Ana", "Bruno", "Carlos", "Amanda", "Beatriz");
List<String> nomesComA = new ArrayList<>();

for (String nome : nomes) {
    if (nome.startsWith("A")) {
        nomesComA.add(nome);
    }
}

Collections.sort(nomesComA);

for (String nome : nomesComA) {
    System.out.println(nome);
}
```

### Abordagem Declarativa com Streams

```java
nomes.stream()
     .filter(nome -> nome.startsWith("A"))
     .sorted()
     .forEach(System.out::println);
```

**Vantagens da Abordagem Declarativa**:

- Código mais conciso e legível.
- Facilita o paralelismo.
- Expressa a intenção sem detalhes de implementação.

---

## Exercícios Práticos

1. **Filtrar Números Ímpares e Multiplicar por 3**:

   Dada uma lista de números inteiros, filtre os números ímpares, multiplique por 3 e colecione em uma lista.

2. **Contar Ocorrências de Palavras**:

   Dada uma lista de palavras, conte quantas vezes cada palavra aparece usando Streams.

3. **Agrupar Pessoas por Idade**:

   Dada uma lista de objetos `Pessoa` com nome e idade, agrupe as pessoas por idade usando `Collectors.groupingBy`.

---

## Conclusão

A Stream API é uma ferramenta poderosa que permite processar coleções de dados de forma eficiente e expressiva. Compreender como utilizá-la para manipular listas e arrays é essencial para escrever código Java moderno e de alta qualidade.

---

## Referências

- **Java Documentation**: [Stream API](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html)
- **Oracle Tutorial**: [Processing Data with Java SE 8 Streams](https://www.oracle.com/technical-resources/articles/java/ma14-java-se-8-streams.html)
- **Livro**: *Java 8 in Action* - Raoul-Gabriel Urma, Mario Fusco, Alan Mycroft

---

## Observações Finais

- Pratique escrevendo código com Streams para se familiarizar com as operações.
- Lembre-se de que nem todas as operações precisam ser feitas com Streams; escolha a abordagem que torna o código mais claro.
- Esteja atento ao desempenho e à legibilidade do código ao usar Streams.

Espero que este guia tenha esclarecido o funcionamento da Stream API em Java e como manipular listas e arrays de forma eficiente e elegante.