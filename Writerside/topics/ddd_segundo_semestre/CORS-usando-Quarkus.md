# Implementando CORS em Aplicações Quarkus

## Introdução

O CORS (Cross-Origin Resource Sharing) é um mecanismo que permite que recursos restritos em uma página web sejam solicitados a partir de um domínio diferente do domínio onde o primeiro recurso foi disponibilizado. O CORS é essencial no desenvolvimento de aplicações modernas, especialmente quando temos frontend e backend em domínios diferentes.

Neste guia, vamos aprender como implementar e configurar o CORS em aplicações Quarkus, permitindo que sua API seja consumida de forma segura por aplicações em diferentes origens.

## Pré-requisitos

- JDK 11+ instalado
- Maven ou Gradle configurado
- Conhecimento básico de Quarkus
- IDE de sua preferência (VS Code, IntelliJ IDEA, Eclipse)

## Habilitando CORS no Quarkus

O Quarkus oferece suporte nativo para CORS através da extensão `quarkus-resteasy-reactive-jackson` ou `quarkus-resteasy-jackson`, dependendo de qual stack REST você está utilizando (reativa ou tradicional).

### Passo 1: Adicionando a extensão necessária

Se você ainda não tem um projeto Quarkus, crie um com o comando:

```bash
mvn io.quarkus:quarkus-maven-plugin:create \
    -DprojectGroupId=org.acme \
    -DprojectArtifactId=cors-example \
    -DclassName="org.acme.GreetingResource" \
    -Dpath="/hello"
```

Para adicionar suporte ao CORS, inclua a extensão RESTEasy no seu projeto:

#### Para Maven (pom.xml):

```xml
<!-- Para aplicações que usam RESTEasy Reactive -->
<dependency>
    <groupId>io.quarkus</groupId>
    <artifactId>quarkus-resteasy-reactive-jackson</artifactId>
</dependency>

<!-- OU para aplicações que usam RESTEasy tradicional -->
<dependency>
    <groupId>io.quarkus</groupId>
    <artifactId>quarkus-resteasy-jackson</artifactId>
</dependency>
```

### Passo 2: Configurando CORS no application.properties

O Quarkus torna a configuração do CORS simples através do arquivo `application.properties`. Adicione as seguintes linhas:

```bash
# Habilitar CORS
quarkus.http.cors=true

# Origens permitidas (separe por vírgula para múltiplas origens)
quarkus.http.cors.origins=http://localhost:3000,https://meuapp.com

# Métodos HTTP permitidos
quarkus.http.cors.methods=GET,POST,PUT,DELETE

# Headers permitidos
quarkus.http.cors.headers=Content-Type,Authorization

# Expor cabeçalhos específicos para o cliente
quarkus.http.cors.exposed-headers=Content-Disposition

# Tempo (em segundos) que as informações de preflight devem ser armazenadas em cache
quarkus.http.cors.access-control-max-age=24H

# Permitir credenciais (cookies, cabeçalhos de autorização)
quarkus.http.cors.access-control-allow-credentials=true
```

### Passo 3: Configurando CORS para endpoints específicos (Opcional)

Se você precisar de configurações específicas para rotas diferentes, pode usar anotações para isso em seus recursos JAX-RS:

```java
package org.acme;

import javax.ws.rs.GET;
import javax.ws.rs.Path;
import javax.ws.rs.Produces;
import javax.ws.rs.core.MediaType;
import org.eclipse.microprofile.config.inject.ConfigProperty;
import org.jboss.resteasy.annotations.jaxrs.PathParam;

@Path("/api")
public class MyResource {

    @GET
    @Path("/public")
    @Produces(MediaType.APPLICATION_JSON)
    // Este endpoint utilizará a configuração global de CORS
    public String publicEndpoint() {
        return "{\"message\": \"Este é um endpoint público\"}";
    }

    @GET
    @Path("/restricted")
    @Produces(MediaType.APPLICATION_JSON)
    // Este endpoint poderia ter configurações específicas de CORS
    public String restrictedEndpoint() {
        return "{\"message\": \"Este é um endpoint restrito\"}";
    }
}
```

## Testando sua configuração CORS

### Usando cURL para testar a resposta OPTIONS (Preflight)

```bash
curl -X OPTIONS \
  -H "Origin: http://localhost:3000" \
  -H "Access-Control-Request-Method: GET" \
  -H "Access-Control-Request-Headers: Content-Type,Authorization" \
  -v http://localhost:8080/api/public
```

Você deve ver cabeçalhos na resposta como:
```
< Access-Control-Allow-Origin: http://localhost:3000
< Access-Control-Allow-Methods: GET,POST,PUT,DELETE
< Access-Control-Allow-Headers: Content-Type,Authorization
< Access-Control-Max-Age: 86400
< Access-Control-Allow-Credentials: true
```

### Usando uma aplicação web

Se você tiver uma aplicação frontend (como React, Angular ou Vue) rodando em `http://localhost:3000`, pode testar a comunicação com seu backend Quarkus com um código simples:

```javascript
fetch('http://localhost:8080/api/public', {
  method: 'GET',
  headers: {
    'Content-Type': 'application/json',
    'Authorization': 'Bearer token'
  },
  credentials: 'include' // para enviar cookies
})
.then(response => response.json())
.then(data => console.log('Sucesso:', data))
.catch((error) => console.error('Erro:', error));
```

## Considerações de Segurança

1. **Não use `*` em produção**: Embora `quarkus.http.cors.origins=*` permita requisições de qualquer origem, isso só deve ser usado em desenvolvimento.

2. **Limite os métodos e cabeçalhos**: Configure apenas os métodos HTTP e cabeçalhos que realmente são necessários para sua aplicação.

3. **Access-Control-Allow-Credentials**: Tenha cuidado ao habilitar esta opção em conjunto com origens muito amplas, pois isso pode criar vulnerabilidades de segurança.

4. **Diferencie ambientes**: Use perfis do Quarkus para ter configurações distintas em desenvolvimento e produção:

```bash
# Desenvolvimento
%dev.quarkus.http.cors=true
%dev.quarkus.http.cors.origins=*

# Produção
%prod.quarkus.http.cors=true
%prod.quarkus.http.cors.origins=https://app.meusite.com,https://admin.meusite.com
```

## Conclusão

O Quarkus oferece uma maneira simples e eficiente de configurar o CORS em suas aplicações. Com poucas linhas de configuração, você pode garantir que sua API seja acessível de forma segura por clientes em diferentes origens, seguindo as melhores práticas de segurança web.

A abordagem de configuração baseada em propriedades facilita tanto a manutenção quanto a adaptação para diferentes ambientes, tornando o Quarkus uma excelente escolha para o desenvolvimento de APIs modernas e seguras.

## Recursos Adicionais

- [Documentação oficial do Quarkus sobre CORS](https://quarkus.io/guides/http-reference#cors-filter)
- [MDN Web Docs: CORS](https://developer.mozilla.org/pt-BR/docs/Web/HTTP/CORS)
- [Quarkus - Criando sua primeira aplicação](https://quarkus.io/guides/getting-started)
