# POJOs, Entidades e Value Objects no DDD

## Introdução

No desenvolvimento de software, especialmente ao utilizar a abordagem de **Domain-Driven Design (DDD)**, é fundamental compreender os conceitos de **POJOs**, **Entidades** e **Value Objects**. Esses conceitos ajudam a modelar o domínio da aplicação de forma clara e consistente.

Este guia explora cada um desses conceitos, como eles se relacionam conceitualmente e fornece exemplos de código que incluem construtores vazios e completos, métodos *getters* e *setters*, e implementações dos métodos `toString`, `equals` e `hashCode`.

---

## POJOs (Plain Old Java Objects)

### O que são POJOs?

POJOs são objetos Java simples, sem qualquer restrição ou requisito especial além dos definidos pela linguagem Java. Eles não estendem classes ou implementam interfaces específicas de qualquer framework ou biblioteca, tornando-os leves e fáceis de usar.

### Características dos POJOs

- **Simplicidade:** Não dependem de bibliotecas ou frameworks externos.
- **Flexibilidade:** Podem ser facilmente serializados, clonados e manipulados.
- **Reutilização:** Servem como base para entidades e value objects no DDD.

### Exemplo de POJO

```java
public class Endereco {
    private String rua;
    private String cidade;
    private String cep;

    // Construtor vazio
    public Endereco() {
    }

    // Construtor completo
    public Endereco(String rua, String cidade, String cep) {
        this.rua = rua;
        this.cidade = cidade;
        this.cep = cep;
    }

    // Getters e Setters
    public String getRua() {
        return rua;
    }

    public void setRua(String rua) {
        this.rua = rua;
    }

    public String getCidade() {
        return cidade;
    }

    public void setCidade(String cidade) {
        this.cidade = cidade;
    }

    public String getCep() {
        return cep;
    }

    public void setCep(String cep) {
        this.cep = cep;
    }

    // toString
    @Override
    public String toString() {
        return "Endereco{" +
                "rua='" + rua + '\'' +
                ", cidade='" + cidade + '\'' +
                ", cep='" + cep + '\'' +
                '}';
    }

    // equals e hashCode
    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;

        Endereco endereco = (Endereco) o;

        if (!rua.equals(endereco.rua)) return false;
        if (!cidade.equals(endereco.cidade)) return false;
        return cep.equals(endereco.cep);
    }

    @Override
    public int hashCode() {
        int result = rua.hashCode();
        result = 31 * result + cidade.hashCode();
        result = 31 * result + cep.hashCode();
        return result;
    }
}
```

---

## Entidades no DDD

### O que são Entidades?

No DDD, uma **Entidade** é um objeto que possui uma identidade única e persistente ao longo do tempo. A identidade é o que distingue uma entidade de outra, mesmo que seus atributos sejam iguais. As entidades geralmente representam conceitos que possuem um ciclo de vida e mudam de estado.

### Características das Entidades

- **Identidade Única:** Possuem um identificador único (como um ID).
- **Ciclo de Vida:** Podem mudar de estado ao longo do tempo.
- **Igualdade por Identidade:** A comparação entre entidades é feita com base em sua identidade.

### Exemplo de Entidade

```java
public class Cliente {
    private Long id; // Identidade única
    private String nome;
    private String email;
    private Endereco endereco;

    // Construtor vazio
    public Cliente() {
    }

    // Construtor completo
    public Cliente(Long id, String nome, String email, Endereco endereco) {
        this.id = id;
        this.nome = nome;
        this.email = email;
        this.endereco = endereco;
    }

    // Getters e Setters
    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    // Outros getters e setters omitidos para brevidade

    // toString
    @Override
    public String toString() {
        return "Cliente{" +
                "id=" + id +
                ", nome='" + nome + '\'' +
                ", email='" + email + '\'' +
                ", endereco=" + endereco +
                '}';
    }

    // equals e hashCode baseados na identidade
    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;

        Cliente cliente = (Cliente) o;

        return id.equals(cliente.id);
    }

    @Override
    public int hashCode() {
        return id.hashCode();
    }
}
```

---

## Value Objects no DDD

### O que são Value Objects?

**Value Objects** são objetos que são definidos não por sua identidade, mas por seus atributos. Eles são imutáveis e representam um conceito ou descrição, como uma quantidade, endereço ou intervalo de datas.

### Características dos Value Objects

- **Imutabilidade:** Uma vez criado, seu estado não pode ser alterado.
- **Igualdade por Valor:** A comparação é feita com base nos valores dos atributos.
- **Sem Identidade Própria:** Não possuem um identificador único.

### Exemplo de Value Object

```java
public final class Dinheiro {
    private final double valor;
    private final String moeda;

    // Construtor completo (sem construtor vazio devido à imutabilidade)
    public Dinheiro(double valor, String moeda) {
        this.valor = valor;
        this.moeda = moeda;
    }

    // Getters (sem setters)
    public double getValor() {
        return valor;
    }

    public String getMoeda() {
        return moeda;
    }

    // toString
    @Override
    public String toString() {
        return "Dinheiro{" +
                "valor=" + valor +
                ", moeda='" + moeda + '\'' +
                '}';
    }

    // equals e hashCode baseados nos atributos
    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;

        Dinheiro dinheiro = (Dinheiro) o;

        if (Double.compare(dinheiro.valor, valor) != 0) return false;
        return moeda.equals(dinheiro.moeda);
    }

    @Override
    public int hashCode() {
        int result;
        long temp;
        temp = Double.doubleToLongBits(valor);
        result = (int) (temp ^ (temp >>> 32));
        result = 31 * result + moeda.hashCode();
        return result;
    }
}
```

