# OO: Polimorfismo, Overload e Override

## Introdução

O **polimorfismo** é um dos pilares da programação orientada a objetos (POO), juntamente com encapsulamento, herança e abstração. Em termos simples, polimorfismo permite que objetos de diferentes classes sejam tratados como objetos de uma classe comum. Em Java, o polimorfismo é alcançado principalmente através de **overloading** (sobrecarga) e **overriding** (sobrescrita).

Neste guia, exploraremos em detalhes o conceito de polimorfismo, explicando a diferença entre overload e override, e forneceremos exemplos práticos, incluindo a sobrecarga de construtores (construtor completo e vazio).

---

## O que é Polimorfismo?

Polimorfismo, do grego "muitas formas", refere-se à capacidade de uma entidade, como um método ou objeto, assumir diferentes formas. Em Java, isso permite que métodos com o mesmo nome se comportem de maneiras diferentes, dependendo do contexto ou dos tipos de dados envolvidos.

### Tipos de Polimorfismo em Java

1. **Polimorfismo de Compilação (Static Binding):** Alcançado através de overloading de métodos e construtores.
2. **Polimorfismo de Execução (Dynamic Binding):** Alcançado através de overriding de métodos.

---

## Overloading (Sobrecarga)

### O que é Overloading?

Overloading é a capacidade de definir múltiplos métodos ou construtores com o mesmo nome, mas com diferentes parâmetros dentro da mesma classe. O compilador diferencia os métodos com base no número, tipo e ordem dos parâmetros.

### Características do Overloading

- **Mesma classe:** A sobrecarga ocorre na mesma classe.
- **Assinaturas diferentes:** Métodos ou construtores devem ter assinaturas diferentes.
- **Resolução em tempo de compilação:** A ligação do método é determinada durante a compilação.

### Exemplo de Overloading de Métodos

```java
public class Calculadora {
    // Método para somar dois inteiros
    public int somar(int a, int b) {
        return a + b;
    }

    // Método para somar três inteiros
    public int somar(int a, int b, int c) {
        return a + b + c;
    }

    // Método para somar dois números de ponto flutuante
    public double somar(double a, double b) {
        return a + b;
    }
}

// Uso
public class Main {
    public static void main(String[] args) {
        Calculadora calc = new Calculadora();

        System.out.println(calc.somar(2, 3));          // Saída: 5
        System.out.println(calc.somar(2, 3, 4));       // Saída: 9
        System.out.println(calc.somar(2.5, 3.5));      // Saída: 6.0
    }
}
```

### Overloading de Construtores

Sobrecarga de construtores permite que uma classe tenha múltiplos construtores com diferentes parâmetros, oferecendo diversas maneiras de instanciar objetos.

#### Exemplo: Construtor Vazio e Construtor Completo

```java
public class Pessoa {
    private String nome;
    private int idade;

    // Construtor vazio
    public Pessoa() {
        // Pode inicializar valores padrão
    }

    // Construtor completo
    public Pessoa(String nome, int idade) {
        this.nome = nome;
        this.idade = idade;
    }

    // Métodos getters e setters
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

// Uso
public class Main {
    public static void main(String[] args) {
        // Usando o construtor vazio
        Pessoa pessoa1 = new Pessoa();
        pessoa1.setNome("Carlos");
        pessoa1.setIdade(30);

        // Usando o construtor completo
        Pessoa pessoa2 = new Pessoa("Ana", 25);

        System.out.println(pessoa1.getNome() + " tem " + pessoa1.getIdade() + " anos.");
        // Saída: Carlos tem 30 anos.

        System.out.println(pessoa2.getNome() + " tem " + pessoa2.getIdade() + " anos.");
        // Saída: Ana tem 25 anos.
    }
}
```

---

## Overriding (Sobrescrita)

### O que é Overriding?

Overriding é a capacidade de uma subclasse fornecer uma implementação específica de um método que já é fornecido por sua superclasse ou interface. Isso permite que a subclasse adapte o comportamento do método às suas necessidades.

### Características do Overriding

- **Classes diferentes:** A sobrescrita ocorre entre classes relacionadas por herança.
- **Mesma assinatura:** O método sobrescrito deve ter a mesma assinatura.
- **Resolução em tempo de execução:** A ligação do método é determinada durante a execução.

### Regras para Overriding

- O método na subclasse deve ter o mesmo nome, tipo de retorno e parâmetros.
- O modificador de acesso não pode ser mais restritivo.
- Métodos marcados como `final` ou `static` não podem ser sobrescritos.

### Exemplo de Overriding de Métodos

