# Boas praticas: Annotations

## Introdução

As **anotações** (ou **annotations**) em Java são uma poderosa ferramenta que permite adicionar metadados ao código, fornecendo informações adicionais que podem ser utilizadas em tempo de compilação ou execução. Elas são amplamente utilizadas por frameworks para simplificar configurações e comportamentos, mas também podem ser criadas e utilizadas sem o auxílio de frameworks externos.

Neste guia, vamos explorar:

- O que são anotações em Java e como funcionam.
- Como criar anotações personalizadas.
- Como processar anotações em tempo de execução usando reflexão.
- Exemplos práticos:
    - Criar uma anotação `@DisplayName` para campos e usar um scanner para reutilização de código.
    - Criar anotações `@Table` e `@Column` para mapear classes e atributos a tabelas e colunas de banco de dados, permitindo a geração de consultas SQL dinâmicas usando JDBC seguindo o padrão Repository.

---

## O que são Anotações em Java?

As anotações em Java são uma forma de associar metadados a elementos do código, como classes, métodos, campos e parâmetros. Elas não afetam diretamente a execução do código, mas podem ser utilizadas por ferramentas, compiladores ou em tempo de execução para fornecer informações adicionais ou modificar o comportamento padrão.

### Exemplos de Anotações Predefinidas

- `@Override`: Indica que um método sobrescreve um método da superclasse.
- `@Deprecated`: Marca um elemento como obsoleto.
- `@SuppressWarnings`: Instrui o compilador a suprimir avisos específicos.

---

## Criando Anotações Personalizadas

### Sintaxe Básica

Para criar uma anotação personalizada, utilizamos a palavra-chave `@interface`:

```java
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;

@Retention(RetentionPolicy.RUNTIME)
public @interface NomeDaAnotacao {
    // Elementos (parâmetros) da anotação
}
```

### Elementos Importantes

- **RetentionPolicy**: Define em que momento a anotação será retida:
    - `SOURCE`: Descartada após a compilação.
    - `CLASS`: Retida até o bytecode, mas não disponível em tempo de execução.
    - `RUNTIME`: Retida e disponível em tempo de execução via reflexão.

- **Target**: Especifica onde a anotação pode ser aplicada:
    - `ElementType.FIELD`: Campos (atributos).
    - `ElementType.METHOD`: Métodos.
    - `ElementType.TYPE`: Classes, interfaces ou enumerações.
    - Outros: `PARAMETER`, `CONSTRUCTOR`, `LOCAL_VARIABLE`, etc.

---

## Exemplo 1: Anotação `@DisplayName` para Reutilização de Código

Vamos criar uma anotação `@DisplayName` que pode ser usada em campos para fornecer um nome amigável ou uma descrição, e depois usar reflexão para ler essas anotações e reutilizar o código.

### Passo 1: Criar a Anotação `@DisplayName`

```java
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;
import java.lang.annotation.ElementType;

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.FIELD)
public @interface DisplayName {
    String value();
}
```

- **RetentionPolicy.RUNTIME**: Precisamos acessar as anotações em tempo de execução.
- **Target(ElementType.FIELD)**: Aplicável apenas a campos.

### Passo 2: Usar a Anotação em uma Classe

```java
public class Usuario {
    @DisplayName("Nome de Usuário")
    private String username;

    @DisplayName("Endereço de E-mail")
    private String email;

    @DisplayName("Idade do Usuário")
    private int idade;

    // Construtor, getters e setters
    public Usuario(String username, String email, int idade) {
        this.username = username;
        this.email = email;
        this.idade = idade;
    }

    // Getters e Setters omitidos para brevidade
}
```

### Passo 3: Criar um Scanner para Processar as Anotações

Vamos criar uma classe que usa reflexão para ler os campos anotados com `@DisplayName` e exibir seus valores com os nomes amigáveis.

```java
import java.lang.reflect.Field;

public class DisplayNameScanner {

    public static void imprimirCamposComDisplayName(Object obj) {
        Class<?> clazz = obj.getClass();

        for (Field field : clazz.getDeclaredFields()) {
            if (field.isAnnotationPresent(DisplayName.class)) {
                DisplayName displayName = field.getAnnotation(DisplayName.class);
                field.setAccessible(true);
                try {
                    Object valor = field.get(obj);
                    System.out.println(displayName.value() + ": " + valor);
                } catch (IllegalAccessException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```

