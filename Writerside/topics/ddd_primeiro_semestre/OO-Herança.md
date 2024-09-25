# OO: Herança e Composição

## Introdução

Em programação orientada a objetos (POO), **herança** e **composição** são conceitos fundamentais para modelar relações entre classes e promover a reutilização de código. Embora ambos permitam que uma classe utilize funcionalidades de outra, eles diferem significativamente em sua semântica e aplicação.

Este guia explora as diferenças entre herança e composição em Java, destacando suas características, usos adequados e fornecendo exemplos práticos, incluindo composições com `ArrayList`.

---

## Herança

### O que é Herança?

Herança é um mecanismo que permite que uma classe (subclasse) adquira as propriedades e métodos de outra classe (superclasse). Isso estabelece uma relação hierárquica entre as classes, promovendo a reutilização e extensão do código existente.

### Características da Herança

- **Relação "é-um" (is-a):** A subclasse é um tipo especializado da superclasse.
- **Herança de implementação:** A subclasse herda atributos e comportamentos da superclasse.
- **Sobrescrita de métodos:** A subclasse pode modificar o comportamento herdado.

### Exemplo de Herança

```Java
// Superclasse
public class Veiculo {
    public void mover() {
        System.out.println("O veículo está se movendo.");
    }
}

// Subclasse
public class Carro extends Veiculo {
    public void abrirPorta() {
        System.out.println("A porta do carro está aberta.");
    }

    @Override
    public void mover() {
        System.out.println("O carro está se movendo.");
    }
}

// Uso
public class Main {
    public static void main(String[] args) {
        Carro carro = new Carro();
        carro.mover();        // Saída: O carro está se movendo.
        carro.abrirPorta();   // Saída: A porta do carro está aberta.
    }
}
```

### Quando Usar Herança?

- Quando existe uma relação natural "é-um" entre classes.
- Para reutilizar código e comportamentos comuns.
- Quando deseja-se polimorfismo com classes base.

---

## Composição

### O que é Composição?

Composição é uma forma de estruturar classes onde uma classe contém instâncias de outras classes como membros, estabelecendo uma relação de "tem-um" (has-a). Isso permite que uma classe reutilize funcionalidades de outras sem estabelecer uma hierarquia.

### Características da Composição

- **Relação "tem-um" (has-a):** A classe possui instâncias de outras classes.
- **Encapsulamento:** Os detalhes das classes componentes são ocultados.
- **Flexibilidade:** Componentes podem ser alterados sem afetar a classe que os utiliza.

### Exemplo de Composição com ArrayList

```Java
import java.util.ArrayList;

// Classe Componente
public class Item {
    private String nome;

    public Item(String nome) {
        this.nome = nome;
    }

    public String getNome() {
        return nome;
    }
}

// Classe que compõe uma lista de Itens
public class Inventario {
    private ArrayList<Item> itens;

    public Inventario() {
        itens = new ArrayList<>();
    }

    public void adicionarItem(Item item) {
        itens.add(item);
    }

    public void listarItens() {
        for (Item item : itens) {
            System.out.println(item.getNome());
        }
    }
}

// Uso
public class Main {
    public static void main(String[] args) {
        Inventario inventario = new Inventario();
        inventario.adicionarItem(new Item("Espada"));
        inventario.adicionarItem(new Item("Escudo"));
        inventario.adicionarItem(new Item("Poção"));

        inventario.listarItens();
        // Saída:
        // Espada
        // Escudo
        // Poção
    }
}
```

### Quando Usar Composição?

- Quando classes não possuem relação "é-um", mas "tem-um".
- Para criar objetos complexos a partir de objetos mais simples.
- Quando se busca maior modularidade e baixo acoplamento.

---

## Diferenças Semânticas Entre Herança e Composição

### Relação Entre Classes

- **Herança:** Estabelece uma relação hierárquica rígida.
- **Composição:** Estabelece uma relação de propriedade ou uso.

### Flexibilidade e Acoplamento

- **Herança:** Acoplamento forte; mudanças na superclasse afetam subclasses.
- **Composição:** Acoplamento fraco; componentes podem ser alterados independentemente.

### Reutilização de Código

- **Herança:** Reutiliza código através da extensão de classes existentes.
- **Composição:** Reutiliza código ao delegar responsabilidades para componentes.

### Design e Mantenabilidade

- **Herança:** Pode levar a hierarquias complexas e difíceis de manter.
- **Composição:** Favorece a composição de comportamentos, facilitando a manutenção.

---

## Composição com ArrayList em Detalhe

Usar `ArrayList` em composição permite que uma classe gerencie uma coleção dinâmica de objetos. Isso é útil para representar relações "um-para-muitos" ou "muitos-para-muitos".

### Exemplo: Gerenciador de Cursos e Alunos

```Java
import java.util.ArrayList;

// Classe Aluno
public class Aluno {
    private String nome;

    public Aluno(String nome) {
        this.nome = nome;
    }

    public String getNome() {
        return nome;
    }
}

// Classe Curso que contém uma lista de Alunos
public class Curso {
    private String nome;
    private ArrayList<Aluno> alunos;

    public Curso(String nome) {
        this.nome = nome;
        alunos = new ArrayList<>();
    }

    public void matricularAluno(Aluno aluno) {
        alunos.add(aluno);
    }

    public void listarAlunos() {
        System.out.println("Alunos matriculados no curso " + nome + ":");
        for (Aluno aluno : alunos) {
            System.out.println(aluno.getNome());
        }
    }
}

// Uso
public class Main {
    public static void main(String[] args) {
        Curso cursoJava = new Curso("Java Avançado");

        Aluno aluno1 = new Aluno("Maria");
        Aluno aluno2 = new Aluno("João");

        cursoJava.matricularAluno(aluno1);
        cursoJava.matricularAluno(aluno2);

        cursoJava.listarAlunos();
        // Saída:
        // Alunos matriculados no curso Java Avançado:
        // Maria
        // João
    }
}
```

---

## Boas Práticas

- **Preferir Composição sobre Herança:** A composição oferece maior flexibilidade e reduz o acoplamento.
- **Usar Herança para Especialização:** Apenas quando há uma relação clara de especialização.
- **Manter Hierarquias Simples:** Evitar heranças profundas que dificultam a manutenção.
- **Encapsular Componentes:** Proteger os componentes internos da classe.

---

## Conclusão

Entender as diferenças semânticas entre herança e composição é crucial para o design eficaz de software orientado a objetos. A escolha entre eles deve ser guiada pela natureza da relação entre as classes e pelos objetivos de flexibilidade, reutilização e manutenção do código.

Ao utilizar composições com `ArrayList`, podemos construir estruturas de dados poderosas e flexíveis, capazes de modelar cenários complexos de maneira organizada e eficiente.

---

<seealso>
    <category ref="wrs">
        <a href="https://docs.oracle.com/javase/8/docs/api/"/>
    </category>
</seealso>