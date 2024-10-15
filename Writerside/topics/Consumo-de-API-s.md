# Tutorial: Consumo de API&apos;s usando HttpCliente e OkHttp

## Introdução

Consumir APIs é uma tarefa comum no desenvolvimento de aplicações modernas. Em Java, existem várias bibliotecas que facilitam essa tarefa, como o `HttpClient`, introduzido no Java 11, e a biblioteca de terceiros `OkHttp`. Este guia irá demonstrar como utilizar ambas as bibliotecas para consumir:

- **Uma API aberta**: [Scryfall API](https://scryfall.com/docs/api), que fornece dados sobre cartas de Magic: The Gathering.
- **Uma API que requer chave de acesso (API Key)**: [OpenWeather API](https://openweathermap.org/api), que fornece dados meteorológicos.


## 1. Configurando o Ambiente

### Requisitos

- **Java 11 ou superior**: O `HttpClient` foi introduzido no Java 11.
- **Maven ou Gradle**: Para gerenciar dependências, especialmente ao usar o `OkHttp`.

### Adicionando Dependências

Para usar o `OkHttp`, adicione a dependência ao seu arquivo `pom.xml` (Maven):

```xml
<dependency>
    <groupId>com.squareup.okhttp3</groupId>
    <artifactId>okhttp</artifactId>
    <version>4.10.0</version>
</dependency>
```

Ou no build.gradle (Gradle):

implementation 'com.squareup.okhttp3:okhttp:4.10.0'

## 2. Usando HttpClient

O HttpClient é uma API moderna para realizar requisições HTTP de forma assíncrona ou síncrona.

### 2.1. Consumindo a API Scryfall

A Scryfall API é uma API RESTful que não requer autenticação. Vamos buscar informações sobre uma carta específica.

Exemplo: Buscar a carta “Black Lotus”

```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class ScryfallExample {
    public static void main(String[] args) throws Exception {
        String cardName = "Black Lotus";
        String url = "https://api.scryfall.com/cards/named?fuzzy=" + cardName.replace(" ", "+");

        HttpClient client = HttpClient.newHttpClient();
        HttpRequest request = HttpRequest.newBuilder()
            .uri(URI.create(url))
            .GET()
            .build();

        HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());

        System.out.println("Status Code: " + response.statusCode());
        System.out.println("Response Body:\n" + response.body());
    }
}
```

Explicação:

	•	Criamos um HttpClient usando o método newHttpClient().
	•	Construímos uma HttpRequest com a URL da API.
	•	Enviamos a requisição e recebemos uma HttpResponse.
	•	Imprimimos o status e o corpo da resposta.

2.2. Consumindo a API OpenWeather

A OpenWeather API requer uma chave de API para autenticação. Você precisa se registrar em OpenWeather e obter uma chave.

Exemplo: Obter o clima atual de uma cidade
```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.util.Scanner;

public class OpenWeatherExample {
    public static void main(String[] args) throws Exception {
        String apiKey = "SUA_API_KEY_AQUI"; // Substitua pela sua chave de API
        String city = "São Paulo";
        String url = "https://api.openweathermap.org/data/2.5/weather?q=" + 
                      city.replace(" ", "%20") + 
                      "&appid=" + apiKey + 
                      "&lang=pt&units=metric";

        HttpClient client = HttpClient.newHttpClient();
        HttpRequest request = HttpRequest.newBuilder()
            .uri(URI.create(url))
            .GET()
            .build();

        HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());

        if(response.statusCode() == 200) {
            System.out.println("Dados do clima em " + city + ":\n" + response.body());
        } else {
            System.out.println("Erro: " + response.statusCode());
        }
    }
}
```
Explicação:

	•	Substitua "SUA_API_KEY_AQUI" pela sua chave de API.
	•	Construímos a URL incluindo a cidade, chave de API, idioma (lang) e unidades (units).
	•	Verificamos o código de status para lidar com erros.

## 3. Usando OkHttp

O OkHttp é uma biblioteca de cliente HTTP eficiente para Java e Android.

### 3.1. Consumindo a API Scryfall

Exemplo: Buscar a carta “Black Lotus”

```java
import okhttp3.OkHttpClient;
import okhttp3.Request;
import okhttp3.Response;

public class ScryfallOkHttpExample {
    public static void main(String[] args) throws Exception {
        String cardName = "Black Lotus";
        String url = "https://api.scryfall.com/cards/named?fuzzy=" + cardName.replace(" ", "+");

        OkHttpClient client = new OkHttpClient();

        Request request = new Request.Builder()
            .url(url)
            .build();

        try (Response response = client.newCall(request).execute()) {
            if(response.isSuccessful()) {
                String responseBody = response.body().string();
                System.out.println("Resposta:\n" + responseBody);
            } else {
                System.out.println("Erro: " + response.code());
            }
        }
    }
}
```

Explicação:

	•	Criamos um OkHttpClient.
	•	Construímos um Request com a URL desejada.
	•	Executamos a chamada e obtemos um Response.
	•	Utilizamos um bloco try-with-resources para fechar automaticamente a resposta.

### 3.2. Consumindo a API OpenWeather

Exemplo: Obter o clima atual de uma cidade

```Java
import okhttp3.OkHttpClient;
import okhttp3.Request;
import okhttp3.Response;

public class OpenWeatherOkHttpExample {
    public static void main(String[] args) throws Exception {
        String apiKey = "SUA_API_KEY_AQUI"; // Substitua pela sua chave de API
        String city = "São Paulo";
        String url = "https://api.openweathermap.org/data/2.5/weather?q=" +
                      city.replace(" ", "%20") +
                      "&appid=" + apiKey +
                      "&lang=pt&units=metric";

        OkHttpClient client = new OkHttpClient();

        Request request = new Request.Builder()
            .url(url)
            .build();

        try (Response response = client.newCall(request).execute()) {
            if(response.isSuccessful()) {
                String responseBody = response.body().string();
                System.out.println("Dados do clima em " + city + ":\n" + responseBody);
            } else {
                System.out.println("Erro: " + response.code());
            }
        }
    }
}
```

Explicação:

	•	Similar ao exemplo anterior, substitua "SUA_API_KEY_AQUI" pela sua chave de API.
	•	Usamos o OkHttpClient para realizar a requisição GET.
	•	Tratamos a resposta verificando se a requisição foi bem-sucedida.

## 4. Boas Práticas

	•	Gerenciamento de Exceções: Sempre trate possíveis exceções, especialmente em operações de rede.
	•	Chaves de API: Nunca exponha suas chaves de API em repositórios públicos. Utilize variáveis de ambiente ou arquivos de configuração seguros.
	•	Parsing de JSON: Utilize bibliotecas como Jackson ou Gson para converter o JSON de resposta em objetos Java.
	•	Timeouts: Configure timeouts para evitar que a aplicação fique aguardando indefinidamente.
	•	Pooling de Conexões: Reutilize instâncias de HttpClient ou OkHttpClient para melhorar a performance.

Observação: Certifique-se de substituir "SUA_API_KEY_AQUI" pela sua chave de API real ao testar os exemplos com o OpenWeather.

