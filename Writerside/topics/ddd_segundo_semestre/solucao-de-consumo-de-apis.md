# Solução de Consumo de APIs e Exportação de Dados
## Dependências
Inclua no seu arquivo pom.xml:

``` xml
<!-- Para Jackson -->
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.12.1</version>
</dependency>
```

## Classes de Utilidade
### HttpClientUtil
``` java
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.net.URI;

public class HttpClientUtil {
    public static String sendGET(String url) throws Exception {
        HttpClient client = HttpClient.newHttpClient();
        HttpRequest request = HttpRequest.newBuilder()
                .uri(new URI(url))
                .GET()
                .build();
        
        HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
        return response.body();
    }
}
```

### FileUtil
``` java
import java.nio.file.Files;
import java.nio.file.Path;

public class FileUtil {
    public static void saveToFile(String data, String filename) throws Exception {
        Files.writeString(Path.of(filename), data);
    }
}
````

Classes de Resposta
DogApiResponse
```java
public class DogApiResponse {
    private String message;
    private String status;
    
    // getters and setters
}
```
WeatherResponse

```java
public class WeatherResponse {
    public class Main {
        private double temp;
        // getters and setters
    }
    
    private Main main;
    // getters and setters
}
```
## Serviços
### DogAPI
```java
public class DogAPI {
    private static final String DOG_API_URL = "https://dog.ceo/api/breeds/image/random";

    public static DogApiResponse getRandomDogImage() throws Exception {
        String response = HttpClientUtil.sendGET(DOG_API_URL);
        return new ObjectMapper().readValue(response, DogApiResponse.class);
    }
}
````

OpenWeatherAPI
```java
public class OpenWeatherAPI {
    private static final String API_KEY = "YOUR_API_KEY";
    private static final String WEATHER_API_URL = "https://api.openweathermap.org/data/2.5/weather?q=%s&units=metric&appid=" + API_KEY;

    public static WeatherResponse getWeatherInfo(String city) throws Exception {
        String formattedURL = String.format(WEATHER_API_URL, city);
        String response = HttpClientUtil.sendGET(formattedURL);
        return new ObjectMapper().readValue(response, WeatherResponse.class);
    }
}
```

### WeatherEvaluator
``` java
public class WeatherEvaluator {

    public static String evaluateTemperatureForDogWalk(double temperature) {
        if (temperature > 15 && temperature < 30) {
            return "A temperatura de " + temperature + "°C está boa para passear com o cachorro.";
        } else {
            return "A temperatura de " + temperature + "°C não é recomendada para passear com o cachorro.";
        }
    }
}
```
## Aplicativo Principal
``` java
import java.util.Scanner;

public class MainApp {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        
        try {
            System.out.println("Obtendo uma imagem aleatória de cachorro...");
            DogApiResponse dogData = DogAPI.getRandomDogImage();
            System.out.println("URL da Imagem: " + dogData.getMessage());

            System.out.print("Digite o nome da cidade para obter informações meteorológicas: ");
            String city = scanner.nextLine();
            WeatherResponse weatherData = OpenWeatherAPI.getWeatherInfo(city);
            System.out.println("Temperatura em " + city + ": " + weatherData.getMain().getTemp() + "°C");

            String temperatureEvaluation = WeatherEvaluator.evaluateTemperatureForDogWalk(weatherData.getMain().getTemp());
            System.out.println(temperatureEvaluation);

            String dataToSave = "URL da Imagem do Cachorro: " + dogData.getMessage() + "\n\nInformações meteorológicas de " + city + ":\nTemperatura: " + weatherData.getMain().getTemp() + "°C\n" + temperatureEvaluation;
            FileUtil.saveToFile(dataToSave, "results.txt");
            System.out.println("Dados salvos em results.txt!");

        } catch (Exception e) {
            System.out.println("Erro ao buscar dados: " + e.getMessage());
        }

        scanner.close();
    }
}
``````