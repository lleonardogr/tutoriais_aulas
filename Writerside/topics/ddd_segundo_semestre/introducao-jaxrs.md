# JAX-RS com Quarkus: Introdução

O Quarkus utiliza o padrão JAX-RS (Java API for RESTful Web Services) para implementação de APIs RESTful de forma simples e eficiente. No JAX-RS, um "resource" é um componente chave modelado para corresponder a um recurso do mundo real que pode ser manipulado via HTTP.

### 1. Configurando Quarkus para JAX-RS

O Quarkus já vem com suporte nativo para JAX-RS através das extensões `quarkus-resteasy` (implementação tradicional) ou `quarkus-resteasy-reactive` (implementação reativa, recomendada para novas aplicações).

Para iniciar um projeto Quarkus com suporte a REST:

```bash
mvn io.quarkus:quarkus-maven-plugin:create \
    -DprojectGroupId=org.acme \
    -DprojectArtifactId=rest-api-example \
    -DclassName="org.acme.BookResource" \
    -Dpath="/books" \
    -Dextensions="resteasy-reactive-jackson"
```

### 2. Annotações de Recurso
O JAX-RS no Quarkus utiliza anotações para definir recursos e mapear métodos Java para operações HTTP:

- `@Path`: Define a rota URI para o recurso.
- `@GET`, `@POST`, `@PUT`, `@DELETE`: Indicam o tipo de operação HTTP que o método manipulará.
- `@Produces`, `@Consumes`: Especificam o tipo de mídia produzido ou consumido.

### 3. Definindo o Recurso
No Quarkus, você define um recurso criando uma classe Java e anotando-a com as anotações do JAX-RS.

Exemplo:

```java
package org.acme;

import java.util.List;

import javax.inject.Inject;
import javax.ws.rs.GET;
import javax.ws.rs.Path;
import javax.ws.rs.Produces;
import javax.ws.rs.core.MediaType;

@Path("/books")
public class BookResource {
    
    @Inject
    BookService bookService;
    
    @GET
    @Produces(MediaType.APPLICATION_JSON)
    public List<Book> getAllBooks() {
        return bookService.getAllBooks();
    }
}
```

### 4. Métodos de Recurso
Os métodos dentro da classe de recurso são mapeados para operações HTTP através de anotações.

- Cada método é responsável por lidar com uma operação específica (GET, POST, etc.)
- No Quarkus, esses métodos podem ser síncronos ou assíncronos (quando usando RESTEasy Reactive)
- Os métodos podem retornar diretamente objetos que serão serializados para JSON (ou outro formato)

### 5. Parâmetros de Recurso
Os parâmetros dos métodos de recurso podem ser anotados para extrair informações da requisição HTTP:

- `@PathParam`: Obtém parâmetros da URI.
- `@QueryParam`: Obtém parâmetros de consulta da URL.
- `@HeaderParam`: Obtém informações do cabeçalho HTTP.
- `@BeanParam`: Agrupa múltiplos parâmetros em uma única classe.

```java
@GET
@Path("{id}")
@Produces(MediaType.APPLICATION_JSON)
public Book getBook(@PathParam("id") Long id) {
    return bookService.getBookById(id);
}

@GET
@Path("/search")
@Produces(MediaType.APPLICATION_JSON)
public List<Book> searchBooks(@QueryParam("author") String author, @QueryParam("year") Integer year) {
    return bookService.searchBooks(author, year);
}
```

### 6. Respostas no Quarkus
No Quarkus, você tem várias opções para retornar respostas HTTP:

Retorno direto (objeto será convertido automaticamente para JSON):
```java
@POST
@Consumes(MediaType.APPLICATION_JSON)
@Produces(MediaType.APPLICATION_JSON)
public Book addBook(Book book) {
    return bookService.addBook(book);
    // Status 200 OK será retornado automaticamente
}
```

Usando `Response` para controle completo:
```java
@POST
@Consumes(MediaType.APPLICATION_JSON)
@Produces(MediaType.APPLICATION_JSON)
public Response addBook(Book book) {
    Book saved = bookService.addBook(book);
    return Response.status(Response.Status.CREATED)
                   .entity(saved)
                   .build();
}
```

Usando `Uni<T>` para respostas reativas (com RESTEasy Reactive):
```java
@GET
@Path("{id}")
@Produces(MediaType.APPLICATION_JSON)
public Uni<Book> getBookReactive(@PathParam("id") Long id) {
    return bookService.getBookByIdReactive(id);
}
```

### 7. Injeção de Dependências no Quarkus
O Quarkus usa CDI (Contexts and Dependency Injection) para injetar dependências em seus recursos:

```java
@Path("/books")
public class BookResource {
    
    @Inject
    BookRepository repository;
    
    // Métodos do recurso
}
```

### 8. Tratamento de Exceções
No Quarkus, você pode criar manipuladores de exceção globais ou específicos:

```java
@Provider
public class BookNotFoundExceptionMapper implements ExceptionMapper<BookNotFoundException> {
    
    @Override
    public Response toResponse(BookNotFoundException exception) {
        return Response
                .status(Response.Status.NOT_FOUND)
                .entity(new ErrorResponse("book_not_found", exception.getMessage()))
                .build();
    }
}
```

### 9. Iniciar e Testar sua Aplicação Quarkus
Para executar sua aplicação Quarkus em modo desenvolvimento (com hot reload):

```bash
./mvnw quarkus:dev
```

Você pode acessar sua API em http://localhost:8080/books e usar a interface interativa do Swagger UI em http://localhost:8080/q/swagger-ui/ para testar seus endpoints.

### 10. Vantagens do JAX-RS com Quarkus

- **Tempo de Inicialização Rápido**: O Quarkus inicia em milissegundos, não segundos.
- **Baixo Consumo de Memória**: Ideal para containers e ambientes com recursos limitados.
- **Dev Mode**: Recarga automática do código para desenvolvimento mais ágil.
- **Configuração Simplificada**: Menos XML e mais code-first.
- **Integração Nativa**: Integração simples com outras extensões Quarkus como Hibernate, Security, etc.

### Resumo:
1. Configure seu projeto Quarkus com as extensões RESTEasy
2. Crie classes de recurso com anotações JAX-RS
3. Implemente os métodos que representam operações HTTP
4. Use CDI para injetar serviços e repositórios
5. Execute a aplicação com `quarkus:dev` para desenvolvimento rápido
6. Teste sua API usando Swagger UI ou ferramentas como Postman
