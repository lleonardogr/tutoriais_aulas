# OO: Abstração

## Introdução

A abstração é um dos pilares fundamentais da programação orientada a objetos. Em termos simples, abstração é o processo de esconder os detalhes complexos de uma implementação e mostrar apenas as funcionalidades essenciais de um objeto. Ela permite que os desenvolvedores trabalhem em um nível mais alto de complexidade sem se preocupar com os detalhes internos.

## O Conceito de Abstração

### Definição
Abstração se refere ao ato de representar características essenciais sem incluir a complexidade de fundo. Em Java, isso é frequentemente feito usando classes abstratas e interfaces.

### Exemplos de Abstração
- **Abstração tradicional**: Uma classe com as propriedades e métodos implementadas é realizada a abstração.
- **Classe Abstrata**: Uma classe que não pode ser instanciada diretamente e pode conter métodos abstratos que não têm implementação.
- **Interface**: Um contrato que define um conjunto de métodos que uma classe deve implementar.

### Abstração em Java

Vimos no outro capitulo o processo de abstrair algo do mundo real para o mundo virtual.

#### Classe Abstrata

Uma classe abstrata é uma classe que não pode ser instanciada. Ela pode conter métodos abstratos (sem implementação) e métodos concretos (com implementação).

```java
abstract class Animal {
    // Método abstrato
    abstract void fazerSom();

    // Método concreto
    void dormir() {
        System.out.println("O animal está dormindo");
    }
}

class Cachorro extends Animal {
    void fazerSom() {
        System.out.println("O cachorro late");
    }
}
```

#### Interface

Uma interface é um contrato que especifica um conjunto de métodos que uma classe deve implementar. Diferente das classes abstratas, interfaces não podem ter qualquer implementação de métodos (até Java 8, que introduziu métodos padrão e estáticos).

```java
interface Animal {
    void fazerSom();
}

class Cachorro implements Animal {
    public void fazerSom() {
        System.out.println("O cachorro late");
    }
}
```

## Dicas e Recomendações para Fazer Abstração

### Dicas Gerais

1. **Foque nas Funcionalidades Essenciais**: Concentre-se em representar apenas as características e comportamentos essenciais do objeto.
2. **Use Interfaces para Contratos Comuns**: Utilize interfaces para definir contratos que podem ser implementados por diferentes classes.
3. **Utilize Classes Abstratas para Compartilhar Código**: Use classes abstratas quando precisar compartilhar código comum entre várias classes.
4. **Evite o Uso Excessivo de Abstrações**: Embora a abstração seja poderosa, o uso excessivo pode tornar o código difícil de entender e manter. Use-a de maneira equilibrada.

### Recomendações de Autores Renomados

#### Robert C. Martin (Uncle Bob)
No livro "Clean Code: A Handbook of Agile Software Craftsmanship", Robert C. Martin enfatiza a importância de manter o código limpo e fácil de entender. Ele sugere:
- **Mantenha as Abstrações Simples**: Não complique demais suas classes abstratas ou interfaces.
- **Prefira Interfaces a Classes Abstratas**: Sempre que possível, use interfaces para definir contratos.

#### Joshua Bloch
Em "Effective Java", Joshua Bloch oferece diversas recomendações sobre o uso de abstrações:
- **Use Interfaces para Definir Tipos**: Utilize interfaces para definir tipos que podem ter múltiplas implementações.
- **Evite Quebra de Contratos**: Certifique-se de que todas as classes que implementam uma interface seguem o contrato definido pela interface.

#### Martin Fowler
No livro "Patterns of Enterprise Application Architecture", Martin Fowler discute a importância dos padrões de design e abstrações:
- **Use Abstrações para Flexibilidade**: Abstrações podem tornar o sistema mais flexível e adaptável a mudanças futuras.
- **Cuidado com Overengineering**: Evite criar abstrações desnecessárias que podem complicar o sistema.

## Exemplo Completo de Abstração

### Exemplo de Classe Abstrata
```java
abstract class Veiculo {
    abstract void acelerar();

    void frear() {
        System.out.println("O veículo está freando");
    }
}

class Carro extends Veiculo {
    void acelerar() {
        System.out.println("O carro está acelerando");
    }
}
```

### Exemplo de Interface
```java
interface Pagamento {
    void processarPagamento(double valor);
}

class PagamentoCartaoCredito implements Pagamento {
    public void processarPagamento(double valor) {
        System.out.println("Processando pagamento de R$" + valor + " via cartão de crédito");
    }
}

class PagamentoPaypal implements Pagamento {
    public void processarPagamento(double valor) {
        System.out.println("Processando pagamento de R$" + valor + " via Paypal");
    }
}
```

<seealso>
    <category ref="wrs">
        <a href="https://docs.oracle.com/javase/tutorial/java/concepts/index.html">Documentação Oficial do Java sobre Abstração</a>
        <a href="https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882">Clean Code - Robert C. Martin</a>
        <a href="https://www.amazon.com/Effective-Java-Joshua-Bloch/dp/0134685997">Effective Java - Joshua Bloch</a>
        <a href="https://www.amazon.com/Patterns-Enterprise-Application-Architecture-Martin/dp/0321127420">Patterns of Enterprise Application Architecture - Martin Fowler</a>
    </category>
</seealso>

## Conclusão

A abstração é uma ferramenta poderosa na programação orientada a objetos, permitindo que os desenvolvedores se concentrem nos aspectos essenciais de um sistema enquanto escondem a complexidade. Ao seguir as dicas e recomendações de autores renomados, você pode utilizar abstração de maneira eficaz para criar sistemas flexíveis e fáceis de manter.
