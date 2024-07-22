# Criando e Executando seu Primeiro Aplicativo Java

Neste tutorial, vamos aprender como criar e executar seu primeiro aplicativo Java usando o IntelliJ IDEA.

## Pré-requisitos

Antes de começar, certifique-se de ter o IntelliJ IDEA instalado em seu sistema. Se você ainda não o tem, você pode baixá-lo [aqui](https://www.jetbrains.com/idea/download/).

## Passo 1: Criando um Novo Projeto

1. Abra o IntelliJ IDEA e clique em "Create New Project" na tela inicial.
2. Selecione "Java" na lista de opções à esquerda e clique em "Next".
3. Escolha um local para o seu projeto (ex: `HelloWorld`) e defina um nome para ele. Clique em "Finish" para criar o projeto.

![new_java_project.png](new_java_project.png)

### Estrutura de Pasta de um Novo Projeto IntelliJ Java
Ao criar um novo projeto Java no IntelliJ IDEA, a estrutura de pasta padrão é criada automaticamente. Aqui está uma explicação do propósito de cada pasta:

- **src**: Esta pasta é onde você deve colocar todos os arquivos de código-fonte do seu projeto Java. Por padrão, ela contém uma pasta chamada "main" e uma pasta chamada "test". A pasta "main" é onde você coloca o código principal do seu aplicativo, enquanto a pasta "test" é onde você coloca os testes unitários.

- **out**: Esta pasta é onde os arquivos compilados do seu projeto são armazenados. Ela contém uma pasta chamada "production" que contém os arquivos compilados do código principal do seu aplicativo e uma pasta chamada "test" que contém os arquivos compilados dos testes unitários.

- **.idea**: Esta pasta contém as configurações do projeto do IntelliJ IDEA. Ela inclui arquivos como o arquivo de configuração do projeto (.iml), o arquivo de configuração do módulo (.xml) e outros arquivos de configuração específicos do IntelliJ IDEA.

- **.git**: Esta pasta é usada pelo sistema de controle de versão Git para armazenar os arquivos relacionados ao controle de versão do seu projeto. Ela inclui arquivos como o diretório de repositório (.git) e outros arquivos relacionados ao Git.

- **out**: Esta pasta é usada para armazenar arquivos de saída gerados pelo IntelliJ IDEA. Ela inclui arquivos como logs, relatórios de análise estática e outros arquivos de saída gerados durante o desenvolvimento do projeto.

- **.iml**: Este é o arquivo de configuração do módulo do IntelliJ IDEA. Ele contém informações sobre o módulo do projeto, como as dependências, as configurações de compilação e outras configurações específicas do módulo.

Essa é a estrutura de pasta básica de um novo projeto Java no IntelliJ IDEA. Você pode personalizar essa estrutura de acordo com as necessidades do seu projeto.

## Passo 2: A classe `main` em Java

A classe `main` é uma parte fundamental de qualquer aplicativo Java. É nela que o programa começa a ser executado.

No exemplo fornecido, a classe `HelloWorld` contém o método `main`. Esse método é o ponto de entrada do programa, onde a execução começa. Ele possui a seguinte assinatura:

Aqui está uma explicação de cada parte da assinatura do método main:

- public: É um modificador de acesso que indica que o método pode ser acessado de qualquer lugar.
- static: É um modificador que indica que o método pertence à classe em si, e não a uma instância específica da classe.
- void: É o tipo de retorno do método main. Nesse caso, o método não retorna nenhum valor.
- main: É o nome do método. Esse nome é fixo e é o ponto de entrada padrão para a execução do programa.
- String[] args: É o parâmetro do método main. Ele permite que você passe argumentos para o programa quando ele é executado a partir da linha de comando. Neste exemplo, não estamos usando os argumentos, mas você pode acessá-los dentro do método se precisar.

## Passo 3: Rodar o projeto

Para executar o código no IntelliJ IDEA, siga estas etapas:

1. Certifique-se de que o projeto esteja aberto no IntelliJ IDEA.
2. Abra o arquivo que contém a classe main que você deseja executar.
3. Clique com o botão direito do mouse dentro do corpo do método main.
4. Selecione a opção “Run ‘meu_primeiro_projeto_java.main()’”. Isso iniciará a execução do programa.

![alt text](../images/run_hello_world.png)

Você também pode executar o código pressionando a combinação de teclas Shift + F10 ou clicando no botão “Run” na barra de ferramentas superior.

Após a execução, você verá a saída do programa no painel de saída do IntelliJ IDEA. No exemplo fornecido, a saída será “Hello World!”.

<seealso>
- [Documentação do IntelliJ IDEA](https://www.jetbrains.com/help/idea/)
- [Tutorial Java](https://www.jetbrains.com/help/idea/creating-and-running-your-first-java-application.html)
</seealso>
