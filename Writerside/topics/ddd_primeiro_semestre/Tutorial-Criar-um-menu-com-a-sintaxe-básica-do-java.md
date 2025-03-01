# Tutorial: Criar um menu com a sintaxe básica do java

## Introdução

Criar um menu em aplicações console é uma tarefa comum e útil para interagir com o usuário. Em Java, podemos utilizar classes como `Scanner` para ler a entrada do usuário e `System.out.println` para exibir opções e mensagens. Este guia irá mostrar como implementar um menu básico em uma aplicação console em Java, usando recursos simples da linguagem.


## 1. Configurando o Ambiente

Antes de começar, certifique-se de que você tem o Java Development Kit (JDK) instalado em sua máquina. Você pode baixar o JDK a partir do site oficial da Oracle ou utilizar uma distribuição open-source como o OpenJDK.

Você pode usar qualquer editor de texto ou IDE (Ambiente de Desenvolvimento Integrado) para escrever seu código Java, como o Eclipse, IntelliJ IDEA ou mesmo um editor de texto simples como o Notepad++.

---

## 2. Criando o Menu Básico

Vamos passo a passo construir um menu simples que permite ao usuário escolher entre diferentes opções e executar ações correspondentes.

### 2.1. Importando o Scanner

A classe `Scanner` é utilizada para ler a entrada do usuário a partir do console. Precisamos importar essa classe do pacote `java.util`.

```java
import java.util.Scanner;

2.2. Exibindo as Opções do Menu

Utilizamos System.out.println para exibir mensagens no console. Vamos mostrar as opções disponíveis para o usuário.

System.out.println("=== Menu Principal ===");
System.out.println("1. Opção 1");
System.out.println("2. Opção 2");
System.out.println("3. Sair");
```

### 2.3. Lendo a Escolha do Usuário

Criamos uma instância de Scanner para ler a entrada do usuário e armazenamos a escolha em uma variável.

```Java
Scanner scanner = new Scanner(System.in);
System.out.print("Escolha uma opção: ");
int escolha = scanner.nextInt();
```

### 2.4. Implementando as Ações do Menu

Utilizamos estruturas de controle como switch ou if-else para executar diferentes ações com base na escolha do usuário.

```Java
switch (escolha) {
    case 1:
        System.out.println("Você escolheu a Opção 1.");
        // Código para a Opção 1
        break;
    case 2:
        System.out.println("Você escolheu a Opção 2.");
        // Código para a Opção 2
        break;
    case 3:
        System.out.println("Saindo do programa...");
        // Código para sair
        break;
    default:
        System.out.println("Opção inválida. Tente novamente.");
        break;
}
```

### 3. Exemplo Completo

Vamos juntar todos os componentes acima em um programa completo.

```Java
import java.util.Scanner;

public class MenuConsole {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        int escolha = 0;

        while (escolha != 3) {
            System.out.println("=== Menu Principal ===");
            System.out.println("1. Saudação");
            System.out.println("2. Mostrar Data e Hora");
            System.out.println("3. Sair");
            System.out.print("Escolha uma opção: ");

            if (scanner.hasNextInt()) {
                escolha = scanner.nextInt();

                switch (escolha) {
                    case 1:
                        System.out.println("Olá! Seja bem-vindo.");
                        break;
                    case 2:
                        System.out.println("Data e Hora atual: " + java.time.LocalDateTime.now());
                        break;
                    case 3:
                        System.out.println("Saindo do programa...");
                        break;
                    default:
                        System.out.println("Opção inválida. Tente novamente.");
                        break;
                }
            } else {
                System.out.println("Entrada inválida. Por favor, digite um número.");
                scanner.next(); // Limpa a entrada inválida
            }

            System.out.println(); // Linha em branco para separar as interações
        }

        scanner.close();
    }
}
```

Explicação:

	•	Loop While: O menu é exibido repetidamente até que o usuário escolha a opção de sair (neste caso, a opção 3).
	•	Validação de Entrada: Usamos scanner.hasNextInt() para verificar se a entrada é um número inteiro. Caso contrário, exibimos uma mensagem de erro e usamos scanner.next() para descartar a entrada inválida.
	•	Opções do Menu:
	•	Opção 1: Exibe uma saudação.
	•	Opção 2: Mostra a data e hora atuais usando java.time.LocalDateTime.
	•	Opção 3: Sai do programa.
	•	Opção Inválida: Qualquer outra entrada numérica exibe uma mensagem de erro.

