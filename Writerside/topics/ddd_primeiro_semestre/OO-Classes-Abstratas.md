# OO: Classes Abstratas

## Introdução

Em programação orientada a objetos, as **classes abstratas** desempenham um papel fundamental na criação de hierarquias de classes e na definição de contratos para subclasses. Elas permitem modelar conceitos que não fazem sentido serem instanciados diretamente, mas que compartilham características comuns que devem ser implementadas pelas subclasses.

Neste guia, exploraremos o conceito de classes abstratas em Java, suas características, quando e como usá-las. Faremos isso através de exemplos práticos utilizando uma classe abstrata `Midia`, que é herdada pelas classes concretas `Musica` e `Podcast`.

---

## O que são Classes Abstratas?

Uma **classe abstrata** é uma classe que não pode ser instanciada diretamente e pode conter métodos abstratos (sem implementação) e métodos concretos (com implementação). Ela serve como um modelo ou base para outras classes que a estendem.

### Características das Classes Abstratas

- **Não podem ser instanciadas**: Não é possível criar objetos diretamente de uma classe abstrata.
- **Podem conter métodos abstratos e concretos**: Métodos abstratos são declarados sem implementação, enquanto métodos concretos possuem implementação.
- **Podem conter atributos**: Tanto estáticos quanto de instância.
- **Servem como base para subclasses**: As subclasses devem implementar os métodos abstratos ou também serem abstratas.

### Sintaxe em Java

```java
public abstract class NomeDaClasse {
    // Atributos

    // Métodos concretos
    public void metodoConcreto() {
        // Implementação
    }

    // Métodos abstratos
    public abstract void metodoAbstrato();
}
```

---

## Por que Usar Classes Abstratas?

- **Modelagem de Conceitos Genéricos**: Representar conceitos que não fazem sentido serem instanciados por si só, mas que compartilham características com subclasses.
- **Definição de Contratos**: Forçar subclasses a implementar métodos específicos.
- **Reutilização de Código**: Compartilhar atributos e métodos comuns entre subclasses.
- **Polimorfismo**: Permitir que objetos de subclasses sejam tratados como objetos da classe abstrata.

---

## Exemplo Prático: Classe `Midia` e Subclasses `Musica` e `Podcast`

Vamos criar uma hierarquia de classes para representar mídias de áudio, como músicas e podcasts.

### Classe Abstrata `Midia`

```java
public abstract class Midia {
    protected String titulo;
    protected String autor;
    protected int duracao; // em segundos

    // Construtor
    public Midia(String titulo, String autor, int duracao) {
        this.titulo = titulo;
        this.autor = autor;
        this.duracao = duracao;
    }

    // Método concreto
    public void reproduzir() {
        System.out.println("Reproduzindo: " + titulo + " de " + autor);
    }

    // Método abstrato
    public abstract void exibirDetalhes();
}
```

**Explicação**:

- `titulo`, `autor` e `duracao` são atributos comuns a todas as mídias.
- `reproduzir()` é um método concreto que pode ser compartilhado.
- `exibirDetalhes()` é um método abstrato que cada subclass deverá implementar.

### Subclasse `Musica`

```java
public class Musica extends Midia {
    private String album;
    private String genero;

    // Construtor
    public Musica(String titulo, String autor, int duracao, String album, String genero) {
        super(titulo, autor, duracao);
        this.album = album;
        this.genero = genero;
    }

    // Implementação do método abstrato
    @Override
    public void exibirDetalhes() {
        System.out.println("Música: " + titulo);
        System.out.println("Artista: " + autor);
        System.out.println("Álbum: " + album);
        System.out.println("Gênero: " + genero);
        System.out.println("Duração: " + duracao + " segundos");
    }
}
```

### Subclasse `Podcast`

```java
public class Podcast extends Midia {
    private String descricao;
    private int episodio;

    // Construtor
    public Podcast(String titulo, String autor, int duracao, String descricao, int episodio) {
        super(titulo, autor, duracao);
        this.descricao = descricao;
        this.episodio = episodio;
    }

    // Implementação do método abstrato
    @Override
    public void exibirDetalhes() {
        System.out.println("Podcast: " + titulo);
        System.out.println("Host: " + autor);
        System.out.println("Descrição: " + descricao);
        System.out.println("Episódio: " + episodio);
        System.out.println("Duração: " + duracao + " segundos");
    }
}
```

### Uso das Classes