### Passo 4: Utilizar o Scanner

```java
public class Main {
    public static void main(String[] args) {
        Usuario usuario = new Usuario("johndoe", "john@example.com", 30);
        DisplayNameScanner.imprimirCamposComDisplayName(usuario);
    }
}
```

**Saída:**

```
Nome de Usuário: johndoe
Endereço de E-mail: john@example.com
Idade do Usuário: 30
```

**Explicação:**

- O scanner percorre todos os campos da classe `Usuario`.
- Verifica se o campo possui a anotação `@DisplayName`.
- Se possuir, obtém o valor do campo e o valor da anotação.
- Exibe o nome amigável e o valor do campo.

---

## Exemplo 2: Anotações para Mapear Classes e Atributos a Tabelas e Colunas (JDBC)

Neste exemplo, vamos criar anotações `@Table` e `@Column` para mapear classes e campos a tabelas e colunas de um banco de dados. Isso nos permitirá gerar consultas SQL dinâmicas de forma genérica, seguindo o padrão Repository.

### Passo 1: Criar as Anotações `@Table` e `@Column`

#### Anotação `@Table`

```java
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;
import java.lang.annotation.ElementType;

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
public @interface Table {
    String name();
}
```

- **Target(ElementType.TYPE)**: Aplicável a classes.

#### Anotação `@Column`

```java
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;
import java.lang.annotation.ElementType;

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.FIELD)
public @interface Column {
    String name();
}
```

- **Target(ElementType.FIELD)**: Aplicável a campos.

### Passo 2: Criar a Entidade Mapeada

```java
@Table(name = "usuarios")
public class Usuario {
    @Column(name = "id")
    private int id;

    @Column(name = "nome_usuario")
    private String username;

    @Column(name = "email")
    private String email;

    // Construtor, getters e setters
    public Usuario(int id, String username, String email) {
        this.id = id;
        this.username = username;
        this.email = email;
    }

    // Getters e Setters omitidos para brevidade
}
```

### Passo 3: Criar o Repositório com Consultas Dinâmicas

Vamos criar uma classe genérica `Repository` que pode realizar operações básicas usando as anotações.

#### Classe `Repository`

```java
import java.sql.*;
import java.util.ArrayList;
import java.util.List;
import java.lang.reflect.Field;

public class Repository<T> {

    private Class<T> clazz;

    public Repository(Class<T> clazz) {
        this.clazz = clazz;
    }

    public List<T> findAll(Connection connection) throws Exception {
        List<T> lista = new ArrayList<>();

        // Obter nome da tabela
        if (!clazz.isAnnotationPresent(Table.class)) {
            throw new RuntimeException("A classe não está anotada com @Table");
        }
        Table table = clazz.getAnnotation(Table.class);
        String nomeTabela = table.name();

        // Construir SQL
        StringBuilder sql = new StringBuilder("SELECT ");
        StringBuilder colunas = new StringBuilder();

        Field[] fields = clazz.getDeclaredFields();
        for (Field field : fields) {
            if (field.isAnnotationPresent(Column.class)) {
                Column column = field.getAnnotation(Column.class);
                colunas.append(column.name()).append(", ");
            }
        }
        // Remover última vírgula
        if (colunas.length() > 0) {
            colunas.setLength(colunas.length() - 2);
        }

        sql.append(colunas).append(" FROM ").append(nomeTabela);

        // Executar consulta
        Statement stmt = connection.createStatement();
        ResultSet rs = stmt.executeQuery(sql.toString());

        while (rs.next()) {
            T instancia = clazz.getDeclaredConstructor().newInstance();
            for (Field field : fields) {
                if (field.isAnnotationPresent(Column.class)) {
                    Column column = field.getAnnotation(Column.class);
                    field.setAccessible(true);
                    Object valor = rs.getObject(column.name());
                    field.set(instancia, valor);
                }
            }
            lista.add(instancia);
        }

        return lista;
    }

    // Outros métodos como findById, save, update, delete podem ser adicionados
}
```

**Notas:**

- O repositório genérico usa reflexão para:
    - Obter o nome da tabela e colunas a partir das anotações.
    - Construir dinamicamente a consulta SQL.
    - Mapear os resultados do `ResultSet` para instâncias da classe.

### Passo 4: Utilizar o Repositório

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.util.List;

