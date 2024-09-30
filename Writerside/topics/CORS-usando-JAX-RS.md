# JAX-RS: CORS

## Introdução

No desenvolvimento de aplicações web, frequentemente precisamos que um frontend (como uma aplicação Angular ou React) consuma APIs REST hospedadas em domínios diferentes. Por padrão, navegadores web implementam uma política de segurança chamada **Same-Origin Policy**, que impede que scripts em uma página interajam com recursos de um domínio diferente. O **CORS** (Cross-Origin Resource Sharing) é um mecanismo que permite que recursos restritos em uma página web sejam solicitados a partir de outro domínio fora do domínio do qual o recurso foi servido.

Este guia irá explicar:

- O que é CORS e como funciona.
- Como habilitar CORS em aplicações JAX-RS.
- Como configurar e responder às requisições **OPTIONS** que os navegadores enviam durante uma solicitação CORS.

---

## O que é CORS?

### Conceito

**CORS** (Cross-Origin Resource Sharing) é um padrão de segurança que permite que um servidor indique explicitamente que sua API pode ser acessada por outros domínios. Isso é feito através do uso de cabeçalhos HTTP que informam aos navegadores quais origens são permitidas para acessar os recursos.

### Same-Origin Policy

A **Same-Origin Policy** é uma medida de segurança implementada pelos navegadores que restringe como um documento ou script carregado de uma origem pode interagir com recursos de outra origem. Uma "origem" é definida pelo esquema (protocol), host (domínio) e porta.

### Como o CORS Funciona

Quando uma aplicação frontend faz uma requisição para um domínio diferente, o navegador verifica se a resposta do servidor inclui os cabeçalhos CORS apropriados que permitem essa interação. Se os cabeçalhos estiverem presentes e forem válidos, o navegador permite a resposta; caso contrário, a requisição é bloqueada.

Existem dois tipos principais de requisições CORS:

1. **Simple Requests** (Requisições Simples): GET, POST ou HEAD com tipos de conteúdo simples.
2. **Preflighted Requests** (Requisições Preflight): Para métodos diferentes ou cabeçalhos personalizados, o navegador envia uma requisição **OPTIONS** antes da requisição real para verificar se é permitido.

---

## Habilitando CORS em JAX-RS

Para permitir que seu serviço JAX-RS responda a requisições CORS, você precisa configurar seu aplicativo para adicionar os cabeçalhos CORS apropriados nas respostas.

### Método 1: Usando um Filtro ContainerResponseFilter

Uma maneira eficaz de habilitar CORS em JAX-RS é criando um filtro que intercepta as respostas e adiciona os cabeçalhos necessários.

#### Passo 1: Criar o Filtro CORS

```java
import javax.ws.rs.container.ContainerResponseFilter;
import javax.ws.rs.container.ContainerRequestContext;
import javax.ws.rs.container.ContainerResponseContext;
import javax.ws.rs.ext.Provider;
import java.io.IOException;

@Provider
public class CorsFilter implements ContainerResponseFilter {

    @Override
    public void filter(ContainerRequestContext requestContext, ContainerResponseContext responseContext) throws IOException {
        // Adicionar cabeçalhos CORS
        responseContext.getHeaders().add("Access-Control-Allow-Origin", "*"); // Permitir todas as origens
        responseContext.getHeaders().add("Access-Control-Allow-Headers", "origin, content-type, accept, authorization");
        responseContext.getHeaders().add("Access-Control-Allow-Credentials", "true");
        responseContext.getHeaders().add("Access-Control-Allow-Methods", "GET, POST, PUT, DELETE, OPTIONS, HEAD");
        responseContext.getHeaders().add("Access-Control-Max-Age", "1209600");
    }
}
```

**Explicação dos Cabeçalhos:**

- **Access-Control-Allow-Origin**: Especifica quais origens têm permissão para acessar o recurso. `*` significa todas as origens.
- **Access-Control-Allow-Headers**: Lista de cabeçalhos permitidos na requisição.
- **Access-Control-Allow-Credentials**: Indica se a resposta pode ser exposta quando o valor do cabeçalho `credentials` é `true`.
- **Access-Control-Allow-Methods**: Métodos HTTP permitidos.
- **Access-Control-Max-Age**: Tempo em segundos que o resultado da requisição preflight pode ser armazenado em cache.

#### Passo 2: Registrar o Filtro

