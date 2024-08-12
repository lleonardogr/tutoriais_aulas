
# Web Scraping em C# com HtmlAgilityPack

## Introdução

Web scraping é a prática de extrair dados de websites. Em C#, a biblioteca **HtmlAgilityPack** é amplamente utilizada para essa tarefa. Este guia irá te mostrar como configurar um projeto de web scraping em C# e como paralelizar as tarefas para aumentar a eficiência.

## Configurando o Projeto

### Instalando HtmlAgilityPack

Para começar, você precisa instalar o pacote **HtmlAgilityPack**. Isso pode ser feito usando o NuGet Package Manager no Visual Studio:

```bash
Install-Package HtmlAgilityPack
```

### Criando o Documento HTML

Com o HtmlAgilityPack, você pode carregar uma página HTML e manipulá-la como um documento:

```csharp
var web = new HtmlWeb();
var doc = web.Load("URL_DA_PAGINA");
```

### Extraindo Dados

Você pode extrair dados utilizando **XPath** ou **manipulação de nós**:

```csharp
var nodes = doc.DocumentNode.SelectNodes("//tagname[@attribute='value']");
foreach (var node in nodes)
{
    Console.WriteLine(node.InnerText);
}
```

## Paralelizando o Web Scraping

### Por que Paralelizar?

Se você precisa processar várias páginas, fazer isso em série pode ser ineficiente. Em vez disso, podemos paralelizar as requisições para economizar tempo.

### Usando `Parallel.ForEach`

O C# oferece a classe `Parallel` para executar loops em paralelo:

```csharp
var urls = new List<string> { "URL1", "URL2", "URL3" };
Parallel.ForEach(urls, url =>
{
    var web = new HtmlWeb();
    var doc = web.Load(url);
    // Processa o documento aqui
});
```

### Gerenciando o Nível de Paralelismo

É importante controlar o nível de paralelismo para evitar sobrecarregar o sistema ou ser bloqueado pelo site:

```csharp
var options = new ParallelOptions { MaxDegreeOfParallelism = 4 };
Parallel.ForEach(urls, options, url =>
{
    var web = new HtmlWeb();
    var doc = web.Load(url);
    // Processa o documento aqui
});
```

## Considerações Finais

- **Respeite o `robots.txt` dos sites**: Nem todos os sites permitem web scraping. Verifique o arquivo `robots.txt` do site para entender suas políticas.
- **Gestão de Erros**: Sempre inclua tratamento de exceções para gerenciar erros de rede ou problemas na extração de dados.
- **Intervalos entre Requisições**: Considere implementar um intervalo entre as requisições para evitar sobrecarregar o servidor.

### Recursos Adicionais

- [HtmlAgilityPack Documentation](https://html-agility-pack.net/)
- [Artigo: How to make webscrapping using C#](https://dev.to/lleonardogr/how-to-make-webscrapping-using-c-597b)
- [Artigo: Speed up web scrapping using C#](https://dev.to/lleonardogr/parallelizing-web-scrapping-using-c-1d1p)
