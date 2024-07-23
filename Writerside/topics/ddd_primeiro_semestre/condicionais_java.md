
# Condicionais em Java e Operadores Ternários

## Introdução

Este tutorial vai ensinar você a usar condicionais em Java, incluindo a estrutura if-else e o operador ternário. As condicionais permitem que seu programa execute diferentes partes do código com base em certas condições.

## Objetivos

- Compreender a estrutura if-else em Java
- Utilizar operadores ternários para simplificar expressões condicionais

## Pré-requisitos

- Conhecimento básico de sintaxe Java
- Um ambiente de desenvolvimento configurado (como IntelliJ IDEA)

## Estrutura if-else

### O que é uma condicional if-else?

A estrutura if-else permite que seu programa execute um bloco de código se uma condição específica for verdadeira e outro bloco de código se a condição for falsa.

### Sintaxe

```java
if (condição) {
    // código a ser executado se a condição for verdadeira
} else {
    // código a ser executado se a condição for falsa
}
```

### Exemplo

Vamos considerar um exemplo onde verificamos se uma pessoa é maior de idade:

```java
int idade = 18;

if (idade >= 18) {
    System.out.println("Você é maior de idade.");
} else {
    System.out.println("Você é menor de idade.");
}
```

### Exercício Prático

Tente criar um programa que verifique se um número é positivo, negativo ou zero.

## Operadores Ternários

### O que é um operador ternário?

O operador ternário é uma forma concisa de escrever uma expressão if-else. Ele usa o símbolo `?` para separar a condição da expressão a ser executada se a condição for verdadeira e `:` para separar a expressão a ser executada se a condição for falsa.

### Sintaxe {id="sintaxe_1"}

```java
variável = (condição) ? expressãoSeVerdadeira : expressãoSeFalsa;
```

### Exemplo {id="exemplo_1"}

Vamos reescrever o exemplo da maioridade usando um operador ternário:

```java
int idade = 18;
String mensagem = (idade >= 18) ? "Você é maior de idade." : "Você é menor de idade.";
System.out.println(mensagem);
```

### Exercício Prático {id="exerc-cio-pr-tico_1"}

Tente criar um programa que utilize o operador ternário para determinar se um número é par ou ímpar.

## Conclusão

Neste tutorial, aprendemos sobre condicionais em Java e como usar o operador ternário para simplificar expressões. Pratique criando suas próprias condicionais e utilizando operadores ternários para fortalecer sua compreensão.

<seealso>
<category ref="wrs">
    <a href="https://docs.oracle.com/javase/8/docs/api/">Documentação Oficial do Java</a>
    <a href="https://www.geeksforgeeks.org/decision-making-javaif-else-switch-break-continue-jump/">Tutorial de Estruturas de Controle em Java</a>
    <a href="https://www.codecademy.com/learn/learn-java">Curso de Java na Codecademy</a>
</category>
</seealso>
