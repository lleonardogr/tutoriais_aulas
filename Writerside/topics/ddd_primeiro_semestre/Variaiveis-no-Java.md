# Variáveis no Java

Este tutorial fornece uma visão geral de como as variáveis funcionam em Java, os tipos primitivos disponíveis e como armazená-los usando o Scanner.

## Antes de começar

Certifique-se de que você tenha os seguintes pré-requisitos:

- Primeiro pré-requisito
- Segundo pré-requisito

## Visão geral

Nesta parte do tutorial, você aprenderá sobre os tipos primitivos em Java e como declarar e inicializar variáveis. Os tipos primitivos incluem:

- `int`: para armazenar números inteiros.
- `double`: para armazenar números de ponto flutuante.
- `boolean`: para armazenar valores verdadeiro ou falso.
- `char`: para armazenar caracteres individuais.
- `String`: para armazenar sequências de caracteres.

## Armazenando variáveis usando o Scanner

O Scanner é uma classe em Java que permite ler entrada do usuário. Aqui está um exemplo de como usar o Scanner para armazenar valores em variáveis:

1. Importe a classe Scanner no início do seu código:

   ```java
   import java.util.Scanner;
   ```

2. Crie uma instância do Scanner para ler a entrada do usuário:

   ```java
   Scanner scanner = new Scanner(System.in);
   ```

3. Solicite ao usuário que insira um valor e armazene-o em uma variável:

   ```java
   System.out.print("Digite um número inteiro: ");
   int numero = scanner.nextInt();
   ```

4. Repita o processo para outros tipos de variáveis, como `double`, `boolean`, `char` e `String`.

## O que você aprendeu {id="o-que-aprendeu"}

Neste tutorial, você aprendeu sobre os tipos primitivos em Java, como declarar e inicializar variáveis e como armazenar valores usando o Scanner.

<seealso>
<!--Forneça alguns links relacionados a artigos de como fazer-->
</seealso>