```java
public class Main {
    public static void main(String[] args) {
        Midia musica = new Musica("Imagine", "John Lennon", 183, "Imagine", "Rock");
        Midia podcast = new Podcast("Tecnologia Hoje", "Maria Silva", 3600, "Discussões sobre tecnologia", 42);

        musica.reproduzir();
        musica.exibirDetalhes();

        System.out.println();

        podcast.reproduzir();
        podcast.exibirDetalhes();
    }
}
```

**Saída**:

```
Reproduzindo: Imagine de John Lennon
Música: Imagine
Artista: John Lennon
Álbum: Imagine
Gênero: Rock
Duração: 183 segundos

Reproduzindo: Tecnologia Hoje de Maria Silva
Podcast: Tecnologia Hoje
Host: Maria Silva
Descrição: Discussões sobre tecnologia
Episódio: 42
Duração: 3600 segundos
```

---

## Análise do Exemplo

- **Classe Abstrata `Midia`**:
    - Não pode ser instanciada diretamente.
    - Define atributos e métodos comuns.
    - Contém o método abstrato `exibirDetalhes()`.

- **Subclasses `Musica` e `Podcast`**:
    - Estendem `Midia` e herdam seus atributos e métodos.
    - Implementam o método abstrato `exibirDetalhes()` de forma específica.
    - Podem adicionar atributos e métodos adicionais.

- **Polimorfismo**:
    - As variáveis `musica` e `podcast` são do tipo `Midia`, mas armazenam objetos de `Musica` e `Podcast`.
    - Chamadas aos métodos `reproduzir()` e `exibirDetalhes()` utilizam o polimorfismo.

---

## Regras e Considerações sobre Classes Abstratas

1. **Instanciação**:
    - Não é possível criar instâncias de classes abstratas diretamente.
    - Exemplo inválido: `Midia minhaMidia = new Midia(...);`

2. **Métodos Abstratos**:
    - Devem ser implementados em subclasses concretas.
    - Uma subclasse pode ser abstrata e optar por não implementar os métodos abstratos.

3. **Construtores**:
    - Classes abstratas podem ter construtores.
    - Os construtores são chamados através do `super()` nas subclasses.

4. **Modificadores de Acesso**:
    - Métodos e atributos podem ter qualquer modificador de acesso (`public`, `protected`, `private`).
    - Métodos abstratos devem ser declarados como `public` ou `protected`.

5. **Interfaces vs. Classes Abstratas**:
    - Classes abstratas podem conter implementação, enquanto interfaces (antes do Java 8) não podiam.
    - Uma classe pode implementar múltiplas interfaces, mas só pode estender uma única classe (abstrata ou não).

---

## Quando Usar Classes Abstratas?

- **Modelagem de Hierarquias**:
    - Quando há uma relação clara de herança e um comportamento padrão ou estado a ser compartilhado.

- **Definição de Contratos Parciais**:
    - Quando deseja fornecer alguma implementação comum, mas deixar detalhes para as subclasses.

- **Evitar Repetição de Código**:
    - Compartilhando código comum entre classes relacionadas.

- **Polimorfismo**:
    - Quando deseja tratar diferentes objetos de subclasses como objetos da classe base.

---

## Comparação entre Classes Abstratas e Interfaces

| Característica            | Classe Abstrata                                     | Interface                                               |
|---------------------------|-----------------------------------------------------|---------------------------------------------------------|
| Instanciação              | Não pode ser instanciada                             | Não pode ser instanciada                                 |
| Métodos Abstratos         | Pode conter métodos abstratos e concretos           | Até Java 7, todos os métodos eram abstratos e públicos   |
| Atributos                 | Pode ter atributos de instância                     | Até Java 7, apenas constantes (`public static final`)    |
| Herança                   | Uma classe pode estender uma única classe abstrata  | Uma classe pode implementar múltiplas interfaces         |
| Uso                       | Quando há relação de herança e comportamento comum  | Para definir contratos sem implementação                 |

**Nota**: A partir do Java 8, interfaces podem ter métodos `default` (com implementação) e métodos estáticos.

---

## Extensão do Exemplo: Adicionando Mais Subclasses

### Subclasse `Audiobook`

```java
public class Audiobook extends Midia {
    private String narrador;
    private int capitulos;

    // Construtor
    public Audiobook(String titulo, String autor, int duracao, String narrador, int capitulos) {
        super(titulo, autor, duracao);
        this.narrador = narrador;
        this.capitulos = capitulos;
    }

    // Implementação do método abstrato
    @Override
    public void exibirDetalhes() {
        System.out.println("Audiobook: " + titulo);
        System.out.println("Autor: " + autor);
        System.out.println("Narrador: " + narrador);
        System.out.println("Capítulos: " + capitulos);
        System.out.println("Duração: " + duracao + " segundos");
    }
}
```