Dependendo da forma como você está configurando seu aplicativo JAX-RS, você precisa registrar o filtro.

**Usando Application Subclass:**

```java
import javax.ws.rs.ApplicationPath;
import javax.ws.rs.core.Application;
import java.util.Set;
import java.util.HashSet;

@ApplicationPath("api")
public class MyApplication extends Application {

    @Override
    public Set<Object> getSingletons() {
        Set<Object> singletons = new HashSet<>();
        singletons.add(new CorsFilter()); // Registrar o filtro
        return singletons;
    }
}
```

**Usando arquivos de configuração (por exemplo, no `web.xml`):**

Se você estiver usando um framework que detecta automaticamente as classes com a anotação `@Provider`, como o Jersey, não precisa registrar o filtro manualmente.

### Método 2: Configurando Cabeçalhos em Recursos Específicos

Você também pode adicionar os cabeçalhos CORS diretamente nas respostas dos recursos, mas isso é menos recomendado devido à repetição.

```java
import javax.ws.rs.GET;
import javax.ws.rs.Path;
import javax.ws.rs.core.Response;
import javax.ws.rs.core.Response.ResponseBuilder;

@Path("/usuarios")
public class UsuarioResource {

    @GET
    public Response getUsuarios() {
        // Lógica para obter os usuários
        ResponseBuilder response = Response.ok(/* dados dos usuários */);
        response.header("Access-Control-Allow-Origin", "*");
        return response.build();
    }
}
```

---

## Lidando com Requisições OPTIONS (Preflight)

### O que é uma Requisição Preflight?

Quando uma requisição não é simples (por exemplo, usa um método diferente de GET, POST ou HEAD, ou inclui cabeçalhos personalizados), o navegador envia uma requisição **OPTIONS** antes da requisição real. Isso é chamado de **Preflight Request**.

A requisição OPTIONS é usada para verificar se a operação é permitida pelo servidor.

### Exemplo de Requisição OPTIONS

**Cabeçalhos da Requisição OPTIONS:**

```
OPTIONS /api/usuarios HTTP/1.1
Host: exemplo.com
Origin: http://meusite.com
Access-Control-Request-Method: POST
Access-Control-Request-Headers: Content-Type
```

**Cabeçalhos da Resposta Esperada:**

```
HTTP/1.1 200 OK
Access-Control-Allow-Origin: http://meusite.com
Access-Control-Allow-Methods: GET, POST, PUT, DELETE, OPTIONS
Access-Control-Allow-Headers: Content-Type
Access-Control-Max-Age: 86400
```

### Habilitando Suporte a OPTIONS em JAX-RS

Para responder corretamente às requisições OPTIONS, você precisa garantir que seu serviço possa lidar com elas.

#### Método 1: Usando o Filtro para Responder a OPTIONS

Modifique o filtro para verificar se a requisição é do método OPTIONS e ajustar a resposta.

```java
import javax.ws.rs.container.ContainerResponseFilter;
import javax.ws.rs.container.ContainerRequestFilter;
import javax.ws.rs.container.ContainerRequestContext;
import javax.ws.rs.container.ContainerResponseContext;
import javax.ws.rs.core.Response;
import javax.ws.rs.ext.Provider;
import java.io.IOException;

@Provider
public class CorsFilter implements ContainerRequestFilter, ContainerResponseFilter {

    @Override
    public void filter(ContainerRequestContext requestContext) throws IOException {
        // Se a requisição for OPTIONS, podemos configurar a resposta aqui
        if (requestContext.getRequest().getMethod().equalsIgnoreCase("OPTIONS")) {
            // Construir uma resposta vazia com os cabeçalhos CORS
            Response.ResponseBuilder response = Response.ok();
            response.header("Access-Control-Allow-Origin", "*");
            response.header("Access-Control-Allow-Headers", "origin, content-type, accept, authorization");
            response.header("Access-Control-Allow-Credentials", "true");
            response.header("Access-Control-Allow-Methods", "GET, POST, PUT, DELETE, OPTIONS, HEAD");
            response.header("Access-Control-Max-Age", "1209600");
            requestContext.abortWith(response.build());
        }
    }

    @Override
    public void filter(ContainerRequestContext requestContext, ContainerResponseContext responseContext) throws IOException {
        // Adicionar cabeçalhos CORS em todas as respostas
        responseContext.getHeaders().add("Access-Control-Allow-Origin", "*");
        responseContext.getHeaders().add("Access-Control-Allow-Headers", "origin, content-type, accept, authorization");
        responseContext.getHeaders().add("Access-Control-Allow-Credentials", "true");
        responseContext.getHeaders().add("Access-Control-Allow-Methods", "GET, POST, PUT, DELETE, OPTIONS, HEAD");
        responseContext.getHeaders().add("Access-Control-Max-Age", "1209600");
    }
}
```

