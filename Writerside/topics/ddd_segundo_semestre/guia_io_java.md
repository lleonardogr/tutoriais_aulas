
            gson.toJson(listaPessoas, writer);
            System.out.println("Novo conteúdo adicionado ao arquivo JSON com sucesso!");
        } catch (IOException e) {
            System.out.println("Ocorreu um erro ao escrever o arquivo JSON: " + e.getMessage());
        }
    }
}
```

#### Explicação:

- **TypeToken:** Utilizado para lidar com tipos genéricos, como `List<Pessoa>`.
- **Escrita em modo append:** Lê o conteúdo existente do arquivo, adiciona novos dados e grava de volta no arquivo.

---

Este guia cobre os fundamentos de IO em Java, incluindo operações básicas de leitura e escrita, gerenciamento eficiente de arquivos grandes, e manipulação de dados JSON usando a biblioteca Gson. Essas técnicas são essenciais para o desenvolvimento de aplicações robustas que precisam interagir com sistemas de arquivos e dados estruturados.