### Uso Atualizado

```java
public class Main {
    public static void main(String[] args) {
        Midia musica = new Musica("Imagine", "John Lennon", 183, "Imagine", "Rock");
        Midia podcast = new Podcast("Tecnologia Hoje", "Maria Silva", 3600, "Discussões sobre tecnologia", 42);
        Midia audiobook = new Audiobook("1984", "George Orwell", 7200, "Carlos Alberto", 24);

        Midia[] biblioteca = {musica, podcast, audiobook};

        for (Midia midia : biblioteca) {
            midia.reproduzir();
            midia.exibirDetalhes();
            System.out.println();
        }
    }
}
```

**Saída**:

```
Reproduzindo: Imagine de John Lennon
Música: Imagine
Artista: John Lennon
Álbum: Imagine
Gênero: Rock
Duração: 183 segundos

Reproduzindo: Tecnologia Hoje de Maria Silva
Podcast: Tecnologia Hoje
Host: Maria Silva
Descrição: Discussões sobre tecnologia
Episódio: 42
Duração: 3600 segundos

Reproduzindo: 1984 de George Orwell
Audiobook: 1984
Autor: George Orwell
Narrador: Carlos Alberto
Capítulos: 24
Duração: 7200 segundos
```

---

## Boas Práticas com Classes Abstratas

- **Nomeação Clara**: Utilize nomes que reflitam a natureza abstrata da classe (e.g., `Midia`, `Veiculo`, `Animal`).
- **Visibilidade Apropriada**: Use `protected` para permitir que subclasses acessem atributos e métodos, mantendo o encapsulamento.
- **Abstração Adequada**: Não implemente detalhes específicos na classe abstrata; deixe para as subclasses.
- **Documentação**: Comente os métodos abstratos para indicar o que se espera das implementações.

---

## Considerações Finais

- **Não Exagerar**: Nem todos os cenários requerem classes abstratas; avalie se é realmente necessário.
- **Combinação com Interfaces**: Pode ser útil implementar interfaces na classe abstrata para definir contratos adicionais.
- **Atualizações Futuras**: Ao adicionar novos métodos abstratos, lembre-se de que todas as subclasses precisarão implementá-los.

---

## Exemplos Adicionais de Uso de Classes Abstratas

### Modelo de Funcionários em uma Empresa

- **Classe Abstrata `Funcionario`**:
    - Atributos: `nome`, `salario`.
    - Método abstrato: `calcularBonus()`.

- **Subclasses**:
    - `Gerente`: Implementa `calcularBonus()` com uma lógica específica.
    - `Desenvolvedor`: Implementa `calcularBonus()` de outra forma.

---

## Exercícios Práticos

1. **Criar uma classe abstrata `FormaGeometrica`**:
    - Atributos: `nome`.
    - Métodos abstratos: `calcularArea()`, `calcularPerimetro()`.
    - Crie subclasses `Circulo`, `Retangulo`, `Triangulo` e implemente os métodos abstratos.

2. **Desenvolver uma hierarquia de veículos**:
    - Classe abstrata `Veiculo` com método abstrato `mover()`.
    - Subclasses `Carro`, `Bicicleta`, `Aviao` implementando `mover()` de forma específica.

3. **Implementar um sistema de pagamentos**:
    - Classe abstrata `Pagamento` com método abstrato `processarPagamento()`.
    - Subclasses `PagamentoCredito`, `PagamentoDebito`, `PagamentoPix` implementando o método.

---

## Conclusão

As classes abstratas são uma ferramenta poderosa em Java para modelar hierarquias e definir contratos parciais para subclasses. Elas permitem a reutilização de código comum e fornecem um meio de garantir que certas funcionalidades sejam implementadas nas subclasses.

Neste guia, exploramos o conceito de classes abstratas através de exemplos práticos com `Midia`, `Musica` e `Podcast`. Compreender quando e como usar classes abstratas é fundamental para escrever código orientado a objetos eficaz e manter sistemas bem estruturados e fáceis de manter.


## Observações Finais

- **Prática**: Experimente criar suas próprias hierarquias utilizando classes abstratas para consolidar o conhecimento.
- **Leitura Adicional**: Estude sobre interfaces, polimorfismo e outros conceitos relacionados para expandir sua compreensão.
- **Comunidade**: Participe de fóruns e comunidades de desenvolvedores para discutir e esclarecer dúvidas.

