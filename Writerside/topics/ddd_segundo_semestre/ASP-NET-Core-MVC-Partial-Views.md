# ASP.NET Core MVC: Partial Views

## Introdução

Em aplicações web, frequentemente encontramos a necessidade de reutilizar trechos de código HTML em diferentes partes do site. Para evitar a repetição de código e facilitar a manutenção, o **ASP.NET Core MVC** oferece o conceito de **Partial Views**. Elas permitem encapsular porções da interface do usuário em componentes reutilizáveis e modulares.

Este guia irá explicar o que são Partial Views, como e quando utilizá-las, e fornecer exemplos práticos de implementação em um projeto ASP.NET Core MVC.

---

## O que são Partial Views?

Uma **Partial View** é um arquivo de visualização (view) que pode ser renderizado dentro de outra view. Elas funcionam como componentes reutilizáveis que ajudam a manter o código organizado e promovem a separação de responsabilidades na camada de apresentação.

### Características das Partial Views

- **Reutilização de Código**: Evita duplicação de código em diferentes views.
- **Modularidade**: Permite dividir a interface em componentes menores e gerenciáveis.
- **Facilita a Manutenção**: Alterações em um componente refletem em todas as views que o utilizam.
- **Separação de Responsabilidades**: Cada Partial View pode se responsabilizar por uma parte específica da interface.

---

## Quando Utilizar Partial Views?

- **Elementos Comuns**: Cabeçalhos, rodapés, barras de navegação que aparecem em várias páginas.
- **Componentes Reutilizáveis**: Formulários, listas, tabelas ou qualquer componente que seja utilizado em múltiplos lugares.
- **Organização**: Para manter o código limpo e organizado, especialmente em views complexas.
- **Atualizações Dinâmicas**: Quando é necessário atualizar partes da página via AJAX sem recarregar toda a view.

---

## Criando e Utilizando Partial Views

### Passo 1: Criar a Partial View

Por convenção, os arquivos de Partial Views começam com um underscore (`_`). Isso ajuda a identificar que o arquivo é uma Partial View.

**Exemplo**: Criando uma Partial View para o menu de navegação.

```html
<!-- Views/Shared/_Menu.cshtml -->
<ul>
    <li><a href="/">Home</a></li>
    <li><a href="/Produtos">Produtos</a></li>
    <li><a href="/Sobre">Sobre</a></li>
    <li><a href="/Contato">Contato</a></li>
</ul>
```

### Passo 2: Incluir a Partial View na View Principal

Para renderizar a Partial View dentro de uma view, você pode utilizar os métodos `Partial` ou `RenderPartialAsync`.

**Usando @Html.Partial**

```html
<!-- Views/Shared/_Layout.cshtml -->
<body>
    <header>
        @Html.Partial("_Menu")
    </header>
    <div>
        @RenderBody()
    </div>
</body>
```

**Usando @await Html.RenderPartialAsync**

```html
<body>
    <header>
        @await Html.RenderPartialAsync("_Menu")
    </header>
    <div>
        @RenderBody()
    </div>
</body>
```

---

## Passando Dados para Partial Views

### Usando Modelos Fortemente Tipados

Você pode passar um modelo para a Partial View da mesma forma que faz com uma view comum.

**Exemplo**: Passando um objeto `Usuario` para a Partial View.

```csharp
// Modelo Usuario
public class Usuario
{
    public string Nome { get; set; }
    public bool IsAdmin { get; set; }
}
```

**Partial View**

```html
<!-- Views/Shared/_UsuarioInfo.cshtml -->
@model MeuProjeto.Models.Usuario

<div>
    <p>Bem-vindo, @Model.Nome!</p>
    @if (Model.IsAdmin)
    {
        <p><a href="/Admin">Área Administrativa</a></p>
    }
</div>
```

**View Principal**

```html
@model MeuProjeto.Models.ViewModels.HomeViewModel

<!-- Passando o modelo para a Partial View -->
@Html.Partial("_UsuarioInfo", Model.UsuarioAtual)
```

### Usando ViewData ou ViewBag

Outra forma de passar dados é através do `ViewData` ou `ViewBag`.

**Partial View**

```html
<!-- Views/Shared/_Aviso.cshtml -->
@{
    var mensagem = ViewBag.Mensagem as string;
}

@if (!string.IsNullOrEmpty(mensagem))
{
    <div class="alert alert-warning">
        @mensagem
    </div>
}
```

**View Principal**

```html
@{
    ViewBag.Mensagem = "Este é um aviso importante!";
}

<!-- Renderizando a Partial View -->
@Html.Partial("_Aviso")
```

---

## Usando Partial Views com AJAX

Partial Views são especialmente úteis quando combinadas com AJAX para atualizar partes da página sem recarregar toda a interface.

**Exemplo**: Atualizar uma lista de comentários em um post de blog.

### Passo 1: Criar a Partial View para a Lista de Comentários

```html
<!-- Views/Shared/_Comentarios.cshtml -->
@model IEnumerable<MeuProjeto.Models.Comentario>

<div id="comentarios">
    @foreach (var comentario in Model)
    {
        <div class="comentario">
            <p><strong>@comentario.Autor</strong> disse:</p>
            <p>@comentario.Mensagem</p>
        </div>
    }
</div>
```

### Passo 2: Criar a Action no Controller que Retorna a Partial View