---

## Relação Conceitual entre POJOs, Entidades e Value Objects

- **POJOs** são a base para criar **Entidades** e **Value Objects**. Eles fornecem uma estrutura simples sem depender de frameworks.
- **Entidades** são POJOs com identidade única, geralmente representando objetos do mundo real que possuem um ciclo de vida e podem mudar de estado.
- **Value Objects** são POJOs que representam valores e não possuem identidade própria. São imutáveis e comparados por seus atributos.

---

## Implementação de `equals` e `hashCode`

### Importância

- **Entidades:** Devem implementar `equals` e `hashCode` baseados na identidade (ID), pois duas entidades com o mesmo ID são consideradas iguais.
- **Value Objects:** Devem implementar `equals` e `hashCode` baseados nos atributos, pois são comparados pelo valor.

### Boas Práticas

- Sempre sobrescrever `equals` e `hashCode` juntos.
- Usar todas as propriedades relevantes para comparação no caso de Value Objects.
- Manter a consistência entre `equals` e `hashCode`.

---

## Exemplo Prático Integrando Entidade e Value Object

```java
public class Produto {
    private Long id; // Identidade única
    private String nome;
    private Dinheiro preco; // Value Object
    private String descricao;

    // Construtor vazio
    public Produto() {
    }

    // Construtor completo
    public Produto(Long id, String nome, Dinheiro preco, String descricao) {
        this.id = id;
        this.nome = nome;
        this.preco = preco;
        this.descricao = descricao;
    }

    // Getters e Setters
    // ...

    // toString
    @Override
    public String toString() {
        return "Produto{" +
                "id=" + id +
                ", nome='" + nome + '\'' +
                ", preco=" + preco +
                ", descricao='" + descricao + '\'' +
                '}';
    }

    // equals e hashCode baseados na identidade
    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;

        Produto produto = (Produto) o;

        return id.equals(produto.id);
    }

    @Override
    public int hashCode() {
        return id.hashCode();
    }
}
```

Neste exemplo, `Produto` é uma entidade que possui um `Dinheiro` como atributo, representando o preço. `Dinheiro` é um Value Object.

---

## Uso de Construtores, Getters e Setters

- **Construtor Vazio:** Necessário para algumas frameworks (como Hibernate) que instanciam objetos via reflexão.
- **Construtor Completo:** Facilita a criação de objetos com todos os atributos definidos.
- **Getters e Setters:** Permitem acessar e modificar os atributos, respeitando o encapsulamento.

### Exemplo de Uso

```java
public class Main {
    public static void main(String[] args) {
        // Usando construtor completo
        Dinheiro preco = new Dinheiro(99.90, "BRL");
        Produto produto = new Produto(1L, "Teclado Mecânico", preco, "Teclado para jogos");

        // Usando construtor vazio e setters
        Cliente cliente = new Cliente();
        cliente.setId(1L);
        cliente.setNome("João Silva");
        cliente.setEmail("joao@example.com");

        // Exibindo informações
        System.out.println(produto);
        System.out.println(cliente);
    }
}
```

---

## Implementação de `toString`

- Facilita o log e depuração exibindo uma representação textual do objeto.
- Útil para imprimir objetos em logs, consoles ou interfaces gráficas.

## Implementação de Imutabilidade em Value Objects

- Declarar a classe como `final` para evitar extensão.
- Não fornecer setters.
- Todos os atributos devem ser `final` e inicializados no construtor.

---

## Conclusão

Compreender e aplicar os conceitos de **POJOs**, **Entidades** e **Value Objects** é essencial para um design de software orientado ao domínio efetivo. Eles permitem uma modelagem clara e consistente do domínio da aplicação, facilitando a manutenção e evolução do sistema.

Ao implementar corretamente construtores, getters, setters, e os métodos `toString`, `equals` e `hashCode`, garantimos que nossos objetos se comportem de maneira previsível e adequada às necessidades da aplicação.



## Exercícios Práticos

1. **Criar uma classe `Pedido` como Entidade, que possui uma lista de `ItemPedido` como Value Objects.**

2. **Implementar os métodos `equals` e `hashCode` em `ItemPedido` considerando todos os atributos.**

3. **Criar um `Value Object` chamado `CPF` que valida o formato do CPF no construtor e é imutável.**

---

## Observações Finais

- **Entidades e Value Objects** são conceitos fundamentais no DDD que ajudam a modelar o domínio de forma precisa.
- **POJOs** são a base para construir essas estruturas, mantendo o código limpo e desacoplado.
- **Imutabilidade** em Value Objects evita efeitos colaterais e facilita a manutenção.
- **Identidade** é crucial em Entidades para garantir que cada objeto seja único no sistema.