public class Main {
    public static void main(String[] args) {
        try {
            // Configuração da conexão (ajuste para o seu ambiente)
            String url = "jdbc:mysql://localhost:3306/meubanco";
            String user = "root";
            String password = "senha";

            Connection connection = DriverManager.getConnection(url, user, password);

            // Criar o repositório
            Repository<Usuario> usuarioRepository = new Repository<>(Usuario.class);

            // Obter todos os usuários
            List<Usuario> usuarios = usuarioRepository.findAll(connection);

            // Exibir os usuários
            for (Usuario usuario : usuarios) {
                System.out.println("ID: " + usuario.getId());
                System.out.println("Username: " + usuario.getUsername());
                System.out.println("Email: " + usuario.getEmail());
                System.out.println("-------------------------");
            }

            connection.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

**Notas:**

- Certifique-se de adicionar o driver JDBC do banco de dados que está usando (por exemplo, MySQL Connector).
- Ajuste a URL de conexão, usuário e senha conforme o seu ambiente.

---

## Benefícios do Uso de Anotações para Consultas Dinâmicas

- **Reutilização de Código**: O repositório genérico pode ser usado com qualquer entidade anotada.
- **Flexibilidade**: Facilita a alteração de nomes de tabelas e colunas sem modificar o código do repositório.
- **Redução de Erros**: Evita erros de digitação em nomes de colunas e tabelas.
- **Manutenção Simplificada**: As mudanças estruturais são centralizadas nas anotações das classes de modelo.

---

## Considerações Finais

- **Reflexão**: O uso de reflexão pode impactar o desempenho, mas para muitos casos é aceitável. Use com cuidado em aplicações de alto desempenho.
- **Segurança**: Certifique-se de validar entradas e proteger contra injeção de SQL, mesmo ao construir consultas dinamicamente.
- **Limitações**: Este exemplo é simplificado e não inclui tratamento de tipos complexos, relacionamentos ou outras funcionalidades de frameworks ORM completos.

---

## Conclusão

As anotações em Java são uma ferramenta poderosa que, mesmo sem frameworks, permite adicionar metadados ao código e criar mecanismos flexíveis e reutilizáveis. Neste guia, exploramos como criar anotações personalizadas para:

- **DisplayName**: Fornecer nomes amigáveis para campos e reutilizar código usando um scanner baseado em reflexão.
- **Table e Column**: Mapear classes e campos a tabelas e colunas de banco de dados, permitindo a geração de consultas SQL dinâmicas em um padrão Repository.

Esses conceitos podem ser estendidos e aprimorados para criar soluções mais robustas e adaptadas às necessidades específicas de sua aplicação.

---

## Recursos Adicionais

- **Documentação Oficial**: [Anotações em Java (Java Annotations)](https://docs.oracle.com/javase/tutorial/java/annotations/)
- **Reflexão em Java**: [Tutorial de Reflexão](https://docs.oracle.com/javase/tutorial/reflect/)
- **JDBC**: [Guia de JDBC](https://docs.oracle.com/javase/tutorial/jdbc/)

---

## Exercícios Práticos

1. **Extender o Repositório**:
    - Implemente métodos `save`, `update` e `delete` no repositório genérico.
    - Considere o tratamento de chaves primárias e autoincremento.

2. **Adicionar Suporte a Relacionamentos**:
    - Crie anotações para mapear relacionamentos `@OneToMany`, `@ManyToOne`, etc.
    - Modifique o repositório para lidar com esses relacionamentos.

3. **Validar Entradas com Anotações**:
    - Crie anotações como `@NotNull`, `@Size`, `@Email` e use reflexão para validar objetos antes de persistir no banco.

4. **Criar um Gerador de Formulários**:
    - Use a anotação `@DisplayName` e outras anotações personalizadas para gerar dinamicamente formulários HTML a partir das classes de modelo.

---

## Observações Finais

- **Boas Práticas**: Ao trabalhar com anotações e reflexão, mantenha o código organizado e documentado, pois a dinâmica pode tornar o código mais complexo de entender.
- **Desempenho**: Embora a reflexão seja poderosa, ela pode ser mais lenta do que o código estático. Use-a de forma consciente.
- **Segurança**: Sempre valide e sanitize as entradas do usuário, especialmente ao construir consultas SQL dinamicamente.

Espero que este guia tenha sido útil e que você possa aplicar esses conceitos em seus projetos para melhorar a reutilização e a flexibilidade do código!