
# Sintaxe: Iteradores (loops)

Loops são uma estrutura fundamental em qualquer linguagem de programação, permitindo a execução repetida de um bloco de código enquanto uma condição específica for verdadeira. Em Java, temos vários tipos de loops: `while`, `do-while`, `for`, e mais recentemente, expressões lambdas para manipulações mais funcionais.

## 1. Loop `while`

O loop `while` continua executando um bloco de código enquanto a condição fornecida é verdadeira. 

### Estrutura Básica
```java
while (condição) {
    // Código a ser executado repetidamente
}
```

### Exemplo
```java
public class ExemploWhile {
    public static void main(String[] args) {
        int contador = 0;
        while (contador < 5) {
            System.out.println("Contador: " + contador);
            contador++;
        }
    }
}
```

### Explicação
1. **Declaração da Condição**: O loop começa verificando a condição `contador < 5`.
2. **Execução do Bloco de Código**: Se a condição for verdadeira, o bloco de código dentro do loop é executado.
3. **Atualização da Condição**: Após a execução, o contador é incrementado.
4. **Repetição**: O loop verifica novamente a condição e repete os passos até que a condição seja falsa.

## 2. Loop `do-while`

O loop `do-while` é similar ao `while`, mas garante que o bloco de código seja executado pelo menos uma vez antes de verificar a condição.

### Estrutura Básica {id="estrutura-b-sica_1"}
```java
do {
    // Código a ser executado repetidamente
} while (condição);
```

### Exemplo {id="exemplo_1"}
```java
public class ExemploDoWhile {
    public static void main(String[] args) {
        int contador = 0;
        do {
            System.out.println("Contador: " + contador);
            contador++;
        } while (contador < 5);
    }
}
```

### Explicação {id="explica-o_1"}
1. **Execução Inicial**: O bloco de código dentro do `do` é executado uma vez antes de qualquer verificação.
2. **Verificação da Condição**: Após a execução inicial, a condição `contador < 5` é verificada.
3. **Repetição**: Se a condição for verdadeira, o bloco de código é executado novamente.

## 3. Loop `for`

O loop `for` é frequentemente usado quando o número de iterações é conhecido. Ele combina inicialização, condição e atualização em uma única linha.

### Estrutura Básica {id="estrutura-b-sica_2"}
```java
for (inicialização; condição; atualização) {
    // Código a ser executado repetidamente
}
```

### Exemplo {id="exemplo_2"}
```java
public class ExemploFor {
    public static void main(String[] args) {
        for (int i = 0; i < 5; i++) {
            System.out.println("Contador: " + i);
        }
    }
}
```

### Explicação {id="explica-o_2"}
1. **Inicialização**: `int i = 0` inicializa a variável de controle.
2. **Condição**: `i < 5` é verificada antes de cada iteração.
3. **Atualização**: `i++` é executado após cada iteração.

## 4. Novidades: Expressões Lambdas

Com a introdução de expressões lambdas no Java 8, manipulamos coleções de forma mais funcional e declarativa.

### Estrutura Básica {id="estrutura-b-sica_3"}
```java
lista.forEach(elemento -> {
    // Código a ser executado para cada elemento
});
```

### Exemplo com Lambdas
```java
import java.util.Arrays;
import java.util.List;

public class ExemploLambda {
    public static void main(String[] args) {
        List<Integer> numeros = Arrays.asList(1, 2, 3, 4, 5);
        numeros.forEach(numero -> System.out.println("Número: " + numero));
    }
}
```

### Explicação {id="explica-o_3"}
1. **Criação da Lista**: `Arrays.asList(1, 2, 3, 4, 5)` cria uma lista de inteiros.
2. **Uso de `forEach`**: `numeros.forEach(numero -> ...)` itera sobre cada elemento da lista.
3. **Expressão Lambda**: `numero -> System.out.println("Número: " + numero)` é a função que será executada para cada elemento.

<seealso>
<category ref="wrs">
<a href="https://docs.oracle.com/javase/tutorial/java/nutsandbolts/while.html">Documentação Oficial do Java sobre Loops</a>
<a href="https://www.geeksforgeeks.org/loops-in-java/">Java Loops - GeeksforGeeks</a>
<a href="https://www.w3schools.com/java/java_while_loop.asp">Tutorial de Java - W3Schools</a>
<a href="https://docs.oracle.com/javase/tutorial/java/javaOO/lambdaexpressions.html">Expressões Lambdas em Java - Oracle</a>
<a href="https://www.baeldung.com/java-8-lambda-expressions-tips">Java 8 Lambdas - Baeldung</a>
</category>
</seealso>

## Conclusão

Loops são essenciais para automatizar tarefas repetitivas. Entender `while`, `do-while`, `for`, e expressões lambdas é fundamental para escrever código eficiente e elegante em Java. Experimente esses exemplos e adapte-os para suas necessidades!