#### Método 2: Criar um Recurso Específico para OPTIONS

Você pode adicionar um método em seus recursos para lidar com o método OPTIONS.

```java
import javax.ws.rs.OPTIONS;
import javax.ws.rs.Path;
import javax.ws.rs.core.Response;

@Path("/usuarios")
public class UsuarioResource {

    @OPTIONS
    public Response options() {
        return Response.ok()
                .header("Access-Control-Allow-Origin", "*")
                .header("Access-Control-Allow-Headers", "origin, content-type, accept, authorization")
                .header("Access-Control-Allow-Methods", "GET, POST, PUT, DELETE, OPTIONS, HEAD")
                .header("Access-Control-Max-Age", "1209600")
                .build();
    }

    // Outros métodos (GET, POST, etc.)
}
```

**Nota:** Este método precisa ser repetido em cada recurso, o que não é ideal. O uso de um filtro é preferível.

---

## Exemplo Completo

Vamos reunir tudo em um exemplo completo.

### Estrutura do Projeto

- `src/main/java`
    - `com.exemplo`
        - `ApplicationConfig.java` (classe de configuração)
        - `CorsFilter.java` (filtro CORS)
        - `resources`
            - `UsuarioResource.java` (recurso JAX-RS)

### Arquivos

#### CorsFilter.java

```java
package com.exemplo;

import javax.ws.rs.container.ContainerResponseFilter;
import javax.ws.rs.container.ContainerRequestFilter;
import javax.ws.rs.container.ContainerRequestContext;
import javax.ws.rs.container.ContainerResponseContext;
import javax.ws.rs.core.Response;
import javax.ws.rs.ext.Provider;
import java.io.IOException;

@Provider
public class CorsFilter implements ContainerRequestFilter, ContainerResponseFilter {

    @Override
    public void filter(ContainerRequestContext requestContext) throws IOException {
        if (requestContext.getRequest().getMethod().equalsIgnoreCase("OPTIONS")) {
            Response.ResponseBuilder response = Response.ok();
            response.header("Access-Control-Allow-Origin", "*");
            response.header("Access-Control-Allow-Headers", "origin, content-type, accept, authorization");
            response.header("Access-Control-Allow-Credentials", "true");
            response.header("Access-Control-Allow-Methods", "GET, POST, PUT, DELETE, OPTIONS, HEAD");
            response.header("Access-Control-Max-Age", "1209600");
            requestContext.abortWith(response.build());
        }
    }

    @Override
    public void filter(ContainerRequestContext requestContext, ContainerResponseContext responseContext) throws IOException {
        responseContext.getHeaders().add("Access-Control-Allow-Origin", "*");
        responseContext.getHeaders().add("Access-Control-Allow-Headers", "origin, content-type, accept, authorization");
        responseContext.getHeaders().add("Access-Control-Allow-Credentials", "true");
        responseContext.getHeaders().add("Access-Control-Allow-Methods", "GET, POST, PUT, DELETE, OPTIONS, HEAD");
        responseContext.getHeaders().add("Access-Control-Max-Age", "1209600");
    }
}
```

#### ApplicationConfig.java

```java
package com.exemplo;

import javax.ws.rs.ApplicationPath;
import javax.ws.rs.core.Application;

@ApplicationPath("api")
public class ApplicationConfig extends Application {
    // Se você precisar registrar classes manualmente, pode sobrecarregar os métodos getClasses() ou getSingletons()
}
```

#### UsuarioResource.java

```java
package com.exemplo.resources;

import javax.ws.rs.GET;
import javax.ws.rs.Path;
import javax.ws.rs.core.Response;

@Path("/usuarios")
public class UsuarioResource {

    @GET
    public Response getUsuarios() {
        // Lógica para obter os usuários
        return Response.ok("Lista de usuários").build();
    }
}
```

---

## Testando a Configuração CORS

Para verificar se o CORS está configurado corretamente:

