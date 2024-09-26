# Enums (Enumeradores)

## Introdução

Em Java, os **enumeradores** (ou **Enums**) são um tipo de dado especial que permite definir um conjunto fixo de constantes nomeadas. Eles são utilizados para representar um grupo de valores constantes e facilitar a legibilidade e manutenção do código.

Este guia explora o conceito de enumeradores em Java, como declará-los e utilizá-los, suas características avançadas, e fornece exemplos práticos para ilustrar seu uso.

---

## O que são Enumeradores?

Um **Enum** é um tipo de dado que consiste em um conjunto fixo de constantes, conhecidas como **constantes enum**, que são, por padrão, públicas, estáticas e finais. Enums são tipicamente usados quando se trabalha com valores que não mudam, como dias da semana, direções, estados de um processo, etc.

### Características dos Enums

- **Tipo Seguro**: Enums fornecem segurança de tipo, evitando valores inválidos.
- **Legibilidade**: Melhoram a legibilidade do código ao usar nomes significativos.
- **Funcionalidades Adicionais**: Podem conter métodos, construtores e atributos.
- **Imutabilidade**: As constantes são imutáveis e não podem ser modificadas após a criação.

### Sintaxe Básica

```java
public enum NomeDoEnum {
    CONSTANTE1,
    CONSTANTE2,
    CONSTANTE3
}
```

---

## Criando e Usando Enums

### Exemplo 1: Dias da Semana

```java
public enum DiaDaSemana {
    SEGUNDA,
    TERCA,
    QUARTA,
    QUINTA,
    SEXTA,
    SABADO,
    DOMINGO
}

public class ExemploEnum {
    public static void main(String[] args) {
        DiaDaSemana hoje = DiaDaSemana.SEGUNDA;

        if (hoje == DiaDaSemana.SEGUNDA) {
            System.out.println("Hoje é segunda-feira!");
        } else {
            System.out.println("Hoje não é segunda-feira.");
        }
    }
}
```

**Saída**:

```
Hoje é segunda-feira!
```

---

## Métodos e Funcionalidades Padrão

### Método `values()`

Retorna um array contendo todas as constantes do Enum.

```java
for (DiaDaSemana dia : DiaDaSemana.values()) {
    System.out.println(dia);
}
```

**Saída**:

```
SEGUNDA
TERCA
QUARTA
QUINTA
SEXTA
SABADO
DOMINGO
```

### Método `valueOf(String name)`

Retorna a constante do Enum com o nome especificado.

```java
DiaDaSemana dia = DiaDaSemana.valueOf("QUARTA");
System.out.println(dia);
```

**Saída**:

```
QUARTA
```

### Método `ordinal()`

Retorna a posição ordinal da constante, começando em zero.

```java
DiaDaSemana dia = DiaDaSemana.SEXTA;
System.out.println(dia.ordinal());
```

**Saída**:

```
4
```

---

## Enums com Atributos e Métodos

Os Enums em Java não se limitam a constantes simples; eles podem ter atributos, construtores e métodos, permitindo comportamentos mais complexos.

### Exemplo 2: Códigos de Países com Código Telefônico

```java
public enum Pais {
    BRASIL("Brasil", "+55"),
    ESTADOS_UNIDOS("Estados Unidos", "+1"),
    PORTUGAL("Portugal", "+351");

    private String nome;
    private String codigoTelefone;

    // Construtor
    Pais(String nome, String codigoTelefone) {
        this.nome = nome;
        this.codigoTelefone = codigoTelefone;
    }

    // Métodos getters
    public String getNome() {
        return nome;
    }

    public String getCodigoTelefone() {
        return codigoTelefone;
    }
}

public class ExemploPaisEnum {
    public static void main(String[] args) {
        for (Pais pais : Pais.values()) {
            System.out.println("País: " + pais.getNome() + ", Código: " + pais.getCodigoTelefone());
        }
    }
}
```

**Saída**:

```
País: Brasil, Código: +55
País: Estados Unidos, Código: +1
País: Portugal, Código: +351
```

---

## Enums com Comportamentos Específicos

É possível definir métodos abstratos dentro do Enum e fornecer implementações específicas para cada constante.

