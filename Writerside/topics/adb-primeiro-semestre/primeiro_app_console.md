# Tutorial: Criando sua Primeira Aplicação Console em C#

Este tutorial irá guiá-lo na criação de uma aplicação console simples em C# usando o Visual Studio.

## Pré-requisitos

- **Visual Studio 2022**: Certifique-se de ter o Visual Studio 2022 instalado. Se você ainda não o instalou, siga o [guia de instalação](https://learn.microsoft.com/pt-br/visualstudio/install/install-visual-studio?view=vs-2022).

## Passos para Criar uma Aplicação Console

### Passo 1: Criar um Novo Projeto

1. **Abrir o Visual Studio**: Inicie o Visual Studio

![vs_new_project.png](vs_new_project.png)

2. **Criar um novo projeto**:
    - Na janela inicial, clique em **"Criar um novo projeto"**.
    - No campo de busca, digite **"Aplicativo de Console"** e selecione **"Aplicativo de Console C#"**.
    - Clique em **"Avançar"**.
    - obs: certifique-se de ter instalado as cargas de trabalho de .NET

![vs_console_new_app.png](vs_console_new_app.png)

3. **Configurar o novo projeto**:
   - Dê um nome ao projeto, por exemplo, **"Calculadora"**.
      ![vs_calculator_app.png](vs_calculator_app.png)
   - Escolha um local para salvar o projeto.
   - Clique em **"Avançar"**.
   - Na janela **"Informações adicionais"**, selecione **.NET 8.0 (Long-term support)** como o framework de destino.
   - Clique em **"Criar"**.
     ![net_version.png](net_version.png)



### Passo 2: Escrever o Código

1. **Abrir o arquivo Program.cs**:
    - No **Gerenciador de Soluções**, clique duas vezes em **Program.cs** para abrir o arquivo no editor de código.

2. **Substituir o código padrão**:
    - Apague o código existente e substitua pelo seguinte:

    ```C#
   using System;

    namespace Calculadora
    {
        class Program
        {
            static void Main(string[] args)
            {
                int a = 42;
                int b = 119;
                int c = a + b;
                Console.WriteLine(c);
                Console.ReadKey();
            }
        }
    }
    ```

### Passo 3: Executar a Aplicação

1. **Compilar e Executar**:
    - Para compilar e executar a aplicação, pressione **F5** ou clique na seta verde **"Iniciar"** na barra de ferramentas superior.
    - Uma janela de console abrirá mostrando o resultado da soma de 42 + 119, que é 161.

### Passo 4: Adicionar Funcionalidade de Calculadora

Para estender a funcionalidade e transformar a aplicação em uma calculadora interativa, substitua o código em **Program.cs** pelo seguinte:

```C#
using System;

namespace Calculadora
{
    class Program
    {
        static void Main(string[] args)
        {
            bool endApp = false;
            Console.WriteLine("Console Calculator in C#\r");
            Console.WriteLine("------------------------\n");

            while (!endApp)
            {
                string numInput1 = "";
                string numInput2 = "";
                double result = 0;

                Console.WriteLine("Type a number, and then press Enter");
                numInput1 = Console.ReadLine();

                double cleanNum1 = 0;
                while (!double.TryParse(numInput1, out cleanNum1))
                {
                    Console.Write("This is not valid input. Please enter an integer value: ");
                    numInput1 = Console.ReadLine();
                }

                Console.WriteLine("Type another number, and then press Enter");
                numInput2 = Console.ReadLine();

                double cleanNum2 = 0;
                while (!double.TryParse(numInput2, out cleanNum2))
                {
                    Console.Write("This is not valid input. Please enter an integer value: ");
                    numInput2 = Console.ReadLine();
                }

                Console.WriteLine("Choose an option from the following list:");
                Console.WriteLine("\ta - Add");
                Console.WriteLine("\ts - Subtract");
                Console.WriteLine("\tm - Multiply");
                Console.WriteLine("\td - Divide");
                Console.Write("Your option? ");

                string op = Console.ReadLine();

                try
                {
                    result = Calculator.DoOperation(cleanNum1, cleanNum2, op);
                    if (double.IsNaN(result))
                    {
                        Console.WriteLine("This operation will result in a mathematical error.\n");
                    }
                    else Console.WriteLine("Your result: {0:0.##}\n", result);
                }
                catch (Exception e)
                {
                    Console.WriteLine("Oh no! An exception occurred trying to do the math.\n - Details: " + e.Message);
                }

                Console.WriteLine("------------------------\n");

                Console.Write("Press 'n' and Enter to close the app, or press any other key and Enter to continue: ");
                if (Console.ReadLine() == "n") endApp = true;

                Console.WriteLine("\n");
            }
            return;
        }
    }

    class Calculator
    {
        public static double DoOperation(double num1, double num2, string op)
        {
            double result = double.NaN;

            switch (op)
            {
                case "a":
                    result = num1 + num2;
                    break;
                case "s":
                    result = num1 - num2;
                    break;
                case "m":
                    result = num1 * num2;
                    break;
                case "d":
                    if (num2 != 0)
                    {
                        result = num1 / num2;
                    }
                    break;
                default:
                    break;
            }
            return result;
        }
    }
}
```

<seealso>
   <category ref="wrs">
      <a href="https://learn.microsoft.com/pt-br/visualstudio/get-started/csharp/tutorial-console?view=vs-2022"/>
      <a href="https://learn.microsoft.com/pt-br/visualstudio/get-started/csharp/tutorial-console-part-2?view=vs-2022"/>   
   </category>
</seealso>