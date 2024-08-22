
# Guia de IO em Java: Operações Simples, Arquivos Grandes e Manipulação de JSON

Este guia oferece uma visão passo a passo sobre como realizar operações básicas de entrada e saída (IO) em Java, incluindo a escrita e leitura de arquivos de texto simples, o gerenciamento de arquivos grandes com buffers e o trabalho com JSON.

## Sumário

1. [Operações Simples de IO](#opera-es-simples-de-io)
    - [Escrita em Arquivo TXT](#escrita-em-arquivo-txt)
    - [Leitura de Arquivo TXT](#leitura-de-arquivo-txt)
2. [Trabalhando com Arquivos Grandes](#trabalhando-com-arquivos-grandes)
    - [Escrita de Arquivos Grandes com Buffer](#escrita-de-arquivos-grandes-com-buffer)
    - [Escrita em Modo Append](#escrita-em-modo-append)
3. [Manipulação de Arquivos JSON](#manipula-o-de-arquivos-json)
    - [Escrita de JSON em Arquivo](#escrita-de-json-em-arquivo)
    - [Leitura de JSON de Arquivo](#leitura-de-json-de-arquivo)
    - [Escrita em Modo Append em JSON](#escrita-em-modo-append-em-json)

---

## Operações Simples de IO

### Escrita em Arquivo TXT

Para escrever dados em um arquivo de texto, usamos as classes `FileWriter` e `BufferedWriter`. 

#### Exemplo: {id="exemplo_1"}

```java
import java.io.BufferedWriter;
import java.io.FileWriter;
import java.io.IOException;

public class ExportarArquivo {
    public static void main(String[] args) {
        String conteudo = "Este é o conteúdo que será salvo no arquivo de texto.";
        String caminhoDoArquivo = "saida.txt";

        try (BufferedWriter writer = new BufferedWriter(new FileWriter(caminhoDoArquivo))) {
            writer.write(conteudo);
            System.out.println("Arquivo exportado com sucesso!");
        } catch (IOException e) {
            System.out.println("Ocorreu um erro ao exportar o arquivo: " + e.getMessage());
        }
    }
}
```

#### Explicação: {id="explica-o_1"}

- **FileWriter:** Escreve dados no arquivo.
- **BufferedWriter:** Melhora a performance ao armazenar temporariamente os dados antes de gravá-los.

### Leitura de Arquivo TXT

Para ler o conteúdo de um arquivo de texto, utilizamos `FileReader` e `BufferedReader`.

#### Exemplo: {id="exemplo_2"}

```java
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;

public class LerArquivo {
    public static void main(String[] args) {
        String caminhoDoArquivo = "saida.txt";

        try (BufferedReader reader = new BufferedReader(new FileReader(caminhoDoArquivo))) {
            String linha;
            while ((linha = reader.readLine()) != null) {
                System.out.println(linha);
            }
        } catch (IOException e) {
            System.out.println("Ocorreu um erro ao ler o arquivo: " + e.getMessage());
        }
    }
}
```

#### Explicação: {id="explica-o_2"}

- **FileReader:** Lê o arquivo.
- **BufferedReader:** Envolve o `FileReader` para leitura eficiente.

## Trabalhando com Arquivos Grandes

### Escrita de Arquivos Grandes com Buffer

Para escrever arquivos grandes eficientemente, podemos usar `StringBuilder` para construir o conteúdo e `BufferedWriter` para escrevê-lo.

#### Exemplo: {id="exemplo_3"}

```java
import java.io.BufferedWriter;
import java.io.FileWriter;
import java.io.IOException;

public class EscreverArquivoGrande {
    public static void main(String[] args) {
        StringBuilder conteudoGrande = new StringBuilder();
        for (int i = 0; i < 1000000; i++) {
            conteudoGrande.append("Esta é a linha número ").append(i).append("\n");
        }

        String caminhoDoArquivo = "arquivo_grande.txt";

        try (BufferedWriter writer = new BufferedWriter(new FileWriter(caminhoDoArquivo))) {
            writer.write(conteudoGrande.toString());
            System.out.println("Arquivo grande escrito com sucesso!");
        } catch (IOException e) {
            System.out.println("Ocorreu um erro ao escrever o arquivo: " + e.getMessage());
        }
    }
}
```

#### Explicação: {id="explica-o_3"}

- **StringBuilder:** Eficiência na construção de grandes strings.
- **BufferedWriter:** Grava os dados de forma eficiente usando um buffer.

### Escrita em Modo Append

Para adicionar conteúdo a um arquivo existente sem sobrescrever o conteúdo anterior, use `FileWriter` no modo append.

#### Exemplo: {id="exemplo_4"}

```java
import java.io.BufferedWriter;
import java.io.FileWriter;
import java.io.IOException;

public class AppendArquivo {
    public static void main(String[] args) {
        String conteudoAdicional = "Este é o novo conteúdo que será adicionado ao final do arquivo.\n";
        String caminhoDoArquivo = "arquivo_grande.txt";

        try (BufferedWriter writer = new BufferedWriter(new FileWriter(caminhoDoArquivo, true))) {
            writer.write(conteudoAdicional);
            System.out.println("Conteúdo adicionado ao arquivo com sucesso!");
        } catch (IOException e) {
            System.out.println("Ocorreu um erro ao adicionar conteúdo ao arquivo: " + e.getMessage());
        }
    }
}
```

#### Explicação: {id="explica-o_4"}

- **Modo append:** O parâmetro `true` no `FileWriter` permite adicionar dados ao final do arquivo.

---

## Manipulação de Arquivos JSON

### Dependências

Primeiro, adicione a dependência Gson ao seu `pom.xml` se você estiver usando Maven:

```xml
<dependencies>
    <dependency>
        <groupId>com.google.code.gson</groupId>
        <artifactId>gson</artifactId>
        <version>2.9.0</version>
    </dependency>
</dependencies>
```

### Escrita de JSON em Arquivo

Usamos a biblioteca **Gson** para converter objetos Java em JSON e escrevê-los em arquivos.

#### Exemplo: {id="exemplo_5"}

```java
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;

import java.io.FileWriter;
import java.io.IOException;

class Pessoa {
    private String nome;
    private int idade;

    public Pessoa() {
    }

    public Pessoa(String nome, int idade) {
        this.nome = nome;
        this.idade = idade;
    }

    // Getters e Setters
}

public class EscritaJson {
    public static void main(String[] args) {
        Pessoa pessoa = new Pessoa("João", 30);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();

        try (FileWriter writer = new FileWriter("pessoa.json")) {
            gson.toJson(pessoa, writer);
            System.out.println("Arquivo JSON escrito com sucesso!");
        } catch (IOException e) {
            System.out.println("Ocorreu um erro ao escrever o arquivo JSON: " + e.getMessage());
        }
    }
}
```

#### Explicação: {id="explica-o_5"}

- **Gson:** Facilita a conversão de objetos Java para JSON.
- **setPrettyPrinting:** Gera JSON formatado de maneira legível.

### Leitura de JSON de Arquivo

Podemos ler um arquivo JSON e convertê-lo de volta em um objeto Java.

#### Exemplo: {id="exemplo_6"}

```java
import com.google.gson.Gson;

import java.io.FileReader;
import java.io.IOException;

public class LeituraJson {
    public static void main(String[] args) {
        Gson gson = new Gson();

        try (FileReader reader = new FileReader("pessoa.json")) {
            Pessoa pessoa = gson.fromJson(reader, Pessoa.class);
            System.out.println("Nome: " + pessoa.getNome());
            System.out.println("Idade: " + pessoa.getIdade());
        } catch (IOException e) {
            System.out.println("Ocorreu um erro ao ler o arquivo JSON: " + e.getMessage());
        }
    }
}
```

#### Explicação: {id="explica-o_6"}

- **fromJson:** Converte JSON de volta para um objeto Java.

### Escrita em Modo Append em JSON

Para adicionar mais dados a um arquivo JSON sem sobrescrever, primeiro lemos o conteúdo existente, adicionamos novos dados e depois escrevemos de volta.

#### Exemplo:

```java
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.reflect.TypeToken;

import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;
import java.lang.reflect.Type;
import java.util.ArrayList;
import java.util.List;

public class AppendJson {
    public static void main(String[] args) {
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        Type listType = new TypeToken<List<Pessoa>>() {}.getType();
        List<Pessoa> listaPessoas = new ArrayList<>();

        // Tentar ler o conteúdo atual do arquivo, se existir
        try (FileReader reader = new FileReader("pessoa.json")) {
            listaPessoas = gson.fromJson(reader, listType);
        } catch (IOException e) {
            System.out.println("Ocorreu um erro ao ler o arquivo JSON: " + e.getMessage());
        }

        // Adicionar uma nova pessoa à lista
        Pessoa novaPessoa = new Pessoa("Maria", 25);
        listaPessoas.add(novaPessoa);

        // Escrever a lista de volta no arquivo JSON
        try (FileWriter writer = new FileWriter("pessoa.json")) {
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
