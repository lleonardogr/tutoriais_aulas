# 1. Introdução a WebServices e APIs RESTful

Neste tópico, exploraremos a evolução dos WebServices desde suas origens até o modelo atual de APIs RESTful
e como esses conceitos se aplicam ao desenvolvimento moderno com Quarkus.

## A Evolução dos WebServices

Os WebServices surgiram no início dos anos 2000 como uma solução para a comunicação entre sistemas heterogêneos
através da internet. Antes disso, a integração entre sistemas era frequentemente feita através de protocolos
proprietários e interfaces personalizadas.

Os primeiros WebServices eram baseados em alguns padrões principais:

- **SOAP (Simple Object Access Protocol)**: Um protocolo baseado em XML para troca de mensagens
- **WSDL (Web Services Description Language)**: Uma linguagem para descrição de interfaces
- **UDDI (Universal Description, Discovery, and Integration)**: Um diretório para registro e descoberta de serviços

Estes padrões, conhecidos coletivamente como "WebServices WS-*", criaram uma base para comunicações
interoperáveis, mas foram criticados por sua complexidade e overhead.

## O Surgimento do REST

Em 2000, Roy Fielding propôs em sua tese de doutorado uma nova arquitetura para sistemas web chamada 
**REST (Representational State Transfer)**. O REST não era um protocolo, mas um estilo
arquitetural baseado em princípios simples:

- **Recursos identificados por URIs**: Cada recurso tem um endereço único
- **Interface uniforme**: Operações padronizadas (GET, POST, PUT, DELETE)
- **Mensagens autodescritivas**: Informações necessárias para processamento incluídas na mensagem
- **HATEOAS** (Hypermedia as the Engine of Application State): Links para navegar entre recursos
- **Comunicação stateless**: Cada requisição contém toda informação necessária

A simplicidade, escalabilidade e alinhamento com os princípios da web tornaram o REST cada vez mais popular,
especialmente com a adoção crescente de formatos como JSON, que era mais leve que XML.

## APIs RESTful Modernas

As APIs RESTful modernas se tornaram o padrão de facto para integração de sistemas web, mobile e IoT.
Algumas características das APIs RESTful modernas incluem:

- Uso predominante de **JSON** como formato de dados
- Versionamento explícito de APIs (por exemplo, `/api/v1/resources`)
- Autenticação por tokens (OAuth 2.0, JWT)
- Design focado em recursos e não em ações
- Respostas com códigos de status HTTP apropriados

O modelo RESTful permitiu o crescimento exponencial de serviços web interoperáveis, criando ecossistemas inteiros
de APIs que podem ser facilmente compostas e integradas.

## O Padrão OpenAPI

Com a proliferação de APIs RESTful, surgiu a necessidade de padronizar sua documentação e definição. É aqui que
entra o **OpenAPI Specification** (anteriormente conhecido como Swagger).

O OpenAPI é uma especificação para arquivos de definição de interface de API RESTful que é agnóstica de linguagem.
O formato permite:

- Descrever endpoints e operações disponíveis na API
- Definir parâmetros de entrada e estruturas de resposta
- Especificar métodos de autenticação
- Documentar exemplos de requisição e resposta
- Gerar automaticamente clientes e servidores em várias linguagens

A especificação OpenAPI promove o conceito de **design-first**, onde a API é projetada e documentada antes
da implementação, ou **code-first**, onde a documentação é gerada a partir do código.

```yaml
openapi: 3.0.0
info:
  title: Exemplo de API Simples
  version: 1.0.0
paths:
  /users:
    get:
      summary: Lista todos os usuários
      responses:
        '200':
          description: Lista de usuários
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/User'
components:
  schemas:
    User:
      type: object
      properties:
        id:
          type: integer
        name:
          type: string
```

## RESTful APIs e Quarkus

O Quarkus é um framework Java nativo para Kubernetes que oferece excelente suporte para o desenvolvimento
de APIs RESTful através de:

- Integração nativa com **RESTEasy** (implementação JAX-RS)
- Suporte out-of-the-box para **OpenAPI e Swagger UI**
- Tempo de inicialização extremamente rápido e baixo consumo de memória
- Suporte para desenvolvimento reativo com Mutiny
- Hot reload automático durante o desenvolvimento

No decorrer deste curso, exploraremos como implementar APIs RESTful robustas utilizando Quarkus,
aproveitando seu rico ecossistema de extensões e ferramentas.

O Quarkus simplifica a geração automática de documentação OpenAPI para suas APIs, permitindo que você se 
concentre na lógica de negócios enquanto mantém uma documentação sempre atualizada e acessível.

## Conclusão

A evolução dos WebServices até as APIs RESTful modernas com o padrão OpenAPI representa uma jornada de
simplificação, padronização e democratização da integração entre sistemas. Essa evolução continua com
frameworks modernos como o Quarkus, que trazem eficiência e produtividade para o desenvolvimento de APIs.

Nos próximos tópicos, começaremos a explorar na prática como consumir APIs existentes e como criar
nossas próprias APIs RESTful utilizando Quarkus.
