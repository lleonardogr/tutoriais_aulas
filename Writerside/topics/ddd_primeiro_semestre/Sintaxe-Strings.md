# Sintaxe: Strings

## 1. Conceitos Básicos de Strings em Java

### 1.1. Declaração e Inicialização de Strings

Em Java, as **Strings** são objetos da classe `java.lang.String`. Existem várias maneiras de declarar e inicializar strings:

```java
// Declaração e inicialização direta
String saudacao = "Olá, Mundo!";

// Usando o construtor da classe String
String vazio = new String();

// Concatenando strings
String nome = "João";
String mensagem = "Olá, " + nome + "!";
```

Observação: Embora seja possível usar o construtor new String(), é recomendado utilizar literais de string ("texto") para aproveitar o String Pool.

### 1.2. Imutabilidade das Strings

As Strings em Java são imutáveis, ou seja, uma vez criadas, seu conteúdo não pode ser alterado. Qualquer operação que pareça modificar uma string, na verdade, cria uma nova instância.

```java
String original = "Java";
String modificada = original.concat(" é legal");

System.out.println(original);    // Saída: Java
System.out.println(modificada);  // Saída: Java é legal
```

1.3. String Pool

Java mantém um String Pool para otimizar o uso de memória. Quando uma string é criada usando um literal, ela é armazenada no pool. Se uma string com o mesmo conteúdo já existir no pool, a referência é reutilizada.

```java
String s1 = "Hello";
String s2 = "Hello";
String s3 = new String("Hello");

System.out.println(s1 == s2); // true
System.out.println(s1 == s3); // false

	•	s1 e s2 referenciam a mesma instância no String Pool.
	•	s3 é uma nova instância na heap, independente do pool.
```

## 2. Métodos Comuns da Classe String

A classe String oferece uma ampla gama de métodos para manipulação de texto. A seguir, destacamos alguns dos mais utilizados.

### 2.1. Manipulação de Caracteres

	•	charAt(int index): Retorna o caractere na posição especificada.

```java
String texto = "Java";
char letra = texto.charAt(1); // 'a'
```

	•	length(): Retorna o comprimento da string.

```java
int tamanho = texto.length(); // 4
```

	•	toUpperCase() e toLowerCase(): Convertem a string para maiúsculas ou minúsculas.

```java
String maiuscula = texto.toUpperCase(); // "JAVA"
String minuscula = texto.toLowerCase(); // "java"
```

### 2.2. Comparação de Strings

	•	equals(Object obj): Compara o conteúdo das strings.

```java
String s1 = "Java";
String s2 = "Java";
String s3 = new String("Java");

System.out.println(s1.equals(s2)); // true
System.out.println(s1.equals(s3)); // true
```

	•	equalsIgnoreCase(String anotherString): Compara ignorando maiúsculas e minúsculas.

```java
String s4 = "java";
System.out.println(s1.equalsIgnoreCase(s4)); // true
```

	•	compareTo(String anotherString): Compara lexicograficamente.

```java
String a = "Apple";
String b = "Banana";

System.out.println(a.compareTo(b)); // Negativo (Apple < Banana)
System.out.println(b.compareTo(a)); // Positivo (Banana > Apple)
System.out.println(a.compareTo("Apple")); // 0
```


## 2.3. Pesquisa e Substituição

	•	contains(CharSequence s): Verifica se a string contém a sequência especificada.

```java
String frase = "Java é uma linguagem poderosa.";
boolean contem = frase.contains("linguagem"); // true
```

	•	startsWith(String prefix) e endsWith(String suffix): Verificam o início ou o fim da string.

```java
frase.startsWith("Java"); // true
frase.endsWith("poderosa."); // true
```

	•	indexOf(String str): Retorna o índice da primeira ocorrência da substring.

```java
int pos = frase.indexOf("uma"); // 10
```

	•	replace(CharSequence target, CharSequence replacement): Substitui todas as ocorrências da sequência alvo pela de substituição.

```java
String novaFrase = frase.replace("poderosa", "eficiente");
// "Java é uma linguagem eficiente."
```


### 2.4. Divisão e Extração

	•	substring(int beginIndex) e substring(int beginIndex, int endIndex): Extraem partes da string.

```java
String texto = "Programação";
String sub1 = texto.substring(0, 4); // "Prog"
String sub2 = texto.substring(5);    // "ramação"
```

	•	split(String regex): Divide a string com base em um delimitador regex.

```java
String csv = "maçã,banana,laranja";
String[] frutas = csv.split(",");
// ["maçã", "banana", "laranja"]

```

### 2.5. Conversão de Tipos

	•	toCharArray(): Converte a string em um array de caracteres.

```java
char[] caracteres = texto.toCharArray();
```

	•	valueOf(): Converte diferentes tipos de dados para string.

```java
int numero = 100;
String strNumero = String.valueOf(numero); // "100"
```


### 2.6. Formatação de Strings

	•	String.format(String format, Object... args): Formata a string com base no especificador de formato.

```java
String nome = "João";
int idade = 30;
String mensagem = String.format("Meu nome é %s e tenho %d anos.", nome, idade);
// "Meu nome é João e tenho 30 anos."
```

	•	printf(String format, Object... args): Imprime a string formatada diretamente no console.

