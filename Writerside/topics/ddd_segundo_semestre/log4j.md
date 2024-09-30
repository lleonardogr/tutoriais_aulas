
# IO: Log4J

## O que é Log4j?

Log4j é uma biblioteca de logging popular em Java que permite aos desenvolvedores registrar mensagens de log em vários destinos, como arquivos, consoles, sockets de rede e muito mais. Log4j é flexível e configurável, tornando-o uma escolha ideal para adicionar logging a aplicações Java.

## Vantagens de Usar Log4j

- **Flexibilidade**: Diferentes níveis de log e múltiplos destinos de log.
- **Desempenho**: Projetado para ser rápido e ter um pequeno impacto no desempenho.
- **Configurável**: Configurações através de arquivos XML, JSON, YAML ou propriedades.

## Desvantagens de Usar Log4j

- **Configuração**: Pode ser complexo configurar corretamente.
- **Curva de aprendizado**: Requer algum tempo para aprender e utilizar efetivamente.
- **Dependência**: Adiciona uma dependência externa ao seu projeto.

## Etapa 1: Configurando Log4j

Primeiro, você precisa adicionar a dependência Log4j ao seu projeto. Se estiver usando Maven, adicione o seguinte ao seu `pom.xml`:

```xml
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-core</artifactId>
    <version>2.14.1</version> <!-- Use a versão mais recente -->
</dependency>
```

Crie um arquivo `log4j2.xml` na pasta `src/main/resources` do seu projeto com a seguinte configuração básica:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<Configuration status="WARN">
    <Appenders>
        <File name="File" fileName="app.log" append="true">
            <PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss} [%t] %-5level %logger{36} - %msg %n" />
        </File>
        <Console name="Console" target="SYSTEM_OUT">
            <PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss} [%t] %-5level %logger{36} - %msg %n" />
        </Console>
    </Appenders>
    <Loggers>
        <Root level="info">
            <AppenderRef ref="File" />
            <AppenderRef ref="Console" />
        </Root>
    </Loggers>
</Configuration>
```

Na classe `main`, podemos testar o log4j, verificando se o arquivo será criado ou o console irá mostrar os registros de log.

```Java
import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;

import java.util.Arrays;
import java.util.Scanner;

public class Main {
    public static Logger logger = LogManager.getLogger(Main.class);
    public static void main(String[] args){
        try{
            logger.info("Sistema iniciando...");

            var scanner = new Scanner(System.in);
            System.out.println("Digite um numero:");
            int numero = scanner.nextInt();
        }
        catch (Exception e){
            logger.fatal("Erro fatal: " + e.getMessage() + " - " +
                    Arrays.toString(e.getStackTrace()));
        }
    }
}
```

![logfile.png](logfile.png)

## Etapa 2: Implementando Logging com Interfaces e Generics

Podemos criar uma interface genérica com métodos de logging padrão e depois implementá-la em nossas classes.

```java
public interface Loggable<T> {
    Logger LOGGER = LogManager.getLogger(Loggable.class);

    default void logInfo(T message) {
        LOGGER.info(message);
    }

    default void logError(T message) {
        LOGGER.error(message);
    }

    default void logDebug(T message) {
        LOGGER.debug(message);
    }
}
```

## Etapa 3: Usando a Interface Loggable

Agora, suas classes podem implementar `Loggable<String>` e usar métodos de log diretamente.

```java
public class UserRepository implements Loggable<String> {
    public void addUser(String user) {
        logInfo("Adding user: " + user);
        // Código para adicionar usuário
    }

    public void removeUser(String user) {
        logInfo("Removing user: " + user);
        // Código para remover usuário
    }
}
```

## Conclusão

Usar Log4j com interfaces e generics em Java pode aumentar a reusabilidade do código de logging e manter sua aplicação bem organizada e mais fácil de manter. Apesar de algumas desvantagens, os benefícios de ter um sistema de logging robusto como o Log4j superam os contras.

## Referências

- [Log4j 2 Apache](https://logging.apache.org/log4j/2.x/)
- [Baeldung Log4j 2 Tutorial](https://www.baeldung.com/log4j2)
