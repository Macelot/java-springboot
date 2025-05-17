---

# 🧑‍💻 Tutorial: CRUD de Clientes com Spring Boot + MySQL + Frontend em HTML

* Java 17 ou superior
* MySQL 5.7.36
* VSCode com as extensões:

  * **Extension Pack for Java** (Microsoft)
  * **Spring Boot Extension Pack**
  * **Language Support for Java(TM) by Red Hat**
  * **Debugger for Java**
  * **Maven for Java**
  * **Live Server** (para testar o frontend)

---

## 🗂️ Estrutura do Projeto

```
cliente-crud/
├── backend/
│   └── src/
│       └── main/
│           ├── java/
│           │   └── com.exemplo.cliente/
│           │       ├── ClienteCrudApplication.java
│           │       ├── controller/
│           │       │   └── ClienteController.java
│           │       ├── model/
│           │       │   └── Cliente.java
│           │       └── repository/
│           │           └── ClienteRepository.java
│           └── resources/
│               ├── application.properties
│               └── static/
├── index.html
│  
└── pom.xml
```

---

## 📦 `pom.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>


	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.7.18</version>
	</parent>

	<groupId>com.example</groupId>
	<artifactId>demo</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<name>demo</name>
	<description>Demo project for Spring Boot</description>
	<url />
	<licenses>
		<license />
	</licenses>
	<developers>
		<developer />
	</developers>
	<scm>
		<connection />
		<developerConnection />
		<tag />
		<url />
	</scm>
	<properties>
		<java.version>17</java.version>
	</properties>
	<dependencies>
		<!-- Spring Boot + Web + JPA -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-data-jpa</artifactId>
		</dependency>

		<!-- MySQL Connector -->
		<dependency>
			<groupId>com.mysql</groupId>
			<artifactId>mysql-connector-j</artifactId>
			<version>8.0.33</version>
		</dependency>

		<!-- Ferramentas de desenvolvimento -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-devtools</artifactId>
			<scope>runtime</scope>
		</dependency>

		<!-- Framework de persistência de dados -->
		<dependency>
			<groupId>org.hibernate.orm</groupId>
			<artifactId>hibernate-core</artifactId>
			<version>6.4.0.Final</version>
		</dependency>
	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>

</project>
```

---

## ⚙️ `application.properties`

```properties
spring.application.name=demo
spring.datasource.url=jdbc:mysql://localhost:3306/test
spring.datasource.username=root
spring.datasource.password=usbw

spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5Dialect

server.port=8080
```


---

## 🧩 Entidade: `Cliente.java`

```java
package com.example.demo.model;

import javax.persistence.Entity;
import javax.persistence.Id;

@Entity
public class Cliente {
    @Id
    private Long id;
    private String nome;
    private String email;

    // Getters e Setters
}
```

---

## 📂 Repositório: `ClienteRepository.java`

```java
package com.example.demo.repository;


import com.example.demo.model.Cliente;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;


public interface ClienteRepository extends JpaRepository<Cliente, Long> {
}
```

---

## 🎯 Controlador: `ClienteController.java`

package com.example.demo.controller;

import com.example.demo.model.Cliente;
import com.example.demo.repository.ClienteRepository;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/api/clientes")
@CrossOrigin(origins = "*")
public class ClienteController {

    private final ClienteRepository repo;

    public ClienteController(ClienteRepository repo) {
        this.repo = repo;
    }

    @GetMapping
    public List<Cliente> listar() {
        return repo.findAll();
    }

    @PostMapping
    public Cliente salvar(@RequestBody Cliente cliente) {
        return repo.save(cliente);
    }

    @DeleteMapping("/{id}")
    public void deletar(@PathVariable Long id) {
        repo.deleteById(id);
    }

    @PutMapping("/{id}")
    public Cliente atualizar(@PathVariable Long id, @RequestBody Cliente cliente) {
        cliente.setId(id);
        return repo.save(cliente);
    }
}
```

---

## 🏁 Classe Principal: `DemoApplication.java`

```java
package com.example.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class DemoApplication {
	public static void main(String[] args) {
		SpringApplication.run(DemoApplication.class, args);
	}
}
```

---

