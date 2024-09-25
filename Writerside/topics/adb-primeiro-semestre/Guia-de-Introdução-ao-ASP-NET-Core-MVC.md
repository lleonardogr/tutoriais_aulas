# ASP.NET Core MVC: Introdução

## Introdução

ASP.NET Core MVC é um framework robusto para construir aplicações web modernas e escaláveis usando o padrão Model-View-Controller (MVC). Este guia explorará os componentes fundamentais do ASP.NET Core MVC, incluindo o Razor, os controllers, os results e como as models são usadas dentro das views.

![vs_projects_templates.png](vs_projects_templates.png)

## 1. Arquitetura MVC no ASP.NET Core

Antes de mergulharmos nos detalhes, é essencial entender o padrão MVC:

	•	Model (Modelo): Representa os dados da aplicação e a lógica de negócios.
	•	View (Visão): Responsável pela apresentação dos dados ao usuário.
	•	Controller (Controlador): Intermediário que manipula as solicitações do usuário, interage com o modelo e seleciona a view adequada para renderizar.

## 2. Razor: O Motor de Visualização

O Razor é a linguagem de marcação usada nas views do ASP.NET Core MVC. Ele permite misturar código C# com HTML de forma fluida e expressiva.

### Sintaxe Básica

Usa o símbolo @ para transicionar entre HTML e C#.

```HTML
<h1>@ViewData["Title"]</h1>
<p>Bem-vindo, @Model.Nome!</p>
```

Helpers do Razor

Facilita a geração de elementos HTML com funcionalidades do servidor.

```HTML
@Html.ActionLink("Home", "Index", "Home")
```

Layouts e Seções

Permite criar layouts comuns para várias views, promovendo reutilização.

```HTML
@{
    Layout = "_Layout";
}
```

## 3. Controllers (Controladores)

Os controllers são responsáveis por manipular as solicitações HTTP e retornar respostas apropriadas.

Criando um Controller

Herda da classe Controller.

```C#
public class HomeController : Controller
{
    public IActionResult Index()
    {
        return View();
    }
}
```

### Actions (Ações)

Métodos dentro do controller que correspondem a endpoints.

```C#
public IActionResult Sobre()
{
    return View();
}
```

Recebendo Dados do Usuário

Via parâmetros de método, query strings, formulários, etc.

```C#
[HttpPost]
public IActionResult Contato(FormularioContato model)
{
    if (ModelState.IsValid)
    {
        // Processar o formulário
    }
    return View(model);
}
```

## 4. Results (Resultados)

Os métodos de ação retornam objetos que derivam de IActionResult, indicando como o framework deve processar a resposta.

Tipos Comuns de Resultados

```C#
//ViewResult: Renderiza uma view.
return View();

//RedirectResult: Redireciona para outra ação ou URL.
return RedirectToAction("Index");

//JsonResult: Retorna dados em formato JSON.
return Json(novoObjeto);

//ContentResult: Retorna conteúdo textual.
return Content("Olá, mundo!");

//StatusCodeResult: Retorna um código HTTP específico.
return StatusCode(404);
```

## 5. Models dentro das Views

As models contêm os dados que serão exibidos nas views.

### Passando Model para a View

Do controller para a view.

```C#
public IActionResult Detalhes(int id)
{
    var produto = _context.Produtos.Find(id);
    return View(produto);
}
```

### Definindo a Model na View

No topo da view, especifica-se o tipo de model.

```HTML
@model MeuProjeto.Models.Produto

<h2>@Model.Nome</h2>
<p>Preço: @Model.Preco</p>
```

### ViewData e ViewBag

Alternativas para passar dados dinâmicos.

```C#
ViewData["Mensagem"] = "Bem-vindo!";
ViewBag.Saudacao = "Olá!";
```

```HTML
<p>@ViewData["Mensagem"]</p>
<p>@ViewBag.Saudacao</p>
```

## 6. Exemplo Prático

### Model: Produto

```C#
public class Produto
{
    public int Id { get; set; }
    public string Nome { get; set; }
    public decimal Preco { get; set; }
}
```

### Controller: ProdutosController

```C#
public class ProdutosController : Controller
{
    private readonly List<Produto> _produtos = new List<Produto>
    {
        new Produto { Id = 1, Nome = "Caneta", Preco = 2.00M },
        new Produto { Id = 2, Nome = "Caderno", Preco = 15.00M },
        new Produto { Id = 3, Nome = "Borracha", Preco = 1.50M },
    };

    public IActionResult Index()
    {
        return View(_produtos);
    }

    public IActionResult Detalhes(int id)
    {
        var produto = _produtos.FirstOrDefault(p => p.Id == id);
        if (produto == null)
            return NotFound();

        return View(produto);
    }
}
```
### View: Index.cshtml
```HTML
@model IEnumerable<MeuProjeto.Models.Produto>

<h1>Lista de Produtos</h1>
<ul>
    @foreach (var produto in Model)
    {
        <li>
            @Html.ActionLink(produto.Nome, "Detalhes", new { id = produto.Id })
        </li>
    }
</ul>
```

### View: Details.cshtml
```HTML
@model MeuProjeto.Models.Produto

<h1>@Model.Nome</h1>
<p>Preço: @Model.Preco.ToString("C")</p>
<p>@Html.ActionLink("Voltar", "Index")</p>
```

## 7. Conclusão

Neste guia, exploramos os fundamentos do ASP.NET Core MVC:

	•	Razor: Combinação elegante de C# e HTML para gerar conteúdo dinâmico.
	•	Controllers: Manipulam solicitações e determinam as respostas.
	•	Results: Diferentes formas de retornar respostas ao cliente.
	•	Models e Views: Como os dados são estruturados e apresentados ao usuário.

Compreender esses conceitos é essencial para desenvolver aplicações web eficientes com o ASP.NET Core MVC.

<seealso>
    <category ref="wrs">
        <a href="https://docs.microsoft.com/pt-br/aspnet/core/mvc" />
        <a href="https://docs.microsoft.com/pt-br/aspnet/core/tutorials/first-mvc-app/start-mvc" />
    </category>
</seealso>