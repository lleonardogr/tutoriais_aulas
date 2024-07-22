# Implementação de CRUD em Java Console Usando JDBC para Oracle

## Dependências

Para começar, você precisará incluir a dependência do JDBC para Oracle em seu `pom.xml`:

```xml
<dependency>
    <groupId>com.oracle.database.jdbc</groupId>
    <artifactId>ojdbc8</artifactId>
    <version>19.8.0.0</version>
</dependency>
```

## Conexão com o Banco de Dados
### DatabaseConfig

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

public class DatabaseConfig {
    private static final String URL = "jdbc:oracle:thin:@oracle.fiap.com.br:1521:ORCL";
    private static final String USER = "your_username";
    private static final String PASS = "your_password";

    public static Connection getConnection() throws SQLException {
        return DriverManager.getConnection(URL, USER, PASS);
    }
}
```

## Modelo
### User

``` java
public class User {
    private Long id;
    private String name;
    private String email;

    // Getters, setters, construtores e outros métodos
}
```

## Inicializar banco
```java
import java.sql.Connection;
import java.sql.Statement;

public class DatabaseInitialization {

    public static void initialize() {
        // SQL para criar a tabela "users"
        String createTableSQL = 
            "CREATE TABLE users (" +
            "id NUMBER(12) PRIMARY KEY," +
            "name VARCHAR2(255) NOT NULL," +
            "email VARCHAR2(255) NOT NULL UNIQUE" +
            ")";

        try (Connection conn = DatabaseConfig.getConnection();
             Statement stmt = conn.createStatement()) {
             
            // Criar a tabela
            stmt.execute(createTableSQL);
            System.out.println("Tabela 'users' criada com sucesso!");
            
        } catch (Exception e) {
            System.out.println("Erro ao criar a tabela: " + e.getMessage());
        }
    }

    public static void main(String[] args) {
        initialize();
    }
}
```

## Repositório
### Classe UserRepository

```java
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.util.ArrayList;
import java.util.List;

public class UserRepository {

    // Insere um novo usuário
    public void addUser(String name, String email) {
        String insertSQL = "INSERT INTO users (name, email) VALUES (?, ?)";
        
        try (Connection conn = DatabaseConfig.getConnection();
             PreparedStatement pstmt = conn.prepareStatement(insertSQL)) {
             
            pstmt.setString(1, name);
            pstmt.setString(2, email);
            
            pstmt.executeUpdate();
            System.out.println("Usuário inserido com sucesso!");
            
        } catch (Exception e) {
            System.out.println("Erro ao inserir usuário: " + e.getMessage());
        }
    }

    // Recupera um usuário pelo ID
    public User getUserById(int id) {
        String selectSQL = "SELECT * FROM users WHERE id = ?";
        User user = null;
        
        try (Connection conn = DatabaseConfig.getConnection();
             PreparedStatement pstmt = conn.prepareStatement(selectSQL)) {
             
            pstmt.setInt(1, id);
            ResultSet rs = pstmt.executeQuery();
            
            if (rs.next()) {
                user = new User(rs.getInt("id"), rs.getString("name"), rs.getString("email"));
            }
            
        } catch (Exception e) {
            System.out.println("Erro ao obter usuário: " + e.getMessage());
        }
        
        return user;
    }

    // Atualiza um usuário pelo ID
    public void updateUser(int id, String newName, String newEmail) {
        String updateSQL = "UPDATE users SET name = ?, email = ? WHERE id = ?";
        
        try (Connection conn = DatabaseConfig.getConnection();
             PreparedStatement pstmt = conn.prepareStatement(updateSQL)) {
             
            pstmt.setString(1, newName);
            pstmt.setString(2, newEmail);
            pstmt.setInt(3, id);
            
            pstmt.executeUpdate();
            System.out.println("Usuário atualizado com sucesso!");
            
        } catch (Exception e) {
            System.out.println("Erro ao atualizar usuário: " + e.getMessage());
        }
    }

    // Deleta um usuário pelo ID
    public void deleteUser(int id) {
        String deleteSQL = "DELETE FROM users WHERE id = ?";
        
        try (Connection conn = DatabaseConfig.getConnection();
             PreparedStatement pstmt = conn.prepareStatement(deleteSQL)) {
             
            pstmt.setInt(1, id);
            
            pstmt.executeUpdate();
            System.out.println("Usuário excluído com sucesso!");
            
        } catch (Exception e) {
            System.out.println("Erro ao excluir usuário: " + e.getMessage());
        }
    }

    // Lista todos os usuários
    public List<User> listAllUsers() {
        List<User> userList = new ArrayList<>();
        String selectAllSQL = "SELECT * FROM users";
        
        try (Connection conn = DatabaseConfig.getConnection();
             PreparedStatement pstmt = conn.prepareStatement(selectAllSQL)) {
             
            ResultSet rs = pstmt.executeQuery();
            
            while (rs.next()) {
                User user = new User(rs.getInt("id"), rs.getString("name"), rs.getString("email"));
                userList.add(user);
            }
            
        } catch (Exception e) {
            System.out.println("Erro ao listar usuários: " + e.getMessage());
        }
        
        return userList;
    }
}
```
Nota: Estou presumindo que existe uma classe User com atributos básicos (id, name e email) e os respectivos getters e setters, além do construtor utilizado nos métodos acima.

Esse repositório contém operações básicas CRUD. A utilização de PreparedStatement ajuda a evitar injeção SQL e torna a interação com o banco de dados mais segura e eficiente.

## Aplicação Console
### Main

``` java
import java.util.Scanner;

public class Main {
    public static void main(String[] args) {
        initialize();

        Scanner scanner = new Scanner(System.in);
        UserRepository userRepository = new UserRepository();

        while (true) {
            System.out.println("Escolha uma opção: ");
            System.out.println("1. Listar todos os usuários");
            System.out.println("2. Adicionar usuário");
       

            int choice = scanner.nextInt();

            switch (choice) {
                case 1:
                    List<User> users = userRepository.findAll();
                    for (User user : users) {
                        System.out.println(user);
                    }
                    break;
                case 2:
                    System.out.println("Digite o nome: ");
                    String name = scanner.next();
                    System.out.println("Digite o email: ");
                    String email = scanner.next();
                    User user = new User(name, email);
                    userRepository.save(user);
                    break;
                
            }
        }
    }
}
```

Notas:

Estou usando a classe DatabaseConfig que foi fornecida anteriormente para obter uma conexão com o banco de dados.
A função initialize cria a tabela "users". Você pode estender essa função para criar outras tabelas ou fazer outras configurações iniciais do banco de dados.
O método main é um ponto de entrada simples que chama initialize. Você pode executar esta classe uma vez para configurar seu banco de dados.
Lembre-se de ter cuidado ao executar scripts de inicialização, pois eles podem destruir dados existentes ou causar erros se a tabela já existir. Você pode querer adicionar verificações para ver se a tabela já existe antes de tentar criá-la.