### Exemplo 3: Operações Matemáticas

```java
public enum Operacao {
    SOMA {
        @Override
        public double calcular(double x, double y) {
            return x + y;
        }
    },
    SUBTRACAO {
        @Override
        public double calcular(double x, double y) {
            return x - y;
        }
    },
    MULTIPLICACAO {
        @Override
        public double calcular(double x, double y) {
            return x * y;
        }
    },
    DIVISAO {
        @Override
        public double calcular(double x, double y) {
            return x / y;
        }
    };

    // Método abstrato
    public abstract double calcular(double x, double y);
}
```

### Uso em Código

```java
public class ExemploOperacaoEnum {
    public static void main(String[] args) {
        double x = 10;
        double y = 5;

        for (Operacao op : Operacao.values()) {
            System.out.println(x + " " + op.name() + " " + y + " = " + op.calcular(x, y));
        }
    }
}
```

**Saída**:

```
10.0 SOMA 5.0 = 15.0
10.0 SUBTRACAO 5.0 = 5.0
10.0 MULTIPLICACAO 5.0 = 50.0
10.0 DIVISAO 5.0 = 2.0
```

---

## Enums e Switch Case

Enums podem ser usados em instruções `switch`, tornando o código mais legível.

### Exemplo 4: Direções

```java
public enum Direcao {
    NORTE,
    SUL,
    LESTE,
    OESTE
}
```

### Uso em Switch

```java
public class ExemploDirecaoEnum {
    public static void main(String[] args) {
        Direcao direcao = Direcao.LESTE;

        switch (direcao) {
            case NORTE:
                System.out.println("Indo para o Norte");
                break;
            case SUL:
                System.out.println("Indo para o Sul");
                break;
            case LESTE:
                System.out.println("Indo para o Leste");
                break;
            case OESTE:
                System.out.println("Indo para o Oeste");
                break;
        }
    }
}
```

**Saída**:

```
Indo para o Leste
```

---

## Enums e Interfaces

Enums podem implementar interfaces, permitindo adicionar comportamento consistente entre as constantes.

### Exemplo 5: Interface `Operavel`

```java
public interface Operavel {
    double calcular(double x, double y);
}
```

### Enum Implementando a Interface

```java
public enum Operacao implements Operavel {
    SOMA {
        @Override
        public double calcular(double x, double y) {
            return x + y;
        }
    },
    SUBTRACAO {
        @Override
        public double calcular(double x, double y) {
            return x - y;
        }
    }
    // ... demais operações
}
```

---

## Boas Práticas com Enums

- **Nomes em LETRAS MAIÚSCULAS**: Por convenção, os nomes das constantes do Enum são escritos em maiúsculas.
- **Imutabilidade**: Não altere o estado interno das constantes do Enum após a inicialização.
- **Uso Adequado**: Utilize Enums para representar conjuntos fixos de constantes.
- **Evitar Abuso**: Não use Enums para simular funcionalidades que não se encaixam no conceito de constantes nomeadas.

---

## EnumSet e EnumMap

O Java fornece coleções especializadas para trabalhar com Enums de forma eficiente.

### EnumSet

Uma implementação de `Set` otimizada para trabalhar com Enums.

```java
import java.util.EnumSet;

public class ExemploEnumSet {
    public static void main(String[] args) {
        EnumSet<DiaDaSemana> diasUteis = EnumSet.range(DiaDaSemana.SEGUNDA, DiaDaSemana.SEXTA);
        System.out.println("Dias úteis: " + diasUteis);
    }
}
```

**Saída**:

```
Dias úteis: [SEGUNDA, TERCA, QUARTA, QUINTA, SEXTA]
```

### EnumMap

Uma implementação de `Map` otimizada para Enums como chaves.

```java
import java.util.EnumMap;
import java.util.Map;

public class ExemploEnumMap {
    public static void main(String[] args) {
        EnumMap<Pais, String> capitais = new EnumMap<>(Pais.class);

        capitais.put(Pais.BRASIL, "Brasília");
        capitais.put(Pais.PORTUGAL, "Lisboa");

        for (Map.Entry<Pais, String> entry : capitais.entrySet()) {
            System.out.println("País: " + entry.getKey().getNome() + ", Capital: " + entry.getValue());
        }
    }
}
```