## 🌐 Frontend: `index.html`

```html
<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <title>Clientes</title>
</head>
<body>
    <h1>Cadastro de Clientes</h1>

    <input type="text" id="nome" placeholder="Nome">
    <input type="text" id="email" placeholder="Email">
    <button onclick="salvarCliente()">Salvar</button>

    <h2>Lista de Clientes</h2>
    <ul id="lista"></ul>

    <script>
        const API = "http://localhost:8080/api/clientes";

        function listarClientes() {
            fetch(API)
                .then(res => res.json())
                .then(dados => {
                    const lista = document.getElementById("lista");
                    lista.innerHTML = "";
                    dados.forEach(c => {
                        const item = document.createElement("li");
                        item.innerText = `${c.nome} (${c.email})`;
                        lista.appendChild(item);
                    });
                });
        }

        function salvarCliente() {
            const nome = document.getElementById("nome").value;
            const email = document.getElementById("email").value;
            fetch(API, {
                method: "POST",
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify({ id: Date.now(), nome, email })
            }).then(() => listarClientes());
        }

        listarClientes();
    </script>
</body>
</html>
```

---

## 🚀 Executando o Projeto

### 1. No terminal (VSCode):

```bash
cd backend
./mvnw spring-boot:run
```

### 2. No frontend (VSCode):

Clique com botão direito em `index.html` > **"Open with Live Server"** ou abra diretamente no navegador.

http://127.0.0.1:5500/
Confira no banco de dados http://localhost/phpmyadmin

---

## ✅ Teste Rápido com cURL

```bash
curl -X POST http://localhost:8080/api/clientes \
-H "Content-Type: application/json" \
-d '{"id":1,"nome":"João","email":"joao@email.com"}'
```

---

Caso tiver problemas
mvn clean install

Próximos passos. Personalizar o index.html com Bootstrap, Colocar mais funcionalidades (Delete, Update). Desenvolver filtros para buscar Clientes. Adicionar colunas no Cliente etc...

Exemplo com Delete e Update 
```html
<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <title>Clientes</title>
    <style>
        li {
            margin-bottom: 10px;
        }
        button {
            margin-left: 5px;
        }
    </style>
</head>
<body>
    <h1>Cadastro de Clientes</h1>

    <input type="hidden" id="clienteId">
    <input type="text" id="nome" placeholder="Nome">
    <input type="text" id="email" placeholder="Email">
    <button onclick="salvarCliente()">Salvar</button>

    <h2>Lista de Clientes</h2>
    <ul id="lista"></ul>

    <script>
        const API = "http://localhost:8080/api/clientes";

        function listarClientes() {
            fetch(API)
                .then(res => res.json())
                .then(dados => {
                    const lista = document.getElementById("lista");
                    lista.innerHTML = "";
                    dados.forEach(c => {
                        const item = document.createElement("li");
                        item.innerHTML = `
                            ${c.nome} (${c.email})
                            <button onclick="editarCliente(${c.id}, '${c.nome}', '${c.email}')">Editar</button>
                            <button onclick="deletarCliente(${c.id})">Excluir</button>
                        `;
                        lista.appendChild(item);
                    });
                });
        }

        function salvarCliente() {
            const id = document.getElementById("clienteId").value;
            const nome = document.getElementById("nome").value;
            const email = document.getElementById("email").value;

            const metodo = id ? "PUT" : "POST";
            const url = id ? `${API}/${id}` : API;

            fetch(url, {
                method: metodo,
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify({ id, nome, email })
            })
            .then(() => {
                listarClientes();
                limparFormulario();
            });
        }

        function editarCliente(id, nome, email) {
            document.getElementById("clienteId").value = id;
            document.getElementById("nome").value = nome;
            document.getElementById("email").value = email;
        }

        function deletarCliente(id) {
            if (confirm("Deseja realmente excluir este cliente?")) {
                fetch(`${API}/${id}`, { method: "DELETE" })
                    .then(() => listarClientes());
            }
        }

        function limparFormulario() {
            document.getElementById("clienteId").value = "";
            document.getElementById("nome").value = "";
            document.getElementById("email").value = "";
        }

        listarClientes();
    </script>
</body>
</html>
```