1. **Inicie o Servidor**: Execute sua aplicação em um servidor (por exemplo, Apache Tomcat, GlassFish, etc.).
2. **Faça uma Requisição de Outro Domínio**: Use uma ferramenta como o `curl` ou um frontend hospedado em outro domínio para fazer uma requisição ao seu serviço.

**Exemplo de Requisição OPTIONS com `curl`:**

```bash
curl -i -X OPTIONS http://localhost:8080/api/usuarios \
  -H "Origin: http://meusite.com" \
  -H "Access-Control-Request-Method: GET"
```

**Resposta Esperada:**

```
HTTP/1.1 200 OK
Access-Control-Allow-Origin: *
Access-Control-Allow-Headers: origin, content-type, accept, authorization
Access-Control-Allow-Credentials: true
Access-Control-Allow-Methods: GET, POST, PUT, DELETE, OPTIONS, HEAD
Access-Control-Max-Age: 1209600

```

---

## Considerações de Segurança

- **Access-Control-Allow-Origin**: Evite usar `*` em ambientes de produção. Em vez disso, especifique os domínios permitidos.

  ```java
  String origin = requestContext.getHeaderString("Origin");
  if (isOriginAllowed(origin)) {
      responseContext.getHeaders().add("Access-Control-Allow-Origin", origin);
  }
  ```

- **Access-Control-Allow-Credentials**: Se você precisar suportar credenciais (cookies, autenticação HTTP), não pode usar `*` no `Access-Control-Allow-Origin`. Deve especificar a origem.

- **Validação de Métodos e Cabeçalhos**: Verifique se os métodos e cabeçalhos permitidos são realmente necessários.

---

## Alternativas e Ferramentas

### Usando Bibliotecas de Terceiros

Existem bibliotecas que facilitam a configuração do CORS, como:

- **CORS Filter do Container**: Alguns servidores de aplicação fornecem filtros CORS embutidos que podem ser configurados no `web.xml`.

**Exemplo no `web.xml`:**

```xml
<filter>
    <filter-name>CorsFilter</filter-name>
    <filter-class>org.apache.catalina.filters.CorsFilter</filter-class>
    <init-param>
        <param-name>cors.allowed.origins</param-name>
        <param-value>http://meusite.com</param-value>
    </init-param>
    <!-- Outros parâmetros de configuração -->
</filter>
<filter-mapping>
    <filter-name>CorsFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

### Usando Frameworks JAX-RS Específicos

Alguns frameworks JAX-RS oferecem anotações ou configurações para habilitar CORS de forma mais simples.

**Exemplo com Jersey:**

```java
import org.glassfish.jersey.server.ResourceConfig;
import org.glassfish.jersey.filter.LoggingFilter;

public class ApplicationConfig extends ResourceConfig {
    public ApplicationConfig() {
        packages("com.exemplo.resources");
        register(CorsFilter.class); // Registrar o filtro
    }
}
```

---

## Resumo

- **CORS** é essencial para permitir que aplicações frontend acessem APIs em domínios diferentes.
- Em **JAX-RS**, o método mais eficaz para habilitar CORS é através de um **ContainerResponseFilter** que adiciona os cabeçalhos CORS a todas as respostas.
- É importante lidar corretamente com as **requisições OPTIONS** enviadas pelos navegadores durante o preflight, adicionando os cabeçalhos necessários e retornando uma resposta adequada.
- Sempre considere as **melhores práticas de segurança**, especificando origens permitidas e limitando métodos e cabeçalhos conforme necessário.

---

## Exercícios Práticos

1. **Implementar CORS em uma API Existente**: Pegue uma API JAX-RS que você já tenha e habilite o CORS usando um filtro.
2. **Especificar Origens Permitidas**: Modifique o filtro para permitir apenas determinadas origens, e teste o acesso a partir de origens permitidas e não permitidas.
3. **Suportar Credenciais**: Configure o CORS para permitir o envio de credenciais (cookies, autenticação HTTP), ajustando os cabeçalhos adequadamente.
4. **Usar um Framework**: Se estiver usando um framework como o Spring Boot, explore como habilitar CORS usando as ferramentas fornecidas pelo framework.

---

## Recursos Adicionais

<seealso>
    <category ref="wrs">
        <a href="https://developer.mozilla.org/pt-BR/docs/Web/HTTP/Controle_Acesso_CORS"/>
        <a href="https://www.w3.org/TR/cors/"/>
        <a href="https://www.baeldung.com/cors-in-jax-rs" />
    </category>
</seealso>
