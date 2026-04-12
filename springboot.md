# Spring Boot Interview Topics: Basic to Advanced

A comprehensive guide covering all major Spring Boot concepts for interview preparation.

---

## Table of Contents

1. [Introduction to Spring Boot](#1-introduction-to-spring-boot)
2. [Spring Core Concepts](#2-spring-core-concepts)
3. [Dependency Injection & IoC](#3-dependency-injection--ioc)
4. [Spring Boot Auto Configuration](#4-spring-boot-auto-configuration)
5. [Spring Boot Annotations](#5-spring-boot-annotations)
6. [Spring MVC & REST API](#6-spring-mvc--rest-api)
7. [Request Handling & Validation](#7-request-handling--validation)
8. [Spring Data JPA](#8-spring-data-jpa)
9. [Spring Security](#9-spring-security)
10. [Exception Handling](#10-exception-handling)
11. [Spring Boot Actuator](#11-spring-boot-actuator)
12. [Logging](#12-logging)
13. [Profiles & Configuration](#13-profiles--configuration)
14. [Caching](#14-caching)
15. [Spring Boot Testing](#15-spring-boot-testing)
16. [Microservices with Spring Boot](#16-microservices-with-spring-boot)
17. [Spring Cloud](#17-spring-cloud)
18. [Messaging — Kafka & RabbitMQ](#18-messaging--kafka--rabbitmq)
19. [Spring Boot with Docker](#19-spring-boot-with-docker)
20. [Performance & Best Practices](#20-performance--best-practices)

---

## 1. Introduction to Spring Boot

### What is Spring Boot?

Spring Boot is an **opinionated framework** built on top of the Spring Framework that simplifies the setup, configuration, and development of Spring-based applications. It eliminates the need for boilerplate configuration and allows developers to get a production-ready application running with minimal setup.

### Spring vs Spring MVC vs Spring Boot

| Feature | Spring Framework | Spring MVC | Spring Boot |
|---------|-----------------|------------|-------------|
| Purpose | Core DI/IoC container | Web layer (MVC pattern) | Full application framework |
| Configuration | Manual XML or Java config | Additional MVC config needed | Auto-configured |
| Server | External (Tomcat WAR) | External (Tomcat WAR) | Embedded (Tomcat/Jetty) |
| Deployment | WAR file | WAR file | JAR file (self-contained) |
| Boilerplate | High | High | Minimal |
| Production Ready | Manual setup | Manual setup | Built-in (Actuator) |

### Key Features of Spring Boot

**1. Auto Configuration**
Spring Boot automatically configures your application based on the dependencies present in the classpath. If you add `spring-boot-starter-data-jpa`, it auto-configures a DataSource, EntityManagerFactory, and TransactionManager.

**2. Starter Dependencies**
Pre-packaged dependency descriptors that bring in all required libraries for a specific functionality:
```xml
<!-- Brings in Spring MVC, Tomcat, Jackson, etc. -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

**3. Embedded Server**
No need to deploy WAR to an external server. The application runs as a standalone JAR with an embedded Tomcat/Jetty/Undertow server.

**4. Production-Ready Features**
Spring Boot Actuator provides health checks, metrics, and monitoring endpoints out of the box.

**5. Opinionated Defaults**
Sensible defaults for configuration — works without any configuration, but everything is customizable.

### Creating a Spring Boot Application

```bash
# Using Spring Initializr (start.spring.io)
# Or using Spring Boot CLI
spring init --dependencies=web,data-jpa,mysql myapp

# Or using Maven
mvn spring-boot:run

# Build and run JAR
mvn clean package
java -jar target/myapp-0.0.1-SNAPSHOT.jar
```

### Main Application Class

```java
package com.example.myapp;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication  // Combination of 3 annotations
public class MyApplication {
    public static void main(String[] args) {
        SpringApplication.run(MyApplication.class, args);
    }
}
```

`@SpringBootApplication` is equivalent to:
- `@Configuration` — marks class as a source of bean definitions
- `@EnableAutoConfiguration` — enables Spring Boot's auto-configuration
- `@ComponentScan` — scans the package and sub-packages for components

---

## 2. Spring Core Concepts

### What is the Spring Framework?

The Spring Framework is a comprehensive Java application framework that provides infrastructure support. Its core features are:

**1. IoC (Inversion of Control) Container**
The container manages the lifecycle and configuration of application objects (beans). Instead of objects creating their own dependencies, the container injects them.

**2. AOP (Aspect-Oriented Programming)**
Allows separation of cross-cutting concerns (logging, security, transactions) from business logic.

**3. Spring Beans**
Any object managed by the Spring IoC container is called a Bean. Beans are created, wired, and managed by the container.

### Bean Lifecycle

```
Container Start
      ↓
Bean Instantiation (constructor called)
      ↓
Dependency Injection (setter/field injection)
      ↓
@PostConstruct method called
      ↓
Bean Ready for Use
      ↓
@PreDestroy method called
      ↓
Bean Destroyed
      ↓
Container Shutdown
```

```java
import jakarta.annotation.PostConstruct;
import jakarta.annotation.PreDestroy;
import org.springframework.stereotype.Component;

@Component
public class DatabaseService {

    @PostConstruct
    public void init() {
        System.out.println("Bean initialized — opening DB connection");
    }

    @PreDestroy
    public void cleanup() {
        System.out.println("Bean destroyed — closing DB connection");
    }
}
```

### Bean Scopes

| Scope | Description | Use Case |
|-------|-------------|----------|
| `singleton` | One instance per Spring container (default) | Stateless services |
| `prototype` | New instance each time bean is requested | Stateful objects |
| `request` | One instance per HTTP request | Web — request-scoped data |
| `session` | One instance per HTTP session | Web — user session data |
| `application` | One instance per ServletContext | Web — app-wide state |

```java
import org.springframework.context.annotation.Scope;
import org.springframework.stereotype.Component;

@Component
@Scope("prototype")
public class ShoppingCart {
    private List<Item> items = new ArrayList<>();
    // Each request gets a fresh shopping cart
}
```

### ApplicationContext

The `ApplicationContext` is the Spring IoC container. It is responsible for instantiating, configuring, and assembling beans.

```java
// Accessing beans programmatically
@SpringBootApplication
public class MyApplication implements ApplicationRunner {

    @Autowired
    private ApplicationContext context;

    public static void main(String[] args) {
        SpringApplication.run(MyApplication.class, args);
    }

    @Override
    public void run(ApplicationArguments args) {
        // Get bean from context
        UserService userService = context.getBean(UserService.class);
        System.out.println("Beans: " + Arrays.toString(context.getBeanDefinitionNames()));
    }
}
```

---

## 3. Dependency Injection & IoC

### What is Dependency Injection?

Dependency Injection (DI) is a design pattern where an object's dependencies are provided (injected) by an external entity (the Spring container) rather than the object creating them itself.

**Without DI (Tight Coupling):**
```java
public class OrderService {
    // Creates its own dependency — tightly coupled
    private PaymentService paymentService = new PaymentService();
}
```

**With DI (Loose Coupling):**
```java
@Service
public class OrderService {
    private final PaymentService paymentService;

    // Spring injects PaymentService — loosely coupled
    public OrderService(PaymentService paymentService) {
        this.paymentService = paymentService;
    }
}
```

### Types of Dependency Injection

#### 1. Constructor Injection (Recommended)

```java
@Service
public class UserService {

    private final UserRepository userRepository;
    private final EmailService emailService;

    // Spring automatically injects both dependencies
    public UserService(UserRepository userRepository, EmailService emailService) {
        this.userRepository = userRepository;
        this.emailService = emailService;
    }
}
```

**Why Constructor Injection is preferred:**
- Dependencies are immutable (final fields)
- All required dependencies must be provided at creation time
- Makes testing easy (can pass mock objects)
- Detects circular dependencies at startup (not at runtime)
- No need for `@Autowired` when there is only one constructor (Spring 4.3+)

#### 2. Setter Injection

```java
@Service
public class NotificationService {

    private EmailService emailService;

    @Autowired
    public void setEmailService(EmailService emailService) {
        this.emailService = emailService;
    }
}
```

Use setter injection for optional dependencies.

#### 3. Field Injection (Not Recommended)

```java
@Service
public class ProductService {

    @Autowired  // Spring injects directly into field via reflection
    private ProductRepository productRepository;
}
```

**Why field injection is discouraged:**
- Cannot make field final (mutable)
- Hard to test (cannot inject mocks without reflection)
- Hides dependencies (not visible in constructor)
- Can lead to NullPointerException if used outside Spring context

### @Autowired, @Qualifier, @Primary

```java
// Multiple implementations of the same interface
@Component("emailNotifier")
public class EmailNotificationService implements NotificationService { }

@Component("smsNotifier")
public class SmsNotificationService implements NotificationService { }

// Inject specific implementation using @Qualifier
@Service
public class AlertService {

    private final NotificationService notificationService;

    public AlertService(@Qualifier("smsNotifier") NotificationService notificationService) {
        this.notificationService = notificationService;
    }
}

// Or mark one as primary (default choice)
@Primary
@Component
public class EmailNotificationService implements NotificationService { }
```

### @Bean vs @Component

| Feature | @Bean | @Component |
|---------|-------|------------|
| Where used | Inside `@Configuration` class on a method | On the class itself |
| Control | Full control over bean creation | Spring manages creation |
| Use case | Third-party class beans, custom config | Your own classes |

```java
// @Component — on your own class
@Component
public class MyService { }

// @Bean — for third-party or custom creation
@Configuration
public class AppConfig {

    @Bean
    public ObjectMapper objectMapper() {
        ObjectMapper mapper = new ObjectMapper();
        mapper.setSerializationInclusion(JsonInclude.Include.NON_NULL);
        return mapper;
    }

    @Bean
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
}
```

---

## 4. Spring Boot Auto Configuration

### How Auto Configuration Works

Auto configuration is the process by which Spring Boot automatically configures the application based on the JARs present in the classpath.

**Internal Mechanism:**
1. `@SpringBootApplication` triggers `@EnableAutoConfiguration`
2. Spring Boot loads `META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports`
3. This file lists hundreds of `AutoConfiguration` classes
4. Each auto-config class uses `@ConditionalOn*` annotations to decide whether to activate

```java
// How DataSourceAutoConfiguration works internally (simplified)
@AutoConfiguration
@ConditionalOnClass({ DataSource.class, EmbeddedDatabaseType.class })
@ConditionalOnMissingBean(type = "io.r2dbc.spi.ConnectionFactory")
@EnableConfigurationProperties(DataSourceProperties.class)
public class DataSourceAutoConfiguration {

    @Bean
    @ConditionalOnMissingBean  // Only if no DataSource bean already exists
    public DataSource dataSource(DataSourceProperties properties) {
        return properties.initializeDataSourceBuilder().build();
    }
}
```

### Conditional Annotations

| Annotation | Condition |
|------------|-----------|
| `@ConditionalOnClass` | Activates if specified class is on classpath |
| `@ConditionalOnMissingClass` | Activates if specified class is NOT on classpath |
| `@ConditionalOnBean` | Activates if specified bean exists |
| `@ConditionalOnMissingBean` | Activates if specified bean does NOT exist |
| `@ConditionalOnProperty` | Activates if specified property has a given value |
| `@ConditionalOnWebApplication` | Activates if it is a web application |
| `@ConditionalOnExpression` | Activates based on a SpEL expression |

```java
// Custom conditional bean
@Configuration
public class PaymentConfig {

    @Bean
    @ConditionalOnProperty(name = "payment.gateway", havingValue = "stripe")
    public PaymentService stripePaymentService() {
        return new StripePaymentService();
    }

    @Bean
    @ConditionalOnProperty(name = "payment.gateway", havingValue = "razorpay")
    public PaymentService razorpayPaymentService() {
        return new RazorpayPaymentService();
    }
}
```

### application.properties vs application.yml

```properties
# application.properties
server.port=8080
spring.application.name=my-app
spring.datasource.url=jdbc:mysql://localhost:3306/mydb
spring.datasource.username=root
spring.datasource.password=secret
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
```

```yaml
# application.yml (equivalent — more readable for nested config)
server:
  port: 8080

spring:
  application:
    name: my-app
  datasource:
    url: jdbc:mysql://localhost:3306/mydb
    username: root
    password: secret
  jpa:
    hibernate:
      ddl-auto: update
    show-sql: true
```

### @ConfigurationProperties

Bind a group of related properties to a Java class.

```yaml
# application.yml
app:
  mail:
    host: smtp.gmail.com
    port: 587
    username: noreply@example.com
    enabled: true
```

```java
@ConfigurationProperties(prefix = "app.mail")
@Component
public class MailProperties {
    private String host;
    private int port;
    private String username;
    private boolean enabled;

    // Getters and setters
    public String getHost() { return host; }
    public void setHost(String host) { this.host = host; }
    // ... other getters/setters
}

// Usage
@Service
public class MailService {
    @Autowired
    private MailProperties mailProperties;

    public void send() {
        System.out.println("Sending via: " + mailProperties.getHost());
    }
}
```

---

## 5. Spring Boot Annotations

### Stereotype Annotations

| Annotation | Purpose | Layer |
|------------|---------|-------|
| `@Component` | Generic Spring-managed component | Any |
| `@Service` | Business logic layer | Service |
| `@Repository` | Data access layer — translates DB exceptions | DAO/Repository |
| `@Controller` | MVC controller — returns views | Web (MVC) |
| `@RestController` | REST API controller — returns JSON/XML | Web (REST) |

```java
// @Repository — automatically translates DB exceptions to Spring's DataAccessException
@Repository
public class UserRepositoryImpl {
    // JdbcTemplate operations here
    // SQLExceptions are translated to DataAccessException automatically
}

// @Service — marks as business logic layer
@Service
public class UserService {
    @Autowired
    private UserRepository userRepository;
}

// @RestController = @Controller + @ResponseBody
@RestController
@RequestMapping("/api/users")
public class UserController {
    // All methods return JSON automatically
}
```

### Configuration Annotations

```java
// @Configuration — source of bean definitions
@Configuration
public class SecurityConfig {
    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}

// @Value — inject individual property values
@Component
public class AppConfig {
    @Value("${app.name}")
    private String appName;

    @Value("${server.port:8080}")  // Default value 8080
    private int port;

    @Value("#{T(java.lang.Math).PI}")  // SpEL expression
    private double pi;
}

// @PropertySource — load custom properties file
@Configuration
@PropertySource("classpath:custom.properties")
public class CustomConfig { }
```

### Spring MVC Annotations

```java
@RestController
@RequestMapping("/api/products")
public class ProductController {

    @GetMapping                          // GET /api/products
    public List<Product> getAll() { }

    @GetMapping("/{id}")                 // GET /api/products/1
    public Product getById(@PathVariable Long id) { }

    @PostMapping                         // POST /api/products
    public Product create(@RequestBody Product product) { }

    @PutMapping("/{id}")                 // PUT /api/products/1
    public Product update(@PathVariable Long id, @RequestBody Product product) { }

    @DeleteMapping("/{id}")              // DELETE /api/products/1
    public void delete(@PathVariable Long id) { }

    @GetMapping("/search")               // GET /api/products/search?name=phone
    public List<Product> search(@RequestParam String name) { }

    @GetMapping("/filter")
    public List<Product> filter(
        @RequestParam(required = false) String category,
        @RequestParam(defaultValue = "0") int page,
        @RequestParam(defaultValue = "10") int size
    ) { }
}
```

### AOP Annotations

```java
@Aspect
@Component
public class LoggingAspect {

    // Pointcut: matches all methods in service package
    @Before("execution(* com.example.service.*.*(..))")
    public void logBefore(JoinPoint joinPoint) {
        System.out.println("Calling: " + joinPoint.getSignature().getName());
    }

    @After("execution(* com.example.service.*.*(..))")
    public void logAfter(JoinPoint joinPoint) {
        System.out.println("Completed: " + joinPoint.getSignature().getName());
    }

    @Around("execution(* com.example.service.*.*(..))")
    public Object logExecutionTime(ProceedingJoinPoint joinPoint) throws Throwable {
        long start = System.currentTimeMillis();
        Object result = joinPoint.proceed();  // Execute actual method
        long time = System.currentTimeMillis() - start;
        System.out.println(joinPoint.getSignature().getName() + " took " + time + "ms");
        return result;
    }

    @AfterThrowing(pointcut = "execution(* com.example.service.*.*(..))", throwing = "ex")
    public void logException(JoinPoint joinPoint, Exception ex) {
        System.out.println("Exception in " + joinPoint.getSignature().getName() + ": " + ex.getMessage());
    }
}
```

---

## 6. Spring MVC & REST API

### MVC Architecture

```
Client Request
      ↓
DispatcherServlet (Front Controller)
      ↓
HandlerMapping (finds the right controller)
      ↓
Controller (processes request, calls service)
      ↓
Service Layer (business logic)
      ↓
Repository Layer (database access)
      ↓
Response (JSON/XML) ← ModelAndView ← Controller
      ↑
MessageConverter (converts Java object to JSON)
```

### Building a Complete REST API

```java
// Entity
@Entity
@Table(name = "employees")
public class Employee {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false)
    private String name;

    private String email;
    private Double salary;

    // Constructors, getters, setters
}

// Repository
@Repository
public interface EmployeeRepository extends JpaRepository<Employee, Long> {
    List<Employee> findByDepartment(String department);
    Optional<Employee> findByEmail(String email);
}

// Service
@Service
public class EmployeeService {

    private final EmployeeRepository employeeRepository;

    public EmployeeService(EmployeeRepository employeeRepository) {
        this.employeeRepository = employeeRepository;
    }

    public List<Employee> getAllEmployees() {
        return employeeRepository.findAll();
    }

    public Employee getEmployeeById(Long id) {
        return employeeRepository.findById(id)
            .orElseThrow(() -> new ResourceNotFoundException("Employee not found with id: " + id));
    }

    public Employee createEmployee(Employee employee) {
        return employeeRepository.save(employee);
    }

    public Employee updateEmployee(Long id, Employee employeeDetails) {
        Employee employee = getEmployeeById(id);
        employee.setName(employeeDetails.getName());
        employee.setEmail(employeeDetails.getEmail());
        employee.setSalary(employeeDetails.getSalary());
        return employeeRepository.save(employee);
    }

    public void deleteEmployee(Long id) {
        Employee employee = getEmployeeById(id);
        employeeRepository.delete(employee);
    }
}

// Controller
@RestController
@RequestMapping("/api/v1/employees")
public class EmployeeController {

    private final EmployeeService employeeService;

    public EmployeeController(EmployeeService employeeService) {
        this.employeeService = employeeService;
    }

    @GetMapping
    public ResponseEntity<List<Employee>> getAllEmployees() {
        return ResponseEntity.ok(employeeService.getAllEmployees());
    }

    @GetMapping("/{id}")
    public ResponseEntity<Employee> getEmployeeById(@PathVariable Long id) {
        return ResponseEntity.ok(employeeService.getEmployeeById(id));
    }

    @PostMapping
    public ResponseEntity<Employee> createEmployee(@Valid @RequestBody Employee employee) {
        Employee created = employeeService.createEmployee(employee);
        URI location = ServletUriComponentsBuilder
            .fromCurrentRequest()
            .path("/{id}")
            .buildAndExpand(created.getId())
            .toUri();
        return ResponseEntity.created(location).body(created);
    }

    @PutMapping("/{id}")
    public ResponseEntity<Employee> updateEmployee(
            @PathVariable Long id,
            @Valid @RequestBody Employee employee) {
        return ResponseEntity.ok(employeeService.updateEmployee(id, employee));
    }

    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteEmployee(@PathVariable Long id) {
        employeeService.deleteEmployee(id);
        return ResponseEntity.noContent().build();
    }
}
```

### HTTP Status Codes Best Practices

| Scenario | Status Code |
|----------|------------|
| Successful GET | 200 OK |
| Successful POST (created) | 201 Created |
| Successful DELETE | 204 No Content |
| Bad request / validation error | 400 Bad Request |
| Unauthorized (no token) | 401 Unauthorized |
| Forbidden (no permission) | 403 Forbidden |
| Resource not found | 404 Not Found |
| Method not allowed | 405 Method Not Allowed |
| Internal server error | 500 Internal Server Error |

### ResponseEntity

`ResponseEntity` gives full control over the HTTP response — status code, headers, and body.

```java
// Different ways to return ResponseEntity
return ResponseEntity.ok(data);                                    // 200 with body
return ResponseEntity.created(uri).body(savedObject);             // 201 with Location header
return ResponseEntity.noContent().build();                         // 204 no body
return ResponseEntity.notFound().build();                          // 404 no body
return ResponseEntity.badRequest().body(errorMessage);             // 400 with body
return ResponseEntity.status(HttpStatus.ACCEPTED).body(data);      // 202 with body

// With custom headers
HttpHeaders headers = new HttpHeaders();
headers.add("X-Custom-Header", "value");
return ResponseEntity.ok().headers(headers).body(data);
```

---

## 7. Request Handling & Validation

### Bean Validation

```xml
<!-- Maven dependency -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

```java
// DTO with validation annotations
public class CreateUserRequest {

    @NotNull(message = "Name is required")
    @NotBlank(message = "Name cannot be blank")
    @Size(min = 2, max = 50, message = "Name must be between 2 and 50 characters")
    private String name;

    @NotNull(message = "Email is required")
    @Email(message = "Email must be valid")
    private String email;

    @NotNull(message = "Password is required")
    @Size(min = 8, message = "Password must be at least 8 characters")
    @Pattern(regexp = ".*[A-Z].*", message = "Password must contain at least one uppercase letter")
    private String password;

    @NotNull(message = "Age is required")
    @Min(value = 18, message = "Age must be at least 18")
    @Max(value = 120, message = "Age must be less than 120")
    private Integer age;

    @Positive(message = "Salary must be positive")
    private Double salary;

    @PastOrPresent(message = "Date of birth must be in past")
    private LocalDate dateOfBirth;

    // Getters and setters
}

// Controller using validation
@PostMapping("/users")
public ResponseEntity<User> createUser(@Valid @RequestBody CreateUserRequest request) {
    // @Valid triggers validation — if fails, MethodArgumentNotValidException is thrown
    User user = userService.create(request);
    return ResponseEntity.status(HttpStatus.CREATED).body(user);
}
```

### Custom Validator

```java
// Custom annotation
@Target({ElementType.FIELD, ElementType.PARAMETER})
@Retention(RetentionPolicy.RUNTIME)
@Constraint(validatedBy = PhoneNumberValidator.class)
public @interface ValidPhone {
    String message() default "Invalid phone number";
    Class<?>[] groups() default {};
    Class<? extends Payload>[] payload() default {};
}

// Validator implementation
public class PhoneNumberValidator implements ConstraintValidator<ValidPhone, String> {

    @Override
    public boolean isValid(String phone, ConstraintValidatorContext context) {
        if (phone == null) return true; // Handle null separately with @NotNull
        return phone.matches("^[+]?[0-9]{10,15}$");
    }
}

// Usage
public class UserRequest {
    @ValidPhone
    private String phoneNumber;
}
```

### @PathVariable vs @RequestParam vs @RequestBody

```java
@RestController
@RequestMapping("/api")
public class DemoController {

    // @PathVariable — part of the URL path
    // GET /api/users/123
    @GetMapping("/users/{id}")
    public User getUser(@PathVariable Long id) {
        return userService.findById(id);
    }

    // @RequestParam — query string parameter
    // GET /api/users?page=1&size=10&sort=name
    @GetMapping("/users")
    public Page<User> getUsers(
        @RequestParam(defaultValue = "0") int page,
        @RequestParam(defaultValue = "10") int size,
        @RequestParam(required = false) String sort
    ) {
        return userService.findAll(page, size, sort);
    }

    // @RequestBody — JSON body of request
    // POST /api/users with JSON body
    @PostMapping("/users")
    public User createUser(@Valid @RequestBody CreateUserRequest request) {
        return userService.create(request);
    }

    // @RequestHeader — HTTP header value
    // GET /api/users with Authorization header
    @GetMapping("/profile")
    public User getProfile(@RequestHeader("Authorization") String token) {
        return userService.findByToken(token);
    }
}
```

---

## 8. Spring Data JPA

### What is JPA?

JPA (Java Persistence API) is a specification for ORM (Object-Relational Mapping) in Java. Hibernate is the most popular implementation of JPA. Spring Data JPA provides a layer on top of JPA to simplify data access.

```
Your Code → Spring Data JPA → JPA (Specification) → Hibernate (Implementation) → Database
```

### Entity Mapping

```java
@Entity
@Table(name = "products",
       uniqueConstraints = @UniqueConstraint(columnNames = "sku"))
public class Product {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(name = "product_name", nullable = false, length = 100)
    private String name;

    @Column(precision = 10, scale = 2)
    private BigDecimal price;

    @Column(name = "created_at", updatable = false)
    @CreationTimestamp
    private LocalDateTime createdAt;

    @UpdateTimestamp
    private LocalDateTime updatedAt;

    // Enum mapping
    @Enumerated(EnumType.STRING)
    private ProductStatus status;

    // One product belongs to one category
    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "category_id")
    private Category category;

    // One product has many reviews
    @OneToMany(mappedBy = "product", cascade = CascadeType.ALL, orphanRemoval = true)
    private List<Review> reviews = new ArrayList<>();
}
```

### JPA Relationships

```java
// ONE-TO-ONE
@Entity
public class User {
    @Id @GeneratedValue
    private Long id;

    @OneToOne(cascade = CascadeType.ALL)
    @JoinColumn(name = "profile_id", referencedColumnName = "id")
    private UserProfile profile;
}

// ONE-TO-MANY / MANY-TO-ONE
@Entity
public class Department {
    @Id @GeneratedValue
    private Long id;

    @OneToMany(mappedBy = "department", cascade = CascadeType.ALL)
    private List<Employee> employees = new ArrayList<>();
}

@Entity
public class Employee {
    @Id @GeneratedValue
    private Long id;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "dept_id")
    private Department department;
}

// MANY-TO-MANY
@Entity
public class Student {
    @Id @GeneratedValue
    private Long id;

    @ManyToMany
    @JoinTable(
        name = "student_course",
        joinColumns = @JoinColumn(name = "student_id"),
        inverseJoinColumns = @JoinColumn(name = "course_id")
    )
    private Set<Course> courses = new HashSet<>();
}
```

### JpaRepository Methods

```java
@Repository
public interface ProductRepository extends JpaRepository<Product, Long> {

    // Derived query methods (Spring generates SQL automatically)
    List<Product> findByCategory(String category);
    List<Product> findByPriceBetween(Double min, Double max);
    List<Product> findByNameContainingIgnoreCase(String name);
    Optional<Product> findBySkuAndActiveTrue(String sku);
    List<Product> findTop10ByOrderByPriceDesc();
    long countByCategory(String category);
    boolean existsByEmail(String email);

    // Custom JPQL query
    @Query("SELECT p FROM Product p WHERE p.price > :minPrice AND p.category = :category")
    List<Product> findExpensiveProductsByCategory(
        @Param("minPrice") Double minPrice,
        @Param("category") String category
    );

    // Native SQL query
    @Query(value = "SELECT * FROM products WHERE YEAR(created_at) = :year",
           nativeQuery = true)
    List<Product> findProductsByYear(@Param("year") int year);

    // Modifying query
    @Modifying
    @Transactional
    @Query("UPDATE Product p SET p.price = p.price * :factor WHERE p.category = :category")
    int updatePriceByCategory(@Param("factor") Double factor, @Param("category") String category);

    // Pagination and sorting
    Page<Product> findByCategory(String category, Pageable pageable);
}

// Using pagination in service
@Service
public class ProductService {
    public Page<Product> getProducts(int page, int size, String sortBy) {
        Pageable pageable = PageRequest.of(page, size, Sort.by(sortBy).ascending());
        return productRepository.findAll(pageable);
    }
}
```

### Fetch Types — Lazy vs Eager

```java
// EAGER: Loads related data immediately with the main entity
@ManyToOne(fetch = FetchType.EAGER)  // Default for @ManyToOne, @OneToOne
private Category category;

// LAZY: Loads related data only when accessed (on-demand)
@OneToMany(fetch = FetchType.LAZY)   // Default for @OneToMany, @ManyToMany
private List<Review> reviews;
```

**Best practice:** Always use `FetchType.LAZY` by default. Use EAGER only when you always need the related data. Eager can cause N+1 query problems.

### Transaction Management

```java
@Service
public class OrderService {

    @Transactional  // All methods in this block run in one transaction
    public Order createOrder(OrderRequest request) {
        Order order = orderRepository.save(new Order(request));
        inventoryService.deductStock(request.getProductId(), request.getQuantity());
        paymentService.processPayment(request.getPaymentDetails());
        // If any step fails → entire transaction rolls back
        return order;
    }

    // Read-only transaction (optimization — no dirty checking)
    @Transactional(readOnly = true)
    public List<Order> getAllOrders() {
        return orderRepository.findAll();
    }

    // Transaction with specific rollback rules
    @Transactional(rollbackFor = PaymentException.class,
                   noRollbackFor = InventoryWarningException.class)
    public void processOrder(Order order) { }
}
```

**Transaction Propagation Types:**

| Propagation | Behavior |
|-------------|----------|
| `REQUIRED` (default) | Use existing transaction or create new one |
| `REQUIRES_NEW` | Always create new transaction, suspend existing |
| `SUPPORTS` | Use existing transaction if available, else run without |
| `NOT_SUPPORTED` | Run without transaction, suspend existing |
| `MANDATORY` | Use existing transaction, throw exception if none |
| `NEVER` | Run without transaction, throw exception if one exists |

---

## 9. Spring Security

### What is Spring Security?

Spring Security is a powerful authentication and authorization framework for Java applications. It provides:
- Authentication (who are you?)
- Authorization (what can you do?)
- Protection against common attacks (CSRF, XSS, session fixation)

### Security Configuration (Spring Security 6+)

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .csrf(csrf -> csrf.disable())  // Disable CSRF for REST APIs (stateless)
            .sessionManagement(session ->
                session.sessionCreationPolicy(SessionCreationPolicy.STATELESS))
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/api/auth/**").permitAll()      // Public endpoints
                .requestMatchers("/api/admin/**").hasRole("ADMIN") // Admin only
                .requestMatchers(HttpMethod.GET, "/api/**").hasAnyRole("USER", "ADMIN")
                .anyRequest().authenticated()                      // All others need auth
            )
            .addFilterBefore(jwtAuthFilter, UsernamePasswordAuthenticationFilter.class);

        return http.build();
    }

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder(12); // Cost factor 12
    }

    @Bean
    public AuthenticationManager authenticationManager(
            AuthenticationConfiguration config) throws Exception {
        return config.getAuthenticationManager();
    }
}
```

### JWT Authentication Implementation

```java
// JWT Utility
@Component
public class JwtUtils {

    @Value("${jwt.secret}")
    private String jwtSecret;

    @Value("${jwt.expiration}")
    private int jwtExpiration;

    public String generateToken(UserDetails userDetails) {
        return Jwts.builder()
            .subject(userDetails.getUsername())
            .issuedAt(new Date())
            .expiration(new Date(System.currentTimeMillis() + jwtExpiration * 1000L))
            .signWith(getSigningKey())
            .compact();
    }

    public String extractUsername(String token) {
        return extractClaims(token).getSubject();
    }

    public boolean isTokenValid(String token, UserDetails userDetails) {
        return extractUsername(token).equals(userDetails.getUsername())
            && !isTokenExpired(token);
    }

    private boolean isTokenExpired(String token) {
        return extractClaims(token).getExpiration().before(new Date());
    }

    private Claims extractClaims(String token) {
        return Jwts.parser()
            .verifyWith(getSigningKey())
            .build()
            .parseSignedClaims(token)
            .getPayload();
    }

    private SecretKey getSigningKey() {
        return Keys.hmacShaKeyFor(jwtSecret.getBytes(StandardCharsets.UTF_8));
    }
}

// JWT Filter
@Component
public class JwtAuthFilter extends OncePerRequestFilter {

    @Autowired private JwtUtils jwtUtils;
    @Autowired private UserDetailsService userDetailsService;

    @Override
    protected void doFilterInternal(HttpServletRequest request,
                                    HttpServletResponse response,
                                    FilterChain filterChain) throws ServletException, IOException {

        String authHeader = request.getHeader("Authorization");

        if (authHeader == null || !authHeader.startsWith("Bearer ")) {
            filterChain.doFilter(request, response);
            return;
        }

        String token = authHeader.substring(7);
        String username = jwtUtils.extractUsername(token);

        if (username != null && SecurityContextHolder.getContext().getAuthentication() == null) {
            UserDetails userDetails = userDetailsService.loadUserByUsername(username);

            if (jwtUtils.isTokenValid(token, userDetails)) {
                UsernamePasswordAuthenticationToken authToken =
                    new UsernamePasswordAuthenticationToken(
                        userDetails, null, userDetails.getAuthorities());
                authToken.setDetails(new WebAuthenticationDetailsSource().buildDetails(request));
                SecurityContextHolder.getContext().setAuthentication(authToken);
            }
        }

        filterChain.doFilter(request, response);
    }
}
```

### Method-Level Security

```java
@Configuration
@EnableMethodSecurity  // Enable method-level security
public class MethodSecurityConfig { }

@RestController
public class AdminController {

    @GetMapping("/users")
    @PreAuthorize("hasRole('ADMIN')")  // Check before method executes
    public List<User> getAllUsers() { }

    @DeleteMapping("/users/{id}")
    @PreAuthorize("hasRole('ADMIN') or #id == authentication.principal.id")
    public void deleteUser(@PathVariable Long id) { }

    @GetMapping("/my-orders")
    @PostAuthorize("returnObject.username == authentication.name")  // Check after return
    public Order getMyOrder() { }
}
```

---

## 10. Exception Handling

### Global Exception Handler with @ControllerAdvice

```java
// Custom exceptions
public class ResourceNotFoundException extends RuntimeException {
    public ResourceNotFoundException(String message) {
        super(message);
    }
}

public class DuplicateResourceException extends RuntimeException {
    public DuplicateResourceException(String message) {
        super(message);
    }
}

// Error response structure
public class ErrorResponse {
    private int status;
    private String message;
    private LocalDateTime timestamp;
    private Map<String, String> validationErrors;

    // Constructor, getters, setters
}

// Global exception handler
@RestControllerAdvice  // Applies to all @RestController classes
public class GlobalExceptionHandler {

    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleResourceNotFound(
            ResourceNotFoundException ex, HttpServletRequest request) {

        ErrorResponse error = new ErrorResponse(
            HttpStatus.NOT_FOUND.value(),
            ex.getMessage(),
            LocalDateTime.now()
        );
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(error);
    }

    @ExceptionHandler(DuplicateResourceException.class)
    public ResponseEntity<ErrorResponse> handleDuplicate(DuplicateResourceException ex) {
        ErrorResponse error = new ErrorResponse(HttpStatus.CONFLICT.value(), ex.getMessage(), LocalDateTime.now());
        return ResponseEntity.status(HttpStatus.CONFLICT).body(error);
    }

    // Handle @Valid validation failures
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ErrorResponse> handleValidationErrors(MethodArgumentNotValidException ex) {

        Map<String, String> errors = new HashMap<>();
        ex.getBindingResult().getFieldErrors().forEach(error ->
            errors.put(error.getField(), error.getDefaultMessage())
        );

        ErrorResponse response = new ErrorResponse(
            HttpStatus.BAD_REQUEST.value(),
            "Validation failed",
            LocalDateTime.now()
        );
        response.setValidationErrors(errors);
        return ResponseEntity.badRequest().body(response);
    }

    @ExceptionHandler(AccessDeniedException.class)
    public ResponseEntity<ErrorResponse> handleAccessDenied(AccessDeniedException ex) {
        ErrorResponse error = new ErrorResponse(403, "Access denied", LocalDateTime.now());
        return ResponseEntity.status(HttpStatus.FORBIDDEN).body(error);
    }

    // Catch all unhandled exceptions
    @ExceptionHandler(Exception.class)
    public ResponseEntity<ErrorResponse> handleAllExceptions(Exception ex) {
        ErrorResponse error = new ErrorResponse(500, "An unexpected error occurred", LocalDateTime.now());
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body(error);
    }
}
```

---

## 11. Spring Boot Actuator

### What is Spring Boot Actuator?

Actuator adds production-ready features to your application — health checks, metrics, and monitoring — without you writing any code.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

### Built-in Endpoints

| Endpoint | Description |
|----------|-------------|
| `/actuator/health` | Application health status |
| `/actuator/info` | Application information |
| `/actuator/metrics` | Application metrics |
| `/actuator/env` | Environment properties |
| `/actuator/beans` | All Spring beans |
| `/actuator/mappings` | All request mappings |
| `/actuator/loggers` | Logger configuration |
| `/actuator/threaddump` | Thread dump |
| `/actuator/heapdump` | Heap dump |
| `/actuator/shutdown` | Shutdown the app (disabled by default) |

```yaml
# application.yml — actuator configuration
management:
  endpoints:
    web:
      exposure:
        include: health, info, metrics, loggers  # Expose specific endpoints
        # include: "*"   — Expose all (not for production)
  endpoint:
    health:
      show-details: always  # Show full health details
  info:
    env:
      enabled: true

# Custom info
info:
  app:
    name: My Spring Boot App
    version: 1.0.0
    description: Employee Management System
```

### Custom Health Indicator

```java
@Component
public class DatabaseHealthIndicator implements HealthIndicator {

    @Autowired
    private DataSource dataSource;

    @Override
    public Health health() {
        try (Connection conn = dataSource.getConnection()) {
            if (conn.isValid(1)) {
                return Health.up()
                    .withDetail("database", "PostgreSQL")
                    .withDetail("status", "Connected")
                    .build();
            }
        } catch (Exception e) {
            return Health.down()
                .withDetail("error", e.getMessage())
                .build();
        }
        return Health.down().build();
    }
}
```

---

## 12. Logging

### Logging Framework

Spring Boot uses **SLF4J** (Simple Logging Facade for Java) as the API and **Logback** as the default implementation.

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Service;

@Service
public class OrderService {

    private static final Logger log = LoggerFactory.getLogger(OrderService.class);

    // Or use Lombok's @Slf4j annotation (eliminates boilerplate)
    // @Slf4j on class — provides `log` automatically

    public Order createOrder(OrderRequest request) {
        log.debug("Creating order for user: {}", request.getUserId());  // Debug details
        log.info("Order request received for product: {}", request.getProductId());

        try {
            Order order = processOrder(request);
            log.info("Order created successfully with ID: {}", order.getId());
            return order;
        } catch (PaymentException e) {
            log.error("Payment failed for order request: {}, error: {}",
                request.getProductId(), e.getMessage(), e);
            throw e;
        }
    }
}
```

### Log Levels (Lowest to Highest)

```
TRACE → DEBUG → INFO → WARN → ERROR
```

- `TRACE` — Most detailed (method entry/exit, loop iterations)
- `DEBUG` — Debugging details (variable values, flow)
- `INFO` — Important events (application started, order created)
- `WARN` — Potential issues (deprecated API used, retry attempt)
- `ERROR` — Errors that affect functionality

### Logging Configuration

```yaml
# application.yml
logging:
  level:
    root: WARN                                      # Default level
    com.example.myapp: DEBUG                        # Your package — more verbose
    org.springframework.web: INFO
    org.hibernate.SQL: DEBUG                        # Show SQL queries
    org.hibernate.type.descriptor.sql: TRACE        # Show SQL bind parameters
  file:
    name: logs/application.log                      # Write to file
  pattern:
    console: "%d{yyyy-MM-dd HH:mm:ss} [%thread] %-5level %logger{36} - %msg%n"
    file: "%d{yyyy-MM-dd HH:mm:ss} [%thread] %-5level %logger{36} - %msg%n"
```

---

## 13. Profiles & Configuration

### Spring Profiles

Profiles allow you to define different configurations for different environments (dev, test, prod).

```yaml
# application.yml — default configuration
spring:
  application:
    name: my-app

---
# application-dev.yml — development config
spring:
  config:
    activate:
      on-profile: dev
  datasource:
    url: jdbc:h2:mem:testdb
    driver-class-name: org.h2.Driver
  jpa:
    show-sql: true
logging:
  level:
    com.example: DEBUG

---
# application-prod.yml — production config
spring:
  config:
    activate:
      on-profile: prod
  datasource:
    url: jdbc:postgresql://prod-db:5432/mydb
    username: ${DB_USERNAME}    # From environment variable
    password: ${DB_PASSWORD}
  jpa:
    show-sql: false
logging:
  level:
    com.example: WARN
```

```bash
# Activate profile when running
java -jar myapp.jar --spring.profiles.active=prod

# Or set environment variable
export SPRING_PROFILES_ACTIVE=prod
```

### @Profile on Beans

```java
// Only create this bean when 'dev' profile is active
@Bean
@Profile("dev")
public DataSource h2DataSource() {
    return new EmbeddedDatabaseBuilder()
        .setType(EmbeddedDatabaseType.H2)
        .build();
}

// Only for production
@Service
@Profile("prod")
public class ProductionEmailService implements EmailService {
    // Uses real SMTP server
}

// For all profiles except prod
@Service
@Profile("!prod")
public class MockEmailService implements EmailService {
    // Just prints to console
}
```

---

## 14. Caching

### Spring Cache Abstraction

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-cache</artifactId>
</dependency>
<!-- For Redis cache -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

```java
@SpringBootApplication
@EnableCaching  // Enable caching
public class MyApplication { }

@Service
public class ProductService {

    // @Cacheable: Caches the return value; skips method if result already cached
    @Cacheable(value = "products", key = "#id")
    public Product getProductById(Long id) {
        log.info("Fetching from database...");  // Only logs if cache miss
        return productRepository.findById(id).orElseThrow();
    }

    // @CacheEvict: Removes entry from cache
    @CacheEvict(value = "products", key = "#id")
    public void deleteProduct(Long id) {
        productRepository.deleteById(id);
    }

    // @CachePut: Updates cache with new value (always executes method)
    @CachePut(value = "products", key = "#product.id")
    public Product updateProduct(Product product) {
        return productRepository.save(product);
    }

    // @CacheEvict all entries in cache
    @CacheEvict(value = "products", allEntries = true)
    public void clearAllProductCache() { }
}
```

### Redis Cache Configuration

```yaml
spring:
  data:
    redis:
      host: localhost
      port: 6379
  cache:
    type: redis
    redis:
      time-to-live: 600000  # 10 minutes in milliseconds
```

```java
@Configuration
public class CacheConfig {

    @Bean
    public RedisCacheConfiguration cacheConfiguration() {
        return RedisCacheConfiguration.defaultCacheConfig()
            .entryTtl(Duration.ofMinutes(10))
            .disableCachingNullValues()
            .serializeValuesWith(
                RedisSerializationContext.SerializationPair
                    .fromSerializer(new GenericJackson2JsonRedisSerializer()));
    }
}
```

---

## 15. Spring Boot Testing

### Testing Layers

```
Unit Tests        → Test individual class (mock dependencies)
Integration Tests → Test multiple layers together
Slice Tests       → Test specific layer (only web, only JPA)
End-to-End Tests  → Test full application with real HTTP calls
```

### Unit Testing with Mockito

```java
@ExtendWith(MockitoExtension.class)
class UserServiceTest {

    @Mock
    private UserRepository userRepository;  // Mock — doesn't hit DB

    @Mock
    private EmailService emailService;

    @InjectMocks
    private UserService userService;  // Real service with mocked dependencies

    @Test
    void shouldReturnUserWhenFound() {
        // Arrange
        User user = new User(1L, "Alice", "alice@test.com");
        when(userRepository.findById(1L)).thenReturn(Optional.of(user));

        // Act
        User result = userService.getUserById(1L);

        // Assert
        assertNotNull(result);
        assertEquals("Alice", result.getName());
        verify(userRepository, times(1)).findById(1L);
    }

    @Test
    void shouldThrowExceptionWhenUserNotFound() {
        when(userRepository.findById(99L)).thenReturn(Optional.empty());

        assertThrows(ResourceNotFoundException.class,
            () -> userService.getUserById(99L));
    }
}
```

### Web Layer Testing with @WebMvcTest

```java
@WebMvcTest(UserController.class)  // Only loads web layer
class UserControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @MockBean
    private UserService userService;  // Mock the service layer

    @Autowired
    private ObjectMapper objectMapper;

    @Test
    void shouldReturnUserWhenGetById() throws Exception {
        User user = new User(1L, "Alice", "alice@test.com");
        when(userService.getUserById(1L)).thenReturn(user);

        mockMvc.perform(get("/api/users/1")
                .contentType(MediaType.APPLICATION_JSON))
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.name").value("Alice"))
            .andExpect(jsonPath("$.email").value("alice@test.com"));
    }

    @Test
    void shouldCreateUserAndReturn201() throws Exception {
        CreateUserRequest request = new CreateUserRequest("Bob", "bob@test.com");
        User created = new User(2L, "Bob", "bob@test.com");
        when(userService.create(any())).thenReturn(created);

        mockMvc.perform(post("/api/users")
                .contentType(MediaType.APPLICATION_JSON)
                .content(objectMapper.writeValueAsString(request)))
            .andExpect(status().isCreated())
            .andExpect(jsonPath("$.id").value(2))
            .andExpect(jsonPath("$.name").value("Bob"));
    }
}
```

### Repository Testing with @DataJpaTest

```java
@DataJpaTest  // Only loads JPA layer — uses in-memory H2 database
class ProductRepositoryTest {

    @Autowired
    private TestEntityManager entityManager;

    @Autowired
    private ProductRepository productRepository;

    @Test
    void shouldFindProductsByCategory() {
        // Arrange — save test data
        Product p1 = new Product("Phone", "Electronics", 50000.0);
        Product p2 = new Product("Laptop", "Electronics", 80000.0);
        Product p3 = new Product("Shirt", "Clothing", 999.0);
        entityManager.persist(p1);
        entityManager.persist(p2);
        entityManager.persist(p3);
        entityManager.flush();

        // Act
        List<Product> electronics = productRepository.findByCategory("Electronics");

        // Assert
        assertEquals(2, electronics.size());
        assertTrue(electronics.stream().anyMatch(p -> p.getName().equals("Phone")));
    }
}
```

### Full Integration Testing with @SpringBootTest

```java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
class UserIntegrationTest {

    @Autowired
    private TestRestTemplate restTemplate;

    @Test
    void shouldCreateAndRetrieveUser() {
        // Create user
        CreateUserRequest request = new CreateUserRequest("Alice", "alice@test.com");
        ResponseEntity<User> createResponse = restTemplate.postForEntity(
            "/api/users", request, User.class);

        assertEquals(HttpStatus.CREATED, createResponse.getStatusCode());
        Long userId = createResponse.getBody().getId();

        // Get user
        ResponseEntity<User> getResponse = restTemplate.getForEntity(
            "/api/users/" + userId, User.class);

        assertEquals(HttpStatus.OK, getResponse.getStatusCode());
        assertEquals("Alice", getResponse.getBody().getName());
    }
}
```

---

## 16. Microservices with Spring Boot

### What are Microservices?

Microservices is an architectural style where an application is built as a collection of small, independently deployable services. Each service:
- Owns its own data (separate database)
- Communicates over network (REST/gRPC/messaging)
- Can be deployed independently
- Is built around a specific business capability

### Microservices Architecture

```
                    ┌─────────────────────────────────┐
                    │       API Gateway                │
                    │  (Spring Cloud Gateway)          │
                    └──┬──────┬──────┬────────────────┘
                       │      │      │
              ┌────────▼──┐ ┌─▼──────▼──┐ ┌──────────────┐
              │  User     │ │  Order    │ │  Product     │
              │  Service  │ │  Service  │ │  Service     │
              │  :8081    │ │  :8082    │ │  :8083       │
              └─────┬─────┘ └─────┬─────┘ └──────┬───────┘
                    │             │               │
              ┌─────▼──┐   ┌──────▼──┐     ┌─────▼──┐
              │User DB │   │Order DB │     │Prod DB │
              └────────┘   └─────────┘     └────────┘

        Service Discovery: Eureka Server
        Config Server: Spring Cloud Config
        Messaging: Kafka / RabbitMQ
```

### Inter-Service Communication

#### Synchronous — RestTemplate / WebClient / OpenFeign

```java
// Using OpenFeign (recommended — declarative)
@FeignClient(name = "product-service", url = "${product.service.url}")
public interface ProductServiceClient {

    @GetMapping("/api/products/{id}")
    Product getProductById(@PathVariable Long id);

    @PostMapping("/api/products/check-stock")
    StockResponse checkStock(@RequestBody StockRequest request);
}

// Usage in Order Service
@Service
public class OrderService {

    @Autowired
    private ProductServiceClient productServiceClient;

    public Order createOrder(OrderRequest request) {
        // Call Product Service
        Product product = productServiceClient.getProductById(request.getProductId());
        // Process order...
    }
}
```

#### Asynchronous — Kafka

```java
// Producer (Order Service publishes event)
@Service
public class OrderEventPublisher {

    @Autowired
    private KafkaTemplate<String, OrderCreatedEvent> kafkaTemplate;

    public void publishOrderCreated(Order order) {
        OrderCreatedEvent event = new OrderCreatedEvent(order.getId(), order.getProductId());
        kafkaTemplate.send("order-events", event);
    }
}

// Consumer (Inventory Service listens)
@Service
public class InventoryEventListener {

    @KafkaListener(topics = "order-events", groupId = "inventory-group")
    public void handleOrderCreated(OrderCreatedEvent event) {
        inventoryService.deductStock(event.getProductId(), event.getQuantity());
    }
}
```

---

## 17. Spring Cloud

### Service Discovery — Eureka

```yaml
# Eureka Server application.yml
spring:
  application:
    name: eureka-server

server:
  port: 8761

eureka:
  client:
    register-with-eureka: false
    fetch-registry: false
```

```java
// Eureka Server
@SpringBootApplication
@EnableEurekaServer
public class EurekaServerApplication { }

// Eureka Client (microservice)
@SpringBootApplication
@EnableEurekaClient
public class UserServiceApplication { }
```

```yaml
# User Service application.yml
spring:
  application:
    name: user-service
eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka
```

### API Gateway — Spring Cloud Gateway

```yaml
# application.yml for API Gateway
spring:
  cloud:
    gateway:
      routes:
        - id: user-service
          uri: lb://user-service      # lb:// = load balanced via Eureka
          predicates:
            - Path=/api/users/**
          filters:
            - StripPrefix=0
            - AddRequestHeader=X-Gateway-Source, api-gateway

        - id: product-service
          uri: lb://product-service
          predicates:
            - Path=/api/products/**

        - id: order-service
          uri: lb://order-service
          predicates:
            - Path=/api/orders/**
```

### Circuit Breaker — Resilience4j

Prevents cascading failures. If a service is down, the circuit opens and a fallback is triggered.

```java
@Service
public class OrderService {

    @Autowired
    private ProductServiceClient productServiceClient;

    @CircuitBreaker(name = "productService", fallbackMethod = "getDefaultProduct")
    @Retry(name = "productService")
    @TimeLimiter(name = "productService")
    public Product getProduct(Long productId) {
        return productServiceClient.getProductById(productId);
    }

    // Fallback method — called when circuit is open or request fails
    public Product getDefaultProduct(Long productId, Throwable ex) {
        log.warn("Product service unavailable, returning default: {}", ex.getMessage());
        return new Product(productId, "Unknown Product", 0.0);
    }
}
```

```yaml
# Resilience4j configuration
resilience4j:
  circuitbreaker:
    instances:
      productService:
        failure-rate-threshold: 50          # Open circuit if 50% calls fail
        wait-duration-in-open-state: 10s    # Stay open for 10 seconds
        sliding-window-size: 10             # Based on last 10 calls
  retry:
    instances:
      productService:
        max-attempts: 3
        wait-duration: 500ms
```

---

## 18. Messaging — Kafka & RabbitMQ

### Apache Kafka with Spring Boot

```xml
<dependency>
    <groupId>org.springframework.kafka</groupId>
    <artifactId>spring-kafka</artifactId>
</dependency>
```

```yaml
spring:
  kafka:
    bootstrap-servers: localhost:9092
    producer:
      key-serializer: org.apache.kafka.common.serialization.StringSerializer
      value-serializer: org.springframework.kafka.support.serializer.JsonSerializer
    consumer:
      group-id: my-group
      auto-offset-reset: earliest
      key-deserializer: org.apache.kafka.common.serialization.StringDeserializer
      value-deserializer: org.springframework.kafka.support.serializer.JsonDeserializer
      properties:
        spring.json.trusted.packages: "*"
```

```java
// Producer
@Service
public class NotificationProducer {

    @Autowired
    private KafkaTemplate<String, NotificationEvent> kafkaTemplate;

    public void sendNotification(NotificationEvent event) {
        kafkaTemplate.send("notifications", event.getUserId().toString(), event)
            .whenComplete((result, ex) -> {
                if (ex != null) {
                    log.error("Failed to send notification: {}", ex.getMessage());
                } else {
                    log.info("Notification sent to partition: {}",
                        result.getRecordMetadata().partition());
                }
            });
    }
}

// Consumer
@Service
public class NotificationConsumer {

    @KafkaListener(topics = "notifications", groupId = "notification-group")
    public void consumeNotification(
            @Payload NotificationEvent event,
            @Header(KafkaHeaders.RECEIVED_TOPIC) String topic,
            @Header(KafkaHeaders.RECEIVED_PARTITION) int partition) {

        log.info("Received event from topic: {}, partition: {}", topic, partition);
        emailService.sendEmail(event.getUserEmail(), event.getMessage());
    }
}
```

---

## 19. Spring Boot with Docker

### Dockerfile for Spring Boot

```dockerfile
# Multi-stage build
FROM eclipse-temurin:21-jdk AS builder
WORKDIR /app
COPY pom.xml .
COPY src ./src
RUN ./mvnw clean package -DskipTests

FROM eclipse-temurin:21-jre
WORKDIR /app
COPY --from=builder /app/target/*.jar app.jar

# Non-root user for security
RUN addgroup --system spring && adduser --system spring --ingroup spring
USER spring:spring

EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```

### Docker Compose

```yaml
# docker-compose.yml
version: '3.8'

services:
  app:
    build: .
    ports:
      - "8080:8080"
    environment:
      - SPRING_PROFILES_ACTIVE=prod
      - DB_HOST=postgres
      - DB_PORT=5432
      - DB_NAME=mydb
      - DB_USERNAME=admin
      - DB_PASSWORD=secret
    depends_on:
      postgres:
        condition: service_healthy
      redis:
        condition: service_started

  postgres:
    image: postgres:15
    environment:
      POSTGRES_DB: mydb
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: secret
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U admin -d mydb"]
      interval: 10s
      timeout: 5s
      retries: 5

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"

volumes:
  postgres_data:
```

---

## 20. Performance & Best Practices

### Connection Pool — HikariCP

Spring Boot uses HikariCP as the default connection pool.

```yaml
spring:
  datasource:
    hikari:
      maximum-pool-size: 10         # Max DB connections
      minimum-idle: 5               # Min idle connections
      idle-timeout: 300000          # Remove idle connections after 5 min
      connection-timeout: 20000     # Fail if no connection in 20s
      max-lifetime: 1200000         # Max connection lifetime 20 min
```

### Async Processing

```java
@SpringBootApplication
@EnableAsync  // Enable async processing
public class MyApplication { }

@Service
public class EmailService {

    // Runs in separate thread — caller doesn't wait
    @Async
    public CompletableFuture<Void> sendEmailAsync(String to, String subject) {
        log.info("Sending email to: {} on thread: {}", to, Thread.currentThread().getName());
        // Simulate slow email sending
        Thread.sleep(3000);
        sendEmail(to, subject);
        return CompletableFuture.completedFuture(null);
    }
}

// Custom thread pool for async tasks
@Configuration
public class AsyncConfig {

    @Bean
    public TaskExecutor taskExecutor() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        executor.setCorePoolSize(5);
        executor.setMaxPoolSize(10);
        executor.setQueueCapacity(100);
        executor.setThreadNamePrefix("async-");
        executor.initialize();
        return executor;
    }
}
```

### Scheduling Tasks

```java
@SpringBootApplication
@EnableScheduling
public class MyApplication { }

@Component
public class ScheduledTasks {

    // Run every 5 seconds
    @Scheduled(fixedDelay = 5000)
    public void runEvery5Seconds() { }

    // Run every minute (cron expression)
    @Scheduled(cron = "0 * * * * ?")
    public void runEveryMinute() { }

    // Run daily at 2:30 AM
    @Scheduled(cron = "0 30 2 * * ?")
    public void runDailyCleanup() {
        log.info("Running daily cleanup...");
        cleanupService.deleteOldRecords();
    }
}
```

### Best Practices Summary

**1. Use Constructor Injection**
Always prefer constructor injection over field injection for mandatory dependencies.

**2. Use DTOs (Data Transfer Objects)**
Never expose entity classes directly in REST responses. Use DTOs to control what data is exposed.

**3. Use Pagination**
Never return all records. Always use `Pageable` for list endpoints.

**4. Externalize Configuration**
Never hardcode URLs, credentials, or environment-specific values. Use `application.yml` and environment variables.

**5. Use Proper HTTP Status Codes**
Return appropriate HTTP status codes — don't always return 200.

**6. Handle Exceptions Globally**
Use `@RestControllerAdvice` for centralized exception handling. Never let stack traces reach the client.

**7. Use Lazy Fetch by Default**
Avoid EAGER fetching in JPA relationships to prevent N+1 problems.

**8. Validate Input**
Always validate request input using `@Valid` and Bean Validation annotations.

**9. Use ReadOnly Transactions**
Mark read-only service methods with `@Transactional(readOnly = true)` for performance.

**10. Log Properly**
Use appropriate log levels. Never log sensitive information (passwords, credit card numbers).

---

## Quick Reference Cheatsheet

### Annotation Summary
```
@SpringBootApplication    → Main class (Config + ComponentScan + AutoConfig)
@RestController           → REST API controller (returns JSON)
@Service                  → Business logic layer
@Repository               → Data access layer
@Component                → Generic bean
@Configuration            → Bean definition class
@Bean                     → Method-level bean definition
@Autowired                → Dependency injection
@Value                    → Inject property value
@ConfigurationProperties  → Bind group of properties to a class
@Entity                   → JPA entity (maps to DB table)
@Id                       → Primary key
@Transactional            → Transaction management
@Valid                    → Trigger bean validation
@ExceptionHandler         → Handle specific exception
@RestControllerAdvice     → Global exception handler
@Cacheable                → Cache method result
@Scheduled                → Schedule a method
@Async                    → Run method asynchronously
@Profile                  → Conditional bean based on profile
@PreAuthorize             → Method-level security
```

### Request Mapping Shortcuts
```
@GetMapping     →  @RequestMapping(method = RequestMethod.GET)
@PostMapping    →  @RequestMapping(method = RequestMethod.POST)
@PutMapping     →  @RequestMapping(method = RequestMethod.PUT)
@PatchMapping   →  @RequestMapping(method = RequestMethod.PATCH)
@DeleteMapping  →  @RequestMapping(method = RequestMethod.DELETE)
```

### JPA DDL Auto Options
```
validate   → Validate schema, make no changes
update     → Update schema if needed (dev)
create     → Drop and create schema on startup (testing)
create-drop→ Create on startup, drop on shutdown (testing)
none       → Do nothing (production — manage with Flyway/Liquibase)
```

---

*This guide covers Spring Boot from core fundamentals through advanced microservices patterns. Build a complete CRUD REST API with security and testing to solidify every concept covered here.*