```java
System.out.printf("O preço do produto é %.2f reais.\n", 49.99);
// "O preço do produto é 49.99 reais."
```


## 3. Strings Mutáveis: StringBuilder e StringBuffer

Enquanto a classe String é imutável, Java fornece classes como StringBuilder e StringBuffer para manipulação eficiente de strings mutáveis.

### 3.1. StringBuilder

A classe StringBuilder é usada para criar e modificar strings de forma eficiente, especialmente em cenários que envolvem muitas modificações.

	•	Vantagens:
	•	Performance: Mais eficiente do que String para operações de concatenação.
	•	Mutabilidade: Permite alterações sem criar novas instâncias.
	•	Exemplo:

```java
StringBuilder sb = new StringBuilder("Olá");
sb.append(", ");
sb.append("Mundo!");

String resultado = sb.toString(); // "Olá, Mundo!"
System.out.println(resultado);
```


### 3.2. StringBuffer

A classe StringBuffer é similar a StringBuilder, mas é sincronizada, tornando-a thread-safe.

	•	Vantagens:
	•	Thread Safety: Seguro para uso em ambientes multi-thread.
	•	Desvantagens:
	•	Performance: Mais lenta que StringBuilder devido à sincronização.
	•	Exemplo:

```java
StringBuffer sb = new StringBuffer("Java");
sb.insert(4, " é ");
sb.append("fantástico!");

String resultado = sb.toString(); // "Java é fantástico!"
System.out.println(resultado);
```


## 4. Concatenação de Strings

A concatenação de strings é uma operação comum em programação. Em Java, existem várias maneiras de concatenar strings:

### 4.1. Usando o Operador +

```java
String a = "Hello";
String b = "World";
String c = a + " " + b + "!"; // "Hello World!"
```

Observação: O operador + é conveniente, mas pode ser ineficiente para múltiplas concatenações devido à criação de várias instâncias de String.

### 4.2. Usando concat()

```java

String a = "Hello";
String b = "World";
String c = a.concat(" ").concat(b).concat("!"); // "Hello World!"
```

Observação: Semelhante ao operador +, mas menos legível.

### 4.3. Usando StringBuilder ou StringBuffer

```java
StringBuilder sb = new StringBuilder();
sb.append("Hello");
sb.append(" ");
sb.append("World");
sb.append("!");
String resultado = sb.toString(); // "Hello World!"
```

Vantagens:

	•	Eficiência: Melhor performance em múltiplas concatenações.
	•	Flexibilidade: Métodos adicionais para manipulação.

### 4.4. Usando String.join()

```java
String[] palavras = {"Hello", "World"};
String resultado = String.join(" ", palavras) + "!"; // "Hello World!"
```

Vantagens:

	•	Legibilidade: Código mais conciso e claro.
	•	Flexibilidade: Pode juntar arrays ou coleções.

### 4.5. Usando Streams (Java 8+)

```java
List<String> palavras = Arrays.asList("Hello", "World");
String resultado = palavras.stream().collect(Collectors.joining(" ")) + "!"; // "Hello World!"
```

Vantagens:

	•	Funcional: Estilo declarativo de programação.
	•	Flexibilidade: Fácil de integrar com outras operações de stream.

## 5. Expressões Regulares com Strings

Java suporta Expressões Regulares (Regex), permitindo buscas e manipulações complexas em strings.

### 5.1. Métodos Utilitários

	•	matches(String regex): Verifica se toda a string corresponde ao regex.

```java
String email = "usuario@example.com";
boolean valido = email.matches("^[A-Za-z0-9+_.-]+@(.+)$"); // true
```

	•	replaceAll(String regex, String replacement): Substitui todas as ocorrências que correspondem ao regex.

```java
String texto = "Java 1.8 é bom, mas Java 11 é melhor.";
String novoTexto = texto.replaceAll("Java \\d+\\.\\d+", "Java");
// "Java é bom, mas Java é melhor."
```

	•	split(String regex): Divide a string com base no regex.

```java
String numeros = "1,2;3|4:5";
String[] partes = numeros.split("[,;|:]"); // ["1", "2", "3", "4", "5"]
```


### 5.2. Usando a Classe Pattern e Matcher

Para operações mais avançadas, utilize as classes Pattern e Matcher.

```java
import java.util.regex.Pattern;
import java.util.regex.Matcher;

String texto = "A casa custa 3500 reais.";
Pattern pattern = Pattern.compile("\\d+");
Matcher matcher = pattern.matcher(texto);

while (matcher.find()) {
    System.out.println("Encontrado: " + matcher.group()); // Saída: Encontrado: 3500
}
```

Vantagens:

	•	Controle: Maior flexibilidade e controle sobre a busca.
	•	Reutilização: Compilar padrões que podem ser reutilizados.

## 6. Unicode e Encoding em Strings

Java utiliza Unicode para representar caracteres, garantindo suporte a uma ampla gama de idiomas e símbolos.

### 6.1. Representação de Caracteres Unicode

	•	Escape Unicode: Use \uXXXX para representar caracteres específicos.

