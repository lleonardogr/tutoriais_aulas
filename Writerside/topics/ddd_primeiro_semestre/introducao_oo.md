
# Tutorial: Introdução à Orientação a Objetos em Java

## Introdução

A orientação a objetos (OO) é um paradigma de programação que surgiu na década de 1960 com a linguagem Simula. Esse paradigma organiza o software em torno de "objetos" que combinam dados e comportamentos. A linguagem de programação Java, criada pela Sun Microsystems em 1995, é uma das linguagens mais populares que utiliza esse paradigma.

## Aplicações da Orientação a Objetos

A orientação a objetos é amplamente utilizada em diversas áreas, incluindo:

- **Desenvolvimento de Software Comercial**: ERP, CRM e sistemas de gerenciamento.
- **Desenvolvimento de Jogos**: Frameworks e motores de jogos que permitem criar personagens, cenários e interações complexas.
- **Aplicações Web e Móveis**: Aplicações robustas que necessitam de manutenção e escalabilidade.

## Os 4 Pilares da Orientação a Objetos

Os quatro pilares fundamentais da orientação a objetos são: Abstração, Encapsulamento, Herança e Polimorfismo. Vamos explorar cada um deles em detalhe.

### 1. Abstração

A abstração foca em esconder os detalhes complexos e mostrar apenas as características essenciais de um objeto. Em outras palavras, abstração permite que você modele objetos do mundo real em termos de classes e atributos.

#### Exemplo
```java
abstract class Animal {
    abstract void fazerSom();
}

class Cachorro extends Animal {
    void fazerSom() {
        System.out.println("O cachorro late");
    }
}
```

### 2. Encapsulamento

O encapsulamento é o mecanismo de restringir o acesso a alguns dos componentes de um objeto e pode ser alcançado usando modificadores de acesso. Isso melhora a segurança e a manutenção do código.

#### Exemplo
```java
class Pessoa {
    private String nome;
    private int idade;

    public String getNome() {
        return nome;
    }

    public void setNome(String nome) {
        this.nome = nome;
    }

    public int getIdade() {
        return idade;
    }

    public void setIdade(int idade) {
        this.idade = idade;
    }
}
```

### 3. Herança

A herança permite que uma classe (subclasse) herde atributos e métodos de outra classe (superclasse). Isso promove a reutilização do código.

#### Exemplo
```java
class Animal {
    void dormir() {
        System.out.println("O animal está dormindo");
    }
}

class Cachorro extends Animal {
    void latir() {
        System.out.println("O cachorro está latindo");
    }
}
```

### 4. Polimorfismo

O polimorfismo permite que objetos de diferentes classes sejam tratados como objetos da mesma classe base. Existem dois tipos de polimorfismo: estático (sobrecarga) e dinâmico (sobrescrita).

#### Exemplo de Sobrecarga
```java
class Calculadora {
    int somar(int a, int b) {
        return a + b;
    }

    double somar(double a, double b) {
        return a + b;
    }
}
```

#### Exemplo de Sobrescrita
```java
class Animal {
    void fazerSom() {
        System.out.println("O animal faz um som");
    }
}

class Cachorro extends Animal {
    void fazerSom() {
        System.out.println("O cachorro late");
    }
}
```

## <seealso>

- [Documentação Oficial do Java sobre Orientação a Objetos](https://docs.oracle.com/javase/tutorial/java/concepts/index.html)
- [Pilares da Orientação a Objetos - GeeksforGeeks](https://www.geeksforgeeks.org/four-pillars-of-object-oriented-programming/)
- [Java OOP - W3Schools](https://www.w3schools.com/java/java_oop.asp)
- [Encapsulamento em Java - Baeldung](https://www.baeldung.com/java-encapsulation)
- [Polimorfismo em Java - Oracle](https://docs.oracle.com/javase/tutorial/java/IandI/polymorphism.html)

## Conclusão

A orientação a objetos é um paradigma poderoso que permite criar software modular, reutilizável e fácil de manter. Compreender os pilares da OO é fundamental para qualquer desenvolvedor Java. Experimente os exemplos e veja como esses conceitos se aplicam no desenvolvimento de software.
