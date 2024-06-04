# Learning Spring Boot 🍃

This project was created to learn the basics of the Spring Boot framework, following Amigoscode’s Spring Boot for Beginners 2023 course.

### Tools
[![My Skills](https://skillicons.dev/icons?i=java,spring,docker,postgres,postman,vscode)](https://skillicons.dev)

## Java Files
The Java files follow a simple MVC-like architecture:

- **Model:** Customer.java
- **Controller:** Main.java
- **Repository:** CustomerRepository.java

### Customer.java
Annotations: ```@Entity``` defines the Entity Table, ```@Id``` designates the Primary Key, ```@SequenceGenerator``` creates the PK in a sequential manner, and ```@GeneratedValue``` specifies how the PK will be generated.

```java
package com.johnnery;

import jakarta.persistence.*;
import java.util.Objects;

@Entity
public class Customer {

     @Id
     @SequenceGenerator(
             name = "customer_id_sequence",
             sequenceName = "customer_id_sequence",
             allocationSize = 1
     )
     @GeneratedValue(
             strategy = GenerationType.SEQUENCE,
             generator = "customer_id_sequence"
     )
    private Integer id;
    private String name;
    private String email;
    private Integer age;

    public Customer(Integer id,
                    String name,
                    String email,
                    Integer age) {
        this.id = id;
        this.name = name;
        this.email = email;
        this.age = age;
    }

    public Customer() {
    }

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Customer customer = (Customer) o;
        return Objects.equals(id, customer.id) && Objects.equals(name, customer.name) && Objects.equals(email, customer.email) && Objects.equals(age, customer.age);
    }

    @Override
    public int hashCode() {
        return Objects.hash(id, name, email, age);
    }

    @Override
    public String toString() {
        return "Customer{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", email='" + email + '\'' +
                ", age=" + age +
                '}';
    }
}
  ```

### Main.java
Annotations: ```@RestController``` designates the class as a REST Controller, ```@RequestMapping``` is specifies the path where functions will make HTTP Requests, ```@GetMapping``` sets the function to do GET Requests, and the same are to ```@PostMapping``` for POST Requests, ```@DeleteMapping``` for DELETE Requests and ```@PutMapping``` for PUT Requests. Additionally, ```@RequestBody``` maps the HTTP Request Body to a object, and ```@PathVariable``` handle template variables in the request URI and set then as method parameters.

```java
package com.johnnery;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.EnableAutoConfiguration;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.bind.annotation.*;
import java.util.List;
import java.util.Objects;

@SpringBootApplication
@RestController
@RequestMapping("api/v1/customers")
public class Main {

    private final CustomerRepository customerRepository;

    public Main(CustomerRepository customerRepository) {
        this.customerRepository = customerRepository;
    }

    public static void main(String[] args){
        SpringApplication.run(Main.class, args);
    }

    @GetMapping
    public List<Customer> getCustomers(){
        return customerRepository.findAll();
    }

    record NewCustomerRequest(
            String name,
            String email,
            Integer age
    ) {

    }

    @PostMapping
    public void addCustomer(@RequestBody NewCustomerRequest request){
        Customer customer = new Customer();
        customer.setName(request.name());
        customer.setEmail(request.email());
        customer.setAge(request.age());
        customerRepository.save(customer);
    }

    @DeleteMapping("{customerId}")
    public void deleteCustomer(@PathVariable("customerId") Integer id){
        customerRepository.deleteById(id);
    }

    @PutMapping("{customerId}")
    public void updateCustomer(@PathVariable("customerId") Integer id, @RequestBody NewCustomerRequest request){
        Customer existingCustomer = customerRepository.findById(id).orElseThrow();
        existingCustomer.setName(request.name());
        existingCustomer.setEmail(request.email());
        existingCustomer.setAge(request.age());
        customerRepository.save(existingCustomer);
    }
}

```

### CustomerRepository.java
```JpaRepository``` extends the ```CrudRepository``` and receives the domain class and the ID type that it should handle.]

```java
package com.johnnery;

import org.springframework.data.jpa.repository.JpaRepository;

public interface CustomerRepository extends JpaRepository<Customer, Integer> {
    
}
```

## Other Files
### pom.xml
The Maven configuration for dependencies, plugins, etc.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>3.2.5</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>
	<groupId>com.example</groupId>
	<artifactId>SpringBeginners</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<name>Spring Boot for Beginners</name>
	<description>Spring Boot for Beginners</description>
	<properties>
		<java.version>22</java.version>
	</properties>
	<dependencies>
		<dependency>
			<groupId>org.postgresql</groupId>
			<artifactId>postgresql</artifactId>
			<scope>runtime</scope>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-data-jpa</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
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

### docker-compose.yml
The Docker configuration to manage the PostgreSQL container.

```yaml
services:
  db:
    container_name: postgres
    image: postgres
    environment:
      POSTGRES_USER: amigoscode
      POSTGRES_PASSWORD: password
      PGDATA: /data/postgres
    volumes:
      - db:/data/postgres
    ports:
      - "5332:5432"
    networks:
      - db
    restart: unless-stopped

networks:
  db:
    driver: bridge

volumes:
  db:
```
