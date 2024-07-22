# Clean Code e Aplicação em Java com Reflections e Anotações

## Sumário

1. [Introdução](#introdução)
2. [Capítulo 1: Clean Code](#capítulo-1-clean-code)
3. [Capítulo 2: Significância dos Nomes](#capítulo-2-significância-dos-nomes)
4. [Capítulo 3: Funções](#capítulo-3-funções)
5. [Aplicação em Java com Reflections e Anotações](#aplicação-em-java-com-reflections-e-anotações)
6. [Comparação de Código: Antes e Depois](#comparação-de-código-antes-e-depois)

## Introdução

Este tutorial apresenta os conceitos dos primeiros capítulos do livro "Clean Code" de Robert C. Martin e demonstra como aplicá-los em Java, utilizando reflexões e anotações para simplificar um código grande de uma implementação pura de JDBC. Usaremos uma classe de CRUD chamada `ProdutoRepository` e criaremos uma anotação para facilitar a execução de queries.

## Capítulo 1: Clean Code

O primeiro capítulo de "Clean Code" aborda a importância de escrever um código limpo, que é fácil de ler, entender e manter. Robert C. Martin destaca que um código limpo é aquele que se parece com uma leitura natural, tem uma estrutura clara e é escrito de maneira que outros desenvolvedores possam compreendê-lo facilmente.

### Princípios de Código Limpo:
- **Legibilidade:** O código deve ser fácil de ler.
- **Simplicidade:** Mantenha o código o mais simples possível.
- **Clareza:** Evite ambiguidades no código.
- **Eliminação de Redundância:** Evite duplicação de código.

## Capítulo 2: Significância dos Nomes

Neste capítulo, é enfatizada a importância de escolher nomes significativos para variáveis, funções, classes e outros elementos do código. Nomes bem escolhidos podem tornar o código muito mais legível e compreensível.

### Dicas para Nomes Significativos:
- **Use Nomes Descritivos:** Nomes devem descrever claramente a intenção.
- **Evite Abreviações:** Use palavras completas para maior clareza.
- **Consistência:** Use um padrão de nomenclatura consistente em todo o código.

## Capítulo 3: Funções

Funções devem ser pequenas e ter uma única responsabilidade. Este capítulo detalha como escrever funções que seguem esses princípios.

### Boas Práticas para Funções:
- **Pequenas e Concisas:** Funções devem ser curtas e fazer apenas uma coisa.
- **Nome Descritivo:** O nome da função deve descrever claramente o que ela faz.
- **Evitar Efeitos Colaterais:** Funções não devem ter efeitos colaterais inesperados.

## Aplicação em Java com Reflections e Anotações

### Reflexões

A reflexão em Java permite que um programa inspecione e modifique seu comportamento em tempo de execução. Isso é útil para criar códigos mais flexíveis e reutilizáveis.

### Anotações

Anotações são usadas para fornecer metadados sobre o programa. Elas podem ser processadas em tempo de compilação ou em tempo de execução.

## Comparação de Código: Antes e Depois

### Código Sem Clean Code

Vamos começar com uma implementação típica de CRUD em JDBC sem aplicar os princípios de Clean Code.

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

public class ProdutoRepository {
    
    private static final String URL = "jdbc:mysql://localhost:3306/seu_banco";
    private static final String USER = "seu_usuario";
    private static final String PASSWORD = "sua_senha";

    public Produto findById(int id) {
        Produto produto = null;
        try (Connection connection = DriverManager.getConnection(URL, USER, PASSWORD)) {
            String query = "SELECT * FROM produtos WHERE id = ?";
            try (PreparedStatement statement = connection.prepareStatement(query)) {
                statement.setInt(1, id);
                try (ResultSet resultSet = statement.executeQuery()) {
                    if (resultSet.next()) {
                        produto = new Produto(resultSet.getInt("id"), resultSet.getString("nome"), resultSet.getDouble("preco"));
                    }
                }
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
        return produto;
    }

    public void save(String nome, double preco) {
        try (Connection connection = DriverManager.getConnection(URL, USER, PASSWORD)) {
            String query = "INSERT INTO produtos (nome, preco) VALUES (?, ?)";
            try (PreparedStatement statement = connection.prepareStatement(query)) {
                statement.setString(1, nome);
                statement.setDouble(2, preco);
                statement.executeUpdate();
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    public void update(int id, String nome, double preco) {
        try (Connection connection = DriverManager.getConnection(URL, USER, PASSWORD)) {
            String query = "UPDATE produtos SET nome = ?, preco = ? WHERE id = ?";
            try (PreparedStatement statement = connection.prepareStatement(query)) {
                statement.setString(1, nome);
                statement.setDouble(2, preco);
                statement.setInt(3, id);
                statement.executeUpdate();
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    public void delete(int id) {
        try (Connection connection = DriverManager.getConnection(URL, USER, PASSWORD)) {
            String query = "DELETE FROM produtos WHERE id = ?";
            try (PreparedStatement statement = connection.prepareStatement(query)) {
                statement.setInt(1, id);
                statement.executeUpdate();
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}
```

### Código Com Clean Code e Anotações

Agora vamos reescrever a classe ProdutoRepository aplicando os princípios de Clean Code e utilizando anotações para simplificar a execução das queries.

#### Passo 1: Criação da Anotação @Query

```java
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;

@Retention(RetentionPolicy.RUNTIME)
public @interface Query {
    String value();
}
```

#### Passo 2: refatoração do repository:
```java
public class ProdutoRepository {
    
    @Query("SELECT * FROM produtos WHERE id = ?")
    public Produto findById(int id) {
        // Implementação será simplificada pelo QueryProcessor
        return null;
    }
    
    @Query("INSERT INTO produtos (nome, preco) VALUES (?, ?)")
    public void save(String nome, double preco) {
        // Implementação será simplificada pelo QueryProcessor
    }
    
    @Query("UPDATE produtos SET nome = ?, preco = ? WHERE id = ?")
    public void update(int id, String nome, double preco) {
        // Implementação será simplificada pelo QueryProcessor
    }
    
    @Query("DELETE FROM produtos WHERE id = ?")
    public void delete(int id) {
        // Implementação será simplificada pelo QueryProcessor
    }
}
```

#### Passo 3: Processamento de Anotações com Reflexões

```java
import java.lang.reflect.Method;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;

public class QueryProcessor {
    
    private static final String URL = "jdbc:mysql://localhost:3306/seu_banco";
    private static final String USER = "seu_usuario";
    private static final String PASSWORD = "sua_senha";
    
    public static void executeAnnotatedMethods(Object obj, Object... params) throws Exception {
        Class<?> clazz = obj.getClass();
        for (Method method : clazz.getDeclaredMethods()) {
            if (method.isAnnotationPresent(Query.class)) {
                Query query = method.getAnnotation(Query.class);
                executeQuery(query.value(), params);
            }
        }
    }
    
    private static void executeQuery(String query, Object... params) throws Exception {
        try (Connection connection = DriverManager.getConnection(URL, USER, PASSWORD);
             PreparedStatement statement = connection.prepareStatement(query)) {
             
            for (int i = 0; i < params.length; i++) {
                statement.setObject(i + 1, params[i]);
            }
            
            if (query.trim().toUpperCase().startsWith("SELECT")) {
                try (ResultSet resultSet = statement.executeQuery()) {
                    while (resultSet.next()) {
                        // Processar resultado
                    }
                }
            } else {
                statement.executeUpdate();
            }
        }
    }
    
    public static void main(String[] args) throws Exception {
        ProdutoRepository repository = new ProdutoRepository();
        executeAnnotatedMethods(repository, 1); // Exemplo para encontrar produto por ID
    }
}
```

### Comparação e Benefícios

#### Antes (Sem Clean Code)

Código repetitivo: Cada método do repositório contém código similar para abrir conexões, preparar statements e executar queries.
Baixa reutilização: Código para manipulação de banco de dados não é reutilizável.
Difícil manutenção: Mudanças na lógica de conexão ou tratamento de exceções requerem alterações em múltiplos lugares.

#### Depois (Com Clean Code)

Redução de Redundância: A lógica para abrir conexões e executar queries é centralizada no QueryProcessor.
Aumento da Legibilidade: Uso de anotações @Query deixa claro o propósito de cada método sem se preocupar.