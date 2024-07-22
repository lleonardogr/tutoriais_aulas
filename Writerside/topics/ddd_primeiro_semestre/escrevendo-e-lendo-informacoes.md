# Escrevendo e lendo informações

Os tutoriais são artigos orientados para aprendizado que ajudam os usuários a passar por um processo e alcançar um resultado final. 
Comece com uma introdução: para quem é este tutorial e o que o leitor irá alcançar ao lê-lo.
Responda à pergunta: "Por que devo seguir este tutorial?"

Forneça um breve resumo do tutorial.
Neste tutorial, você aprenderá como usar `println`, `printf` e `Scanner` em Java.

## Antes de começar

Liste os pré-requisitos que são necessários ou recomendados.

Certifique-se de que:
- Você tem conhecimento básico da linguagem de programação Java.
- Você tem um ambiente de desenvolvimento Java configurado.

## Parte 1: Usando println

Nesta parte, você aprenderá como usar o método `println` para imprimir saída no console.

1. Abra seu IDE Java ou editor de texto.

2. Crie um novo arquivo de classe Java.

3. Dentro do método `main`, adicione o seguinte código:

    ```java
    public class HelloWorld {
         public static void main(String[] args) {
              System.out.println("Olá, Mundo!");
         }
    }
    ```

4. Salve o arquivo e compile-o.

5. Execute o programa e observe a saída no console.

## Parte 2: Usando printf

Nesta parte, você aprenderá como usar o método `printf` para formatar e imprimir saída.

1. Abra seu IDE Java ou editor de texto.

2. Crie um novo arquivo de classe Java.

3. Dentro do método `main`, adicione o seguinte código:

    ```java
    public class HelloWorld {
         public static void main(String[] args) {
              String nome = "João";
              int idade = 25;
              double altura = 1.75;

              System.out.printf("Nome: %s, Idade: %d, Altura: %.2f", nome, idade, altura);
         }
    }
    ```

4. Salve o arquivo e compile-o.

5. Execute o programa e observe a saída formatada no console.

## Parte 3: Usando Scanner

Nesta parte, você aprenderá como usar a classe `Scanner` para ler entrada do usuário.

1. Abra seu IDE Java ou editor de texto.

2. Crie um novo arquivo de classe Java.

3. Dentro do método `main`, adicione o seguinte código:

    ```java
    import java.util.Scanner;

    public class HelloWorld {
         public static void main(String[] args) {
              Scanner scanner = new Scanner(System.in);

              System.out.print("Digite seu nome: ");
              String nome = scanner.nextLine();

              System.out.print("Digite sua idade: ");
              int idade = scanner.nextInt();

              System.out.printf("Nome: %s, Idade: %d", nome, idade);
         }
    }
    ```

4. Salve o arquivo e compile-o.

5. Execute o programa e digite seu nome e idade quando solicitado. Observe a saída no console.

## O que você aprendeu {id="o-que-aprendeu"}

Neste tutorial, você aprendeu como usar `println`, `printf` e `Scanner` em Java para imprimir saída e ler entrada. Agora você tem o conhecimento básico para começar a construir programas Java mais complexos.

<seealso>
[Tutorial de escrita e leitura de informações em Java no Baeldung](https://www.baeldung.com/java-io)
- [Documentação do IntelliJ sobre escrita e leitura de informações em Java](https://www.jetbrains.com/help/idea/working-with-files.html)
</seealso>
