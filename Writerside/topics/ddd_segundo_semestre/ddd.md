
# Tutorial Java: DDD Entities e Repositories vs. Beans/DAO

## Introdução

Neste tutorial, vamos explorar os conceitos de Domain-Driven Design (DDD) focando em Entities e Repositories, e como eles diferem do tradicional modelo de Beans e DAO (Data Access Object) em aplicações Java.

## O que é DDD?

Domain-Driven Design é uma abordagem para o desenvolvimento de software centrada no domínio e na lógica do negócio. Ela propõe a modelagem baseada no domínio real para o qual o software está sendo desenvolvido.

### Entidades (Entities) em DDD

Em DDD, uma Entidade é um objeto que é identificado não pelo seu atributo, mas pela sua identidade única (ex: ID ou chave única).

```java
public class Pedido {
    private Long id;
    private Date dataPedido;
    private List<ItemPedido> itens;

    // Getters e Setters, construtores, toString(), equalsAndHashCode(), clone()
}
```

#### Entidade Base

A `EntidadeBase` é uma classe abstrata que contém os atributos comuns a todas as outras entidades do sistema, como `id` e `nome`. Ela serve como uma base para as outras classes, evitando a repetição de código.

Exemplo:
```java
public abstract class EntidadeBase {
    protected int id;
    protected String nome;

    // Construtores, getters e setters
}
```

### Repositórios (Repositories) em DDD

Repositories são abstrações que gerenciam o armazenamento e recuperação de entidades, sem expor detalhes de implementação do banco de dados.

```java
public interface PedidoRepository {
    Pedido findById(Long id);
    void save(Pedido pedido);
    void delete(Pedido pedido);
}
```

#### Repositório Genérico

A interface RepositorioGenerico<T> define os métodos CRUD básicos que serão implementados pelos repositórios específicos das entidades.

```java
public interface RepositorioGenerico<T> {
    void adicionar(T entidade);
    void atualizar(T entidade);
    void remover(int id);
    List<T> listar();
}
```

### Camada de Serviço (Service)

A camada de serviço é responsável por validar os dados das entidades antes de serem persistidos no repositório.

#### Validador de Entidades

A classe ValidadorEntidades fornece métodos estáticos para validar atributos específicos das entidades, como verificar se o nome não está vazio ou se o ano de lançamento é válido.

```java
public class ProdutoService {
    public static void validarProduto(Produto produto) {
        // Validações para o produto
    }
}
```


## Modelo Beans/DAO

Tradicionalmente, em aplicações Java, usamos o padrão Beans para representar objetos e DAO para interagir com o banco de dados.

### Beans

São simples objetos Java que seguem uma convenção de nomenclatura e têm propriedades privadas acessadas por getters e setters.

```java
public class PedidoBean {
    private Long id;
    private Date dataPedido;
    // Getters e Setters
}
```

### DAO (Data Access Object)

DAO é um padrão de design que fornece uma interface para interagir com o banco de dados, separando a lógica de acesso ao banco de dados da lógica de negócios.

```java
public class PedidoDAO {
    public PedidoBean findById(Long id) {
        // Implementação do acesso ao banco de dados
    }
}
```

## Diferenças entre DDD e Modelo Beans/DAO

1. **Foco no Domínio vs. Foco na Persistência**: DDD se concentra no domínio e na lógica de negócios, enquanto Beans/DAO se concentra em persistência e acesso a dados.
2. **Identidade vs. Estrutura**: Entidades em DDD são definidas por sua identidade, enquanto Beans são definidos por sua estrutura.
3. **Abstração vs. Concretização**: Repositories em DDD abstraem o acesso a dados, enquanto DAOs são implementações concretas de acesso a dados.

## Por que usar DDD?

1. **Complexidade do Domínio**: DDD é útil em sistemas complexos onde a lógica do negócio e as regras do domínio são mais importantes e complicadas.
2. **Evolução e Manutenção**: DDD facilita a evolução e manutenção do software, permitindo mudanças no domínio de forma mais ágil.
3. **Colaboração**: Facilita a comunicação entre desenvolvedores e especialistas do domínio através da modelagem baseada no domínio real.


## Conclusão

Neste tutorial, abordamos os conceitos básicos de Entities e Repositories em DDD e como eles se diferenciam do modelo Beans/DAO. O uso de DDD pode proporcionar um design mais limpo, focado no domínio e facilitar a manutenção e evolução do software.