## 4. Melhorias e Boas Práticas

### 4.1. Organizar o Código em Métodos

Para tornar o código mais organizado e legível, podemos separar as ações em métodos.

```Java
import java.util.Scanner;

public class MenuConsole {
    public static final int OPCAO_SAUDACAO = 1;
    public static final int OPCAO_DATA_HORA = 2;
    public static final int OPCAO_SAIR = 3;

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        int escolha = 0;

        while (escolha != OPCAO_SAIR) {
            exibirMenu();
            escolha = obterEscolha(scanner);
            executarAcao(escolha);
        }

        scanner.close();
    }

    public static void exibirMenu() {
        System.out.println("=== Menu Principal ===");
        System.out.println(OPCAO_SAUDACAO + ". Saudação");
        System.out.println(OPCAO_DATA_HORA + ". Mostrar Data e Hora");
        System.out.println(OPCAO_SAIR + ". Sair");
        System.out.print("Escolha uma opção: ");
    }

    public static int obterEscolha(Scanner scanner) {
        int escolha = 0;

        if (scanner.hasNextInt()) {
            escolha = scanner.nextInt();
        } else {
            System.out.println("Entrada inválida. Por favor, digite um número.");
            scanner.next(); // Limpa a entrada inválida
        }

        return escolha;
    }

    public static void executarAcao(int escolha) {
        switch (escolha) {
            case OPCAO_SAUDACAO:
                saudacao();
                break;
            case OPCAO_DATA_HORA:
                mostrarDataHora();
                break;
            case OPCAO_SAIR:
                System.out.println("Saindo do programa...");
                break;
            default:
                System.out.println("Opção inválida. Tente novamente.");
                break;
        }
        System.out.println();
    }

    public static void saudacao() {
        System.out.println("Olá! Seja bem-vindo.");
    }

    public static void mostrarDataHora() {
        System.out.println("Data e Hora atual: " + java.time.LocalDateTime.now());
    }
}
```

Benefícios:

	•	Legibilidade: Separar o código em métodos torna mais fácil entender o que cada parte do programa faz.
	•	Reutilização: Se precisar exibir o menu ou executar ações em outros lugares, você pode reutilizar esses métodos.
	•	Manutenção: Facilita a atualização e manutenção do código.

## 4.2. Tratamento de Exceções

Para um programa mais robusto, podemos adicionar tratamento de exceções para evitar que entradas inválidas interrompam a execução.

```Java
public static int obterEscolha(Scanner scanner) {
    int escolha = 0;

    try {
        escolha = scanner.nextInt();
    } catch (InputMismatchException e) {
        System.out.println("Entrada inválida. Por favor, digite um número.");
        scanner.next(); // Limpa a entrada inválida
    }

    return escolha;
}
```

Nota: No exemplo anterior, usamos scanner.hasNextInt() para verificar se a entrada é um número antes de chamar nextInt(), mas podemos usar um try-catch para capturar a exceção InputMismatchException.

## 5. Conclusão

Criar um menu básico em aplicações console em Java é uma tarefa simples que envolve a exibição de opções para o usuário, leitura da entrada através do Scanner e execução de ações com base na escolha. Com este guia, você aprendeu a:

	•	Importar e utilizar o Scanner para leitura de dados.
	•	Exibir mensagens e opções usando System.out.println.
	•	Ler e validar a escolha do usuário.
	•	Utilizar estruturas de controle como switch e if-else.
	•	Organizar o código em métodos para melhorar a legibilidade e manutenção.

Com essas técnicas, você pode expandir seu menu para incluir mais opções e funcionalidades, criando aplicações de console interativas e úteis.

Dicas Finais:

	•	Validação de Entrada: Sempre valide a entrada do usuário para evitar erros e comportamentos inesperados.
	•	Comentários: Comente seu código para facilitar o entendimento por outras pessoas (ou por você mesmo no futuro).
	•	Modularização: Organize seu código em métodos e classes quando apropriado para melhorar a estrutura do programa.
	•	Testes: Teste seu programa com diferentes entradas para garantir que funciona conforme esperado.
	•	Recursos Adicionais: Explore outras classes e bibliotecas Java para adicionar funcionalidades avançadas, como leitura de arquivos, manipulação de dados, etc.

