# JAX-RS: Tutorial

## 1. Configuração Inicial:
### Criando o Projeto Maven:

No intelij, selecione um novo projeto, escoha o tipo Maven Arquetype, o catalogo Maven Central e o Arquetipo: org.glassfish.jersey.archetypes:jersey-quickstart-grizzly2

![jax_rs_template.png](jax_rs_template.png)

## Adicionar Dependência:
No arquivo pom.xml, adicione a dependência do JDBC para Oracle, lembrem-se que outra versão pode ser baixada no link: https://mvnrepository.com

```xml
<dependency>
    <groupId>com.oracle.database.jdbc</groupId>
    <artifactId>ojdbc8</artifactId>
    <version>19.8.0.0</version>
</dependency>
```

## 2. Modelo: Classe Livro

``` java
public class Livro {
    private int id;
    private String titulo;
    private String autor;
    private String sinopse;

    // Construtores, getters e setters aqui...
}
```

## 3. Resource:

``` java

import javax.ws.rs.*;
import javax.ws.rs.core.MediaType;
import java.util.List;

@Path("/livros")
public class LivroRecurso {
    
    private LivroRepositorio repositorio = new LivroRepositorio();

    @GET
    @Produces(MediaType.APPLICATION_JSON)
    public List<Livro> getLivros() {
        return repositorio.findAll();
    }

    @GET
    @Path("{id}")
    @Produces(MediaType.APPLICATION_JSON)
    public Livro getLivro(@PathParam("id") int id) {
        return repositorio.findById(id).orElse(null);
    }

    @POST
    @Consumes(MediaType.APPLICATION_JSON)
    public void addLivro(Livro livro) {
        repositorio.add(livro);
    }

    @PUT
    @Path("{id}")
    @Consumes(MediaType.APPLICATION_JSON)
    public void updateLivro(@PathParam("id") int id, Livro livro) {
        if (repositorio.findById(id).isPresent()) {
            livro.setId(id);
            repositorio.update(livro);
        }
    }

    @DELETE
    @Path("{id}")
    public void deleteLivro(@PathParam("id") int id) {
        repositorio.delete(id);
    }
}

```

## 4. Repositório

os componentes principais do JDBC (Java Database Connectivity), que é uma API Java para conectar e executar operações de banco de dados. Os componentes essenciais incluem ```Connection```, ```PreparedStatement``` e métodos como ```executeQuery()``` e ```executeUpdate()```.

### 4.1. Connection

Função:
A Connection é uma interface no JDBC que fornece métodos para se conectar a um banco de dados, realizar transações, etc.

Como é Utilizada:
É obtida através do DriverManager ou um DataSource.
Uma vez obtida, a conexão pode ser usada para criar instâncias de Statement ou PreparedStatement.

```java
Connection connection = DriverManager.getConnection(URL, USERNAME, PASSWORD);
```

Detalhes:
É sempre recomendado fechar a conexão depois de usá-la para liberar recursos.

### 2. PreparedStatement
Função:
É uma interface que estende Statement. É usado para executar SQL parametrizado, o que ajuda a prevenir ataques de injeção SQL.

Como é Utilizada:
Criado a partir de um objeto Connection.
Parâmetros são definidos antes de o SQL ser executado.
Utilizado para executar consultas SQL parametrizadas e atualizações.

Detalhes:
Melhora a performance e segurança em comparação com o Statement regular.

```java
String sql = "SELECT * FROM users WHERE username = ?";
PreparedStatement statement = connection.prepareStatement(sql);
statement.setString(1, "alice");
ResultSet resultSet = statement.executeQuery();
```

### 3. executeQuery()
Função:
Este método é utilizado para executar consultas SQL que retornam um ResultSet, geralmente SELECTs.

Como é Utilizada:
Invocado em um objeto Statement ou PreparedStatement.
Retorna um ResultSet que pode ser usado para acessar os resultados da consulta.

```java

ResultSet resultSet = statement.executeQuery();
while (resultSet.next()) {
    String username = resultSet.getString("username");
    // Processa resultado...
}

```

Segue a classe completa de repositório:

```java

import java.util.ArrayList;
import java.util.List;
import java.util.Optional;

public class LivroRepositorio {

public List<Livro> findAll() throws Exception {
    List<Livro> resultList = new ArrayList<>();
    String sql = "SELECT id, titulo, autor, sinopse FROM livros";

    try (Connection conn = DatabaseConfig.getConnection();
         PreparedStatement stmt = conn.prepareStatement(sql);
         ResultSet rs = stmt.executeQuery()) {

        while (rs.next()) {
            resultList.add(new Livro(rs.getInt("id"), rs.getString("titulo"), rs.getString("autor"), rs.getString("sinopse")));
        }
    }

    return resultList;
}

public Optional<Livro> findById(int id) throws Exception {
    String sql = "SELECT id, titulo, autor, sinopse FROM livros WHERE id = ?";
    Livro livro = null;

    try (Connection conn = DatabaseConfig.getConnection();
         PreparedStatement stmt = conn.prepareStatement(sql)) {
        stmt.setInt(1, id);
        try (ResultSet rs = stmt.executeQuery()) {
            if (rs.next()) {
                livro = new Livro(rs.getInt("id"), rs.getString("titulo"), rs.getString("autor"), rs.getString("sinopse"));
            }
        }
    }

    return Optional.ofNullable(livro);
}

public Livro add(Livro livro) throws Exception {
    String sql = "INSERT INTO livros (titulo, autor, sinopse) VALUES (?, ?, ?)";

    try (Connection conn = DatabaseConfig.getConnection();
         PreparedStatement stmt = conn.prepareStatement(sql, new String[]{"ID"})) {
        stmt.setString(1, livro.getTitulo());
        stmt.setString(2, livro.getAutor());
        stmt.setString(3, livro.getSinopse());
        stmt.executeUpdate();

        try (ResultSet generatedKeys = stmt.getGeneratedKeys()) {
            if (generatedKeys.next()) {
                livro.setId(generatedKeys.getInt(1));
            }
        }
    }

    return livro;
}

public void update(Livro livro) throws Exception {
    String sql = "UPDATE livros SET titulo = ?, autor = ?, sinopse = ? WHERE id = ?";

    try (Connection conn = DatabaseConfig.getConnection();
         PreparedStatement stmt = conn.prepareStatement(sql)) {
        stmt.setString(1, livro.getTitulo());
        stmt.setString(2, livro.getAutor());
        stmt.setString(3, livro.getSinopse());
        stmt.setInt(4, livro.getId());
        stmt.executeUpdate();
    }
}

public void delete(int id) throws Exception {
    String sql = "DELETE FROM livros WHERE id = ?";

    try (Connection conn = DatabaseConfig.getConnection();
         PreparedStatement stmt = conn.prepareStatement(sql)) {
        stmt.setInt(1, id);
        stmt.executeUpdate();
    }
}


}

```

Links Úteis:

https://www.treinaweb.com.br/blog/criando-uma-api-restful-com-a-jax-rs-api

https://www.baeldung.com/jax-rs-spec-and-implementations
