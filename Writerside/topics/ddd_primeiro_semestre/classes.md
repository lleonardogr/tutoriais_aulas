# OO: Classes

## Introdução

Em Java, uma classe é um modelo (ou blueprint) a partir do qual objetos são criados. Uma classe define atributos e comportamentos que os objetos dessa classe terão.

## Estrutura de uma Classe

Uma classe em Java é composta por:

- **Atributos**: Também chamados de variáveis de instância, eles representam as propriedades de um objeto.
- **Métodos**: Também chamados de funções, eles definem os comportamentos que um objeto pode realizar.
- **Construtores**: Métodos especiais usados para inicializar objetos.
- **Getters e Setters**: Métodos para acessar e modificar os atributos de um objeto.
- **Controles de Acesso**: Definem a visibilidade dos atributos e métodos (público, privado, protegido).

### Estrutura Básica
```java
public class NomeDaClasse {
    // Atributos
    tipo atributo1;
    tipo atributo2;

    // Construtor
    public NomeDaClasse(tipo atributo1, tipo atributo2) {
        this.atributo1 = atributo1;
        this.atributo2 = atributo2;
    }

    // Métodos
    public void metodo1() {
        // Implementação
    }

    public tipo getAtributo1() {
        return atributo1;
    }

    public void setAtributo1(tipo atributo1) {
        this.atributo1 = atributo1;
    }
}
```

### Construtores

Construtores são métodos especiais que são chamados quando um objeto é instanciado. Eles têm o mesmo nome da classe e não têm um tipo de retorno.

#### Exemplo de construtor

```java
public class Musica {
    // Atributos
    private String titulo;
    private String artista;
    private int duracao; // duração em segundos

    // Construtor
    public Musica(String titulo, String artista, int duracao) {
        this.titulo = titulo;
        this.artista = artista;
        this.duracao = duracao;
    }

    // Métodos
    // ...
}
```

### Getters e Setters
Getters e setters são métodos que permitem o acesso e a modificação dos atributos de um objeto. Eles seguem uma convenção de nomenclatura específica: get para obter o valor e set para modificar o valor.

```java
// Getters
    public String getTitulo() {
        return titulo;
    }

    public String getArtista() {
        return artista;
    }

    public int getDuracao() {
        return duracao;
    }

    // Setters
    public void setTitulo(String titulo) {
        this.titulo = titulo;
    }

    public void setArtista(String artista) {
        this.artista = artista;
    }

    public void setDuracao(int duracao) {
        this.duracao = duracao;
    }

    // Outros métodos
    // ...
```

## Exemplo 1: Classe `Musica`

Vamos criar uma classe `Musica` que tem atributos como `titulo`, `artista`, `duracao` e métodos para obter essas informações.

### Definição da Classe `Musica`
```java
public class Musica {
    // Atributos
    private String titulo;
    private String artista;
    private int duracao; // duração em segundos

    // Construtor
    public Musica(String titulo, String artista, int duracao) {
        this.titulo = titulo;
        this.artista = artista;
        this.duracao = duracao;
    }

    // Métodos
    public String getTitulo() {
        return titulo;
    }

    public void setTitulo(String titulo) {
        this.titulo = titulo;
    }

    public String getArtista() {
        return artista;
    }

    public void setArtista(String artista) {
        this.artista = artista;
    }

    public int getDuracao() {
        return duracao;
    }

    public void setDuracao(int duracao) {
        this.duracao = duracao;
    }

    public void tocar() {
        System.out.println("Tocando " + titulo + " de " + artista);
    }
}
```

### Uso da Classe `Musica`
```java
public class TesteMusica {
    public static void main(String[] args) {
        Musica musica = new Musica("Bohemian Rhapsody", "Queen", 354);
        musica.tocar();
        System.out.println("Título: " + musica.getTitulo());
        System.out.println("Artista: " + musica.getArtista());
        System.out.println("Duração: " + musica.getDuracao() + " segundos");
    }
}
```

## Exemplo 2: Classe `CartaMagic`

Vamos criar uma classe `CartaMagic` que tem atributos como `nome`, `tipo`, `custo`, `poder`, `resistencia` e métodos para obter essas informações.

### Definição da Classe `CartaMagic`
```java
public class CartaMagic {
    // Atributos
    private String nome;
    private String tipo;
    private int custo;
    private int poder;
    private int resistencia;

    // Construtor
    public CartaMagic(String nome, String tipo, int custo, int poder, int resistencia) {
        this.nome = nome;
        this.tipo = tipo;
        this.custo = custo;
        this.poder = poder;
        this.resistencia = resistencia;
    }

    // Métodos
    public String getNome() {
        return nome;
    }

    public void setNome(String nome) {
        this.nome = nome;
    }

    public String getTipo() {
        return tipo;
    }

    public void setTipo(String tipo) {
        this.tipo = tipo;
    }

    public int getCusto() {
        return custo;
    }

    public void setCusto(int custo) {
        this.custo = custo;
    }

    public int getPoder() {
        return poder;
    }

    public void setPoder(int poder) {
        this.poder = poder;
    }

    public int getResistencia() {
        return resistencia;
    }

    public void setResistencia(int resistencia) {
        this.resistencia = resistencia;
    }

    public void exibirDetalhes() {
        System.out.println(nome + " - " + tipo);
        System.out.println("Custo: " + custo);
        System.out.println("Poder: " + poder);
        System.out.println("Resistência: " + resistencia);
    }
}
```

### Uso da Classe `CartaMagic`
```java
public class TesteCartaMagic {
    public static void main(String[] args) {
        CartaMagic carta = new CartaMagic("Serra Angel", "Criatura", 5, 4, 4);
        carta.exibirDetalhes();
    }
}
```

<seealso>
    <category ref="wrs">
        <a href="https://docs.oracle.com/javase/tutorial/java/javaOO/classes.html">Documentação Oficial do Java sobre Classes </a>
        <a href="https://www.baeldung.com/java-encapsulation">Encapsulamento em Java - Baeldung</a>
        <a href="https://www.geeksforgeeks.org/constructors-in-java/">Construtores em Java - GeeksforGeeks</a>
        <a href="https://www.w3schools.com/java/java_methods.asp">Métodos em Java - W3Schools</a>
    </category>
</seealso>
## Conclusão

Classes são a base da programação orientada a objetos em Java. Elas permitem a criação de objetos com atributos e comportamentos específicos. Compreender como definir e usar classes é fundamental para qualquer desenvolvedor Java. Experimente os exemplos e veja como esses conceitos se aplicam no desenvolvimento de software.
