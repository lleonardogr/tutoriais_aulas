# Consumo de APIs RESTful com curl

O curl é uma ferramenta de linha de comando amplamente utilizada para transferir dados com URLs. É uma excelente opção para interagir com APIs RESTful diretamente do terminal, permitindo testar e entender como funcionam as APIs antes de implementar o consumo em uma aplicação.

## O que é o curl?

curl (Client URL) é uma biblioteca e ferramenta de linha de comando para transferir dados usando vários protocolos. É gratuita, open source, e está disponível para praticamente todos os sistemas operacionais. Algumas características importantes:

- Suporte a múltiplos protocolos (HTTP, HTTPS, FTP, etc.)
- Extremamente versátil e poderosa
- Disponível em praticamente qualquer ambiente Unix/Linux, macOS e Windows
- Perfeita para testar APIs RESTful
- Usada amplamente para automação e scripts

## Instalação do curl

Na maioria dos sistemas operacionais modernos, o curl já vem pré-instalado. Para verificar se você tem o curl instalado, abra o terminal e digite:

```bash
curl --version
```

Se não estiver instalado, você pode instalá-lo:

- **Ubuntu/Debian**: `sudo apt-get install curl`
- **macOS (Homebrew)**: `brew install curl`
- **Windows**: Baixe o instalador do [site oficial](https://curl.se/download.html) ou use o Windows Subsystem for Linux

## Operações Básicas com APIs RESTful

### GET - Recuperando Dados

O método HTTP GET é usado para solicitar dados de um recurso específico. É a operação mais simples e comum.

```bash
# Formato básico
curl https://api.exemplo.com/recursos

# Exemplo prático com uma API pública
curl https://jsonplaceholder.typicode.com/posts/1
```

Por padrão, o curl exibe a resposta no terminal. Para salvar a saída em um arquivo:

```bash
curl https://jsonplaceholder.typicode.com/posts/1 -o resposta.json
```

### POST - Criando Dados

O método HTTP POST é usado para enviar dados para criar um novo recurso.

```bash
# Enviando JSON
curl -X POST \
  -H "Content-Type: application/json" \
  -d '{"titulo": "Novo Post", "conteudo": "Conteúdo do post", "autorId": 1}' \
  https://jsonplaceholder.typicode.com/posts
```

Explicação dos parâmetros:
- `-X POST`: Especifica o método HTTP
- `-H "Content-Type: application/json"`: Define o cabeçalho para indicar que estamos enviando JSON
- `-d '{"campo": "valor"}'`: Define os dados do corpo da requisição

### PUT - Atualizando Dados (Substituição Completa)

O método HTTP PUT é usado para atualizar completamente um recurso existente.

```bash
curl -X PUT \
  -H "Content-Type: application/json" \
  -d '{"titulo": "Título Atualizado", "conteudo": "Conteúdo atualizado", "autorId": 1}' \
  https://jsonplaceholder.typicode.com/posts/1
```

### PATCH - Atualizando Dados (Atualização Parcial)

O método HTTP PATCH é usado para atualizar parcialmente um recurso existente.

```bash
curl -X PATCH \
  -H "Content-Type: application/json" \
  -d '{"titulo": "Apenas o Título foi Atualizado"}' \
  https://jsonplaceholder.typicode.com/posts/1
```

### DELETE - Excluindo Dados

O método HTTP DELETE é usado para remover um recurso.

```bash
curl -X DELETE https://jsonplaceholder.typicode.com/posts/1
```

## Recursos Avançados

### Tratamento de Respostas e Cabeçalhos

Para visualizar os cabeçalhos da resposta:

```bash
# Mostrar cabeçalhos da resposta
curl -i https://jsonplaceholder.typicode.com/posts/1

# Mostrar apenas cabeçalhos
curl -I https://jsonplaceholder.typicode.com/posts/1
```

Para visualizar detalhes completos da requisição e resposta (útil para debug):

```bash
curl -v https://jsonplaceholder.typicode.com/posts/1
```

### Enviando Parâmetros de Consulta (Query Parameters)

```bash
# Básico
curl "https://jsonplaceholder.typicode.com/posts?userId=1"

# Múltiplos parâmetros
curl "https://jsonplaceholder.typicode.com/posts?userId=1&_sort=id&_order=desc"

# Utilizando --data-urlencode para codificar corretamente caracteres especiais
curl --get --data-urlencode "q=termo de pesquisa" https://api.exemplo.com/search
```

### Autenticação

#### Autenticação Básica

```bash
curl -u username:password https://api.exemplo.com/recurso-protegido
```

#### Autenticação com Token (OAuth 2.0, JWT)

```bash
curl -H "Authorization: Bearer seu_token_aqui" https://api.exemplo.com/recurso-protegido
```

### Enviando Formulários

```bash
# Formulário simples
curl -X POST -d "nome=João&email=joao@exemplo.com" https://api.exemplo.com/contato

# Upload de arquivo
curl -X POST -F "arquivo=@/caminho/para/arquivo.pdf" -F "descricao=Meu arquivo" https://api.exemplo.com/upload
```

### Lidando com Cookies

```bash
# Salvar cookies recebidos
curl -c cookies.txt https://api.exemplo.com/login

# Enviar cookies em uma requisição
curl -b cookies.txt https://api.exemplo.com/area-restrita
```

### Formatando Respostas JSON

Você pode usar ferramentas como `jq` para formatar e filtrar respostas JSON:

```bash
# Instalar jq (Ubuntu/Debian)
# sudo apt-get install jq

# Formatar JSON
curl https://jsonplaceholder.typicode.com/posts/1 | jq

# Filtrar campos específicos
curl https://jsonplaceholder.typicode.com/posts/1 | jq '.titulo'
```

## Exemplos Práticos com APIs Públicas

### Consultando Informações sobre o Clima

```bash
curl "https://api.openweathermap.org/data/2.5/weather?q=São%20Paulo&appid=SUA_CHAVE_API&units=metric&lang=pt_br"
```

### Buscando Informações sobre Livros

```bash
curl "https://www.googleapis.com/books/v1/volumes?q=Duna+autor:Frank+Herbert"
```

### Consultando Informações sobre Filmes

```bash
curl "http://www.omdbapi.com/?t=Star%20Wars&apikey=SUA_CHAVE_API"
```

## Criando Scripts com curl

Você pode criar scripts shell com curl para automação de tarefas:

```bash
#!/bin/bash

# Exemplo de script que busca posts e mostra apenas os títulos
response=$(curl -s https://jsonplaceholder.typicode.com/posts)
echo "$response" | jq '.[].title'
```

## Boas Práticas e Dicas

1. **Use aspas duplas para URLs** com parâmetros de consulta para evitar problemas com caracteres especiais
2. **Utilize a opção `-s` (silent)** para suprimir a barra de progresso em scripts
3. **Defina timeout** para evitar que requisições fiquem pendentes por muito tempo: `curl --connect-timeout 10`
4. **Siga redirecionamentos** com a opção `-L`: `curl -L https://site-com-redirecionamento.com`
5. **Use `-w` para formatar a saída** de informações como tempo de resposta:
   ```bash
   curl -s -w "\nTempo Total: %{time_total}s\n" https://api.exemplo.com/
   ```

## Ferramentas Alternativas

Embora o curl seja extremamente poderoso, algumas alternativas podem oferecer recursos adicionais:

- **HTTPie**: Sintaxe mais amigável e saída colorida
- **Postman**: Interface gráfica para testes de API
- **Insomnia**: Outra opção de interface gráfica
- **wget**: Focado em download de arquivos, mas também pode ser usado para APIs

## Conclusão

O curl é uma ferramenta essencial para qualquer desenvolvedor que trabalha com APIs RESTful. Dominar seus recursos permite testar e compreender APIs de forma eficiente antes de implementar o consumo dessas APIs em suas aplicações.

No próximo tópico, vamos explorar como criar nossa primeira API RESTful usando o Quarkus, onde você poderá aplicar os conhecimentos adquiridos sobre o funcionamento de APIs.