```java
// Superclasse
public class Animal {
    public void emitirSom() {
        System.out.println("O animal faz um som.");
    }
}

// Subclasse
public class Cachorro extends Animal {
    @Override
    public void emitirSom() {
        System.out.println("O cachorro late.");
    }
}

// Uso
public class Main {
    public static void main(String[] args) {
        Animal meuAnimal = new Animal();
        Animal meuCachorro = new Cachorro();

        meuAnimal.emitirSom();       // Saída: O animal faz um som.
        meuCachorro.emitirSom();     // Saída: O cachorro late.
    }
}
```

No exemplo acima, o método `emitirSom` é sobrescrito na classe `Cachorro` para fornecer uma implementação específica.

---

## Diferenças entre Overloading e Overriding

| Aspecto              | Overloading (Sobrecarga)                  | Overriding (Sobrescrita)                     |
|----------------------|-------------------------------------------|----------------------------------------------|
| Localização          | Mesma classe                              | Classes diferentes (herança)                 |
| Assinatura do Método | Deve ser diferente                        | Deve ser exatamente a mesma                  |
| Modificadores        | Pode variar                                | Não pode ser mais restritivo                 |
| Resolução            | Tempo de compilação                       | Tempo de execução                            |
| Uso Principal        | Aumentar a legibilidade e flexibilidade   | Implementar polimorfismo e comportamento específico |

---

## Quando Usar Overloading e Overriding?

### Overloading

- Quando métodos ou construtores realizam operações semelhantes com diferentes tipos ou número de parâmetros.
- Para oferecer múltiplas formas de inicializar objetos ou chamar métodos.

### Overriding

- Quando uma subclasse precisa modificar o comportamento de um método herdado.
- Para implementar polimorfismo e permitir que classes derivadas tenham comportamentos específicos.

---

## Polimorfismo em Ação

### Exemplo Prático

Vamos combinar overloading e overriding em um exemplo que demonstra polimorfismo.

```java
// Classe base
public class Forma {
    public void desenhar() {
        System.out.println("Desenhando uma forma genérica.");
    }
}

// Subclasse
public class Circulo extends Forma {
    @Override
    public void desenhar() {
        System.out.println("Desenhando um círculo.");
    }

    // Sobrecarga de método
    public void desenhar(int raio) {
        System.out.println("Desenhando um círculo com raio " + raio + ".");
    }
}

// Uso
public class Main {
    public static void main(String[] args) {
        Forma forma = new Forma();
        Forma circuloForma = new Circulo();
        Circulo circulo = new Circulo();

        forma.desenhar();             // Saída: Desenhando uma forma genérica.
        circuloForma.desenhar();      // Saída: Desenhando um círculo.
        circulo.desenhar();           // Saída: Desenhando um círculo.
        circulo.desenhar(5);          // Saída: Desenhando um círculo com raio 5.
    }
}
```

Neste exemplo:

- **Overriding:** O método `desenhar()` é sobrescrito na classe `Circulo`.
- **Overloading:** O método `desenhar(int raio)` é uma sobrecarga em `Circulo`.

---

## Boas Práticas

- **Usar `@Override`:** Sempre que sobrescrever um método, use a anotação `@Override` para evitar erros.
- **Clareza de Código:** Use overloading quando métodos realizam ações relacionadas.
- **Consistência:** Mantenha os métodos sobrecarregados logicamente consistentes.
- **Documentação:** Documente os métodos sobrecarregados e sobrescritos para facilitar a manutenção.

---

## Conclusão

Polimorfismo é uma ferramenta poderosa que permite que objetos sejam tratados de maneira uniforme, mesmo quando comportamentos específicos são implementados. A compreensão de overloading e overriding é essencial para aproveitar ao máximo a orientação a objetos em Java.

Ao utilizar a sobrecarga de métodos e construtores, podemos oferecer maior flexibilidade e facilidade de uso em nossas classes. A sobrescrita permite que subclasses adaptem ou estendam o comportamento de suas superclasses, promovendo reutilização de código e extensibilidade.

## Exercícios Práticos

1. **Criar uma classe `Calculadora` que sobrecarrega o método `multiplicar` para diferentes tipos de dados.**

2. **Implementar uma hierarquia de classes `Veiculo` e `Bicicleta`, onde `Bicicleta` sobrescreve o método `mover`.**

3. **Experimentar a sobrecarga de construtores em uma classe `Produto`, permitindo a criação de objetos com diferentes conjuntos de informações.**

<seealso>
    <category ref="wrs">
        <a href="https://docs.oracle.com/javase/8/docs/api/](https://docs.oracle.com/javase/8/docs/api/"/>
    </category>
</seealso>