```java
String smile = "\u263A"; // ☺
System.out.println(smile); // Saída: ☺
```

### 6.2. Codificação e Decodificação

Embora as strings sejam armazenadas como Unicode, ao interagir com arquivos ou redes, é importante considerar a codificação correta (como UTF-8).

```java
import java.nio.charset.StandardCharsets;

String texto = "Olá, Mundo!";
byte[] bytes = texto.getBytes(StandardCharsets.UTF_8);
String recuperado = new String(bytes, StandardCharsets.UTF_8);
System.out.println(recuperado); // "Olá, Mundo!"
```

Dicas:

	•	Consistência: Use a mesma codificação ao ler e escrever dados.
	•	Especificação Explícita: Sempre especifique a codificação para evitar problemas de portabilidade.

## 7. Boas Práticas no Uso de Strings

### 7.1. Evitar Uso Excessivo do Operador +

Para múltiplas concatenações, prefira StringBuilder ou métodos como String.join() para melhorar a performance.

### 7.2. Preferir Métodos Imutáveis

A imutabilidade das strings ajuda a evitar efeitos colaterais e facilita o gerenciamento de memória.

### 7.3. Utilizar String Pool

Aproveite o String Pool utilizando literais de string em vez de criar novas instâncias com new String().

### 7.4. Tratar Nulls Adequadamente

Evite NullPointerException ao manipular strings. Utilize verificações ou métodos como Objects.toString().

String nome = null;
System.out.println(Objects.toString(nome, "Desconhecido")); // "Desconhecido"

### 7.5. Validar Inputs

Sempre valide e sanitize inputs quando trabalhar com strings provenientes de fontes externas para evitar vulnerabilidades como Injection.

# 8. Exemplos Práticos

### 8.1. Verificação de Palíndromo

```java
public class Palindromo {
    public static boolean isPalindromo(String str) {
        String limpa = str.replaceAll("[^a-zA-Z0-9]", "").toLowerCase();
        String invertida = new StringBuilder(limpa).reverse().toString();
        return limpa.equals(invertida);
    }

    public static void main(String[] args) {
        String texto = "A man, a plan, a canal: Panama";
        System.out.println(isPalindromo(texto)); // true
    }
}
```

### 8.2. Contagem de Palavras em uma String

```java
public class ContadorPalavras {
    public static int contarPalavras(String frase) {
        if (frase == null || frase.isEmpty()) {
            return 0;
        }
        String[] palavras = frase.trim().split("\\s+");
        return palavras.length;
    }

    public static void main(String[] args) {
        String frase = "Java é uma linguagem de programação poderosa.";
        System.out.println(contarPalavras(frase)); // 6
    }
}
```

### 8.3. Formatação de Data e Hora

```java
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;

public class FormatarDataHora {
    public static void main(String[] args) {
        LocalDateTime agora = LocalDateTime.now();
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("dd/MM/yyyy HH:mm:ss");
        String formatada = agora.format(formatter);
        System.out.println("Data e Hora: " + formatada); // Exemplo: "Data e Hora: 25/04/2024 14:30:45"
    }
}
```

### 8.4. Manipulação de CSV

```Java
public class ManipularCSV {
    public static void main(String[] args) {
        String csv = "Nome,Idade,Email\nJoão,30,joao@example.com\nMaria,25,maria@example.com";
        String[] linhas = csv.split("\n");

        for (String linha : linhas) {
            String[] campos = linha.split(",");
            System.out.printf("Nome: %s, Idade: s, Email: sn", campos[0], campos[1], campos[2]);
        }
    }
}

//Saída:

//Nome: Nome, Idade: Idade, Email: Email
//Nome: João, Idade: 30, Email: joao@example.com
//Nome: Maria, Idade: 25, Email: maria@example.com
```

## Conclusão

As Strings são fundamentais no desenvolvimento de aplicações Java, fornecendo a base para manipulação de texto e comunicação. Compreender a classe String, suas características como imutabilidade e o String Pool, bem como conhecer métodos e classes auxiliares como StringBuilder e StringBuffer, é essencial para escrever código eficiente e eficaz.

Este guia apresentou uma visão abrangente sobre como strings funcionam em Java, desde conceitos básicos até técnicas avançadas de manipulação. Além disso, foram abordadas boas práticas que ajudam a evitar armadilhas comuns e a otimizar o desempenho das aplicações.

Dicas Finais:

	•	Pratique Regularmente: A melhor maneira de dominar a manipulação de strings é aplicá-la em projetos reais.
	•	Mantenha-se Atualizado: Java continua a evoluir, trazendo novas funcionalidades e melhorias. Acompanhe as novidades para aproveitar ao máximo a linguagem.
	•	Escreva Código Legível: Utilize métodos que promovam a legibilidade e mantenha o código limpo e organizado.
	•	Otimize com Cuidado: Embora classes como StringBuilder ofereçam performance melhor, use-as quando realmente necessárias para evitar complicações desnecessárias.

Esperamos que este guia tenha sido útil e que você se sinta mais confiante ao trabalhar com strings em Java!