```csharp
public class PostsController : Controller
{
    private readonly MeuDbContext _context;

    public PostsController(MeuDbContext context)
    {
        _context = context;
    }

    public IActionResult ObterComentarios(int postId)
    {
        var comentarios = _context.Comentarios
            .Where(c => c.PostId == postId)
            .OrderByDescending(c => c.Data)
            .ToList();

        return PartialView("_Comentarios", comentarios);
    }
}
```

### Passo 3: Chamar a Partial View via AJAX

```html
<!-- Views/Posts/Detalhes.cshtml -->
<div id="area-comentarios">
    <!-- Comentários serão carregados aqui -->
</div>

<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.5.1/jquery.min.js"></script>
<script>
    function carregarComentarios() {
        $.ajax({
            url: '@Url.Action("ObterComentarios", "Posts", new { postId = Model.PostId })',
            type: 'GET',
            success: function (result) {
                $('#area-comentarios').html(result);
            }
        });
    }

    // Carrega os comentários quando a página é carregada
    $(document).ready(function () {
        carregarComentarios();
    });
</script>
```

---

## Dicas e Boas Práticas

- **Organização de Arquivos**: Mantenha suas Partial Views na pasta **Views/Shared** ou em pastas específicas, facilitando a localização.
- **Nomenclatura**: Utilize nomes claros e, por convenção, inicie com underscore (`_NomeDaPartial.cshtml`).
- **Evite Lógica Complexa**: Mantenha a lógica de negócios no Controller ou em ViewModels, não na Partial View.
- **Reutilização**: Sempre que identificar código repetido em várias views, considere extrair esse código para uma Partial View.
- **Desempenho**: Use `RenderPartialAsync` para melhorar o desempenho em aplicações com alto tráfego.
- **View Components**: Para componentes mais complexos que requerem lógica adicional ou injeção de dependências, considere utilizar **View Components**.

---

## Comparação entre Partial Views e View Components

- **Partial Views**: Ideais para conteúdo estático ou que depende apenas de dados passados diretamente. Não suportam injeção de dependências.
- **View Components**: Mais poderosos, permitem injeção de dependências e lógica mais complexa. Funcionam de forma semelhante a controllers miniaturizados.

---

## Exemplo Completo: Criando um Layout com Partial Views

### Arquitetura do Projeto

- **Views**
    - **Shared**
        - `_Layout.cshtml`
        - `_Menu.cshtml`
        - `_Footer.cshtml`
    - **Home**
        - `Index.cshtml`

### `_Layout.cshtml`

```html
<!DOCTYPE html>
<html>
<head>
    <title>@ViewData["Title"] - Meu Site</title>
    <link rel="stylesheet" href="~/css/site.css" />
</head>
<body>
    <header>
        @await Html.RenderPartialAsync("_Menu")
    </header>
    <main role="main" class="container">
        @RenderBody()
    </main>
    <footer>
        @await Html.RenderPartialAsync("_Footer")
    </footer>
    @RenderSection("Scripts", required: false)
</body>
</html>
```

### `_Menu.cshtml`

```html
<nav>
    <ul>
        <li><a href="/">Início</a></li>
        <li><a href="/Produtos">Produtos</a></li>
        <li><a href="/Servicos">Serviços</a></li>
        <li><a href="/Contato">Contato</a></li>
    </ul>
</nav>
```

### `_Footer.cshtml`

```html
<footer>
    <p>&copy; @DateTime.Now.Year - Meu Site</p>
</footer>
```

### `Index.cshtml`

```html
@{
    ViewData["Title"] = "Página Inicial";
}

<h1>Bem-vindo ao Meu Site!</h1>
<p>Esta é a página inicial.</p>
```

---

## Considerações sobre Layouts e Section Rendering

- **Layouts**: Usados para definir a estrutura comum de páginas (header, footer, etc.).
- **RenderBody**: Marca o local onde o conteúdo específico de cada view será renderizado.
- **RenderSection**: Permite definir seções opcionais ou obrigatórias nas views, como scripts ou estilos adicionais.

---

## Resumo

- **Partial Views** ajudam a modularizar a interface do usuário.
- Podem receber dados via modelo, `ViewData`, `ViewBag` ou parâmetros anônimos.
- São úteis para reutilização de código e atualização parcial de páginas com AJAX.
- Devem ser usadas para componentes simples; para lógica mais complexa, considere View Components.

---

## Recursos Adicionais

- **Documentação Oficial**: [Partial views in ASP.NET Core](https://docs.microsoft.com/pt-br/aspnet/core/mvc/views/partial)
- **Tutorial Vídeo**: Busque por vídeos que demonstrem o uso de Partial Views no ASP.NET Core MVC.
- **Comunidades e Fóruns**: Participe de comunidades como Stack Overflow para tirar dúvidas e compartilhar conhecimento.

---

## Exercícios Práticos

1. **Crie uma Partial View para exibir mensagens de erro** que possa ser reutilizada em diferentes formulários.
2. **Implemente uma Partial View para um formulário de login** que aparece em várias páginas do seu site.
3. **Utilize AJAX para atualizar uma Partial View** que exibe notificações ou alertas em tempo real.

---

Espero que este guia tenha esclarecido o uso de Partial Views no ASP.NET Core MVC e como elas podem ser aplicadas para melhorar a organização e eficiência das suas aplicações web.