**Saída**:

```
País: Brasil, Capital: Brasília
País: Portugal, Capital: Lisboa
```

---

## Serialização de Enums

Enums são serializáveis por padrão. Durante a serialização, apenas o nome da constante é serializado, garantindo que durante a desserialização, a mesma constante seja recuperada.

### Exemplo de Serialização

```java
import java.io.*;

public class ExemploSerializacaoEnum {
    public static void main(String[] args) {
        try {
            // Serialização
            ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("direcao.ser"));
            oos.writeObject(Direcao.NORTE);
            oos.close();

            // Desserialização
            ObjectInputStream ois = new ObjectInputStream(new FileInputStream("direcao.ser"));
            Direcao direcao = (Direcao) ois.readObject();
            ois.close();

            System.out.println("Direção desserializada: " + direcao);

        } catch (IOException | ClassNotFoundException e) {
            e.printStackTrace();
        }
    }
}
```

**Saída**:

```
Direção desserializada: NORTE
```

---

## Usando Enums em Anotações

Enums podem ser utilizados em anotações para restringir os valores possíveis.

### Exemplo de Anotação Personalizada

```java
public enum NivelLog {
    DEBUG,
    INFO,
    WARN,
    ERROR
}

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface Loggable {
    NivelLog nivel() default NivelLog.INFO;
}
```

### Uso da Anotação

```java
public class Servico {

    @Loggable(nivel = NivelLog.DEBUG)
    public void executar() {
        // Código do método
    }
}
```

---

## Considerações Finais

- **Comparação**: Use `==` para comparar Enums, pois eles são singletons.
- **Thread-Safety**: Enums são thread-safe por natureza.
- **Singleton**: Enums podem ser usados para implementar o padrão Singleton de forma segura.

### Exemplo de Singleton com Enum

```java
public enum GerenciadorDeConexao {
    INSTANCIA;

    // Métodos e atributos do singleton
    public void conectar() {
        System.out.println("Conectado!");
    }
}
```

### Uso

```java
public class ExemploSingletonEnum {
    public static void main(String[] args) {
        GerenciadorDeConexao conexao = GerenciadorDeConexao.INSTANCIA;
        conexao.conectar();
    }
}
```

**Saída**:

```
Conectado!
```

---

## Exercícios Práticos

1. **Criar um Enum `TipoDocumento`**:
    - Constantes: `CPF`, `CNPJ`.
    - Adicionar um método `validar(String valor)` que verifica se o número do documento é válido.

2. **Implementar um Enum `EstadoProcesso`**:
    - Constantes: `INICIADO`, `EM_ANDAMENTO`, `FINALIZADO`.
    - Adicionar um atributo `descricao` e métodos para acessar a descrição.

3. **Utilizar EnumSet**:
    - Criar um programa que pergunta ao usuário quais dias da semana ele está disponível e armazena as respostas em um `EnumSet<DiaDaSemana>`.

---

## Conclusão

Enumeradores em Java são uma poderosa ferramenta para representar conjuntos fixos de constantes, fornecendo segurança de tipo, legibilidade e funcionalidades avançadas. Eles vão além de simples constantes, permitindo a adição de métodos, atributos e comportamentos específicos para cada constante.

Compreender e utilizar Enums efetivamente pode melhorar significativamente a qualidade e a manutenção do seu código Java.

---

## Observações Finais

- **Prática**: Experimente criar seus próprios Enums e utilizar as funcionalidades avançadas.
- **Leitura Adicional**: Estude como Enums podem interagir com outras partes do Java, como anotações e coleções.
- **Comunidade**: Participe de fóruns e grupos de discussão para compartilhar conhecimentos e tirar dúvidas.

---

<seealso>
<category ref="wrs">
<a href="https://docs.oracle.com/javase/tutorial/java/javaOO/enum.html"/>
<a href="https://docs.oracle.com/javase/specs/jls/se8/html/jls-8.html#jls-8.9"/>
</category>
</seealso>


