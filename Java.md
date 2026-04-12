# Java Interview Topics: Basic to Advanced
### Questions & Answers with Detailed Explanations

---

## TABLE OF CONTENTS

1. [Java Basics](#1-java-basics)
2. [Object-Oriented Programming (OOP)](#2-object-oriented-programming-oop)
3. [Java Data Types & Variables](#3-java-data-types--variables)
4. [Control Flow & Loops](#4-control-flow--loops)
5. [Arrays & Strings](#5-arrays--strings)
6. [Collections Framework](#6-collections-framework)
7. [Exception Handling](#7-exception-handling)
8. [Java Memory Management](#8-java-memory-management)
9. [Multithreading & Concurrency](#9-multithreading--concurrency)
10. [Java 8+ Features](#10-java-8-features)
11. [Generics](#11-generics)
12. [File I/O & Serialization](#12-file-io--serialization)
13. [Design Patterns in Java](#13-design-patterns-in-java)
14. [Java Internals & JVM](#14-java-internals--jvm)
15. [Quick Reference: Top 60 Interview Q&A](#15-quick-reference-top-60-interview-qa)

---

## 1. JAVA BASICS

---

### Q1. What is Java? What are its key features?

**Answer:**

Java is a **high-level, class-based, object-oriented programming language** developed by Sun Microsystems (now Oracle) in 1995. It follows the principle of **"Write Once, Run Anywhere" (WORA)** — compiled Java code runs on any platform with a JVM (Java Virtual Machine).

**Key Features:**

| Feature | Description |
|---|---|
| Platform Independent | Compiled to bytecode, runs on any JVM |
| Object-Oriented | Everything is an object (except primitives) |
| Strongly Typed | All variables must have a declared type |
| Automatic Memory Management | Garbage Collector handles memory |
| Multithreaded | Built-in support for concurrent execution |
| Secure | No direct memory access; bytecode verification |
| Robust | Strong type checking, exception handling |
| High Performance | JIT compiler optimizes at runtime |
| Distributed | Built-in networking support (java.net) |
| Dynamic | Supports runtime polymorphism, reflection |

---

### Q2. What is the difference between JDK, JRE, and JVM?

**Answer:**

```
┌──────────────────────────────────────┐
│               JDK                    │
│  ┌────────────────────────────────┐  │
│  │             JRE                │  │
│  │  ┌──────────────────────────┐  │  │
│  │  │          JVM             │  │  │
│  │  │  (Bytecode execution)    │  │  │
│  │  └──────────────────────────┘  │  │
│  │  + Java Class Libraries        │  │
│  └────────────────────────────────┘  │
│  + Compiler (javac), Debugger, Tools │
└──────────────────────────────────────┘
```

**JVM (Java Virtual Machine):**
- Abstract computing machine that executes Java bytecode
- Provides platform independence
- Handles memory management, garbage collection, security
- NOT platform independent itself — different JVM for each OS

**JRE (Java Runtime Environment):**
- JVM + Java Class Libraries (rt.jar) + supporting files
- Everything needed to **run** Java programs
- Does NOT include compiler or development tools

**JDK (Java Development Kit):**
- JRE + Compiler (javac) + Debugger + JavaDoc + other tools
- Everything needed to **develop** Java programs
- Required for development; JRE sufficient for end-users

---

### Q3. How does Java code get executed?

**Answer:**

```
Source Code (.java)
        ↓  javac (compiler)
   Bytecode (.class)
        ↓  JVM
   Machine Code (execution)
```

1. **Write:** Developer writes `.java` source file
2. **Compile:** `javac Hello.java` → generates `Hello.class` (bytecode)
3. **Load:** ClassLoader loads `.class` file into JVM memory
4. **Verify:** Bytecode Verifier checks for security violations
5. **Execute:** JVM interprets OR JIT-compiles bytecode to native machine code
6. **Run:** OS executes the native code

```java
// Hello.java
public class Hello {
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}
```

```bash
javac Hello.java   # compile
java Hello         # run
```

---

### Q4. What is the difference between `==` and `.equals()` in Java?

**Answer:**

- `==` compares **references** (memory addresses) for objects, **values** for primitives
- `.equals()` compares **content/value** of objects

```java
// Primitives — == compares values
int a = 5, b = 5;
System.out.println(a == b); // true

// Strings — == compares references
String s1 = new String("hello");
String s2 = new String("hello");
System.out.println(s1 == s2);       // false (different objects in heap)
System.out.println(s1.equals(s2));  // true  (same content)

// String pool — == may return true
String s3 = "hello";
String s4 = "hello";
System.out.println(s3 == s4);      // true (both point to same pool object)
System.out.println(s3.equals(s4)); // true

// Integer cache (-128 to 127)
Integer x = 127;
Integer y = 127;
System.out.println(x == y);       // true (cached)

Integer p = 128;
Integer q = 128;
System.out.println(p == q);       // false (not cached — different objects)
System.out.println(p.equals(q));  // true
```

**Rule:** Always use `.equals()` for object comparison. Use `==` only for primitives or intentional reference comparison.

---

### Q5. What are access modifiers in Java?

**Answer:**

| Modifier | Same Class | Same Package | Subclass | Everywhere |
|---|---|---|---|---|
| `private` | ✅ | ❌ | ❌ | ❌ |
| `default` (no keyword) | ✅ | ✅ | ❌ | ❌ |
| `protected` | ✅ | ✅ | ✅ | ❌ |
| `public` | ✅ | ✅ | ✅ | ✅ |

```java
public class BankAccount {
    private double balance;         // only within this class
    String accountType;             // default — same package
    protected String ownerName;     // subclasses + same package
    public String bankName;         // everywhere

    private void deductFee() { }    // private method
    public double getBalance() {    // public getter
        return balance;
    }
}
```

---

### Q6. What is the difference between `static` and non-static members?

**Answer:**

```java
public class Counter {
    // static — belongs to the class, shared by all instances
    private static int count = 0;

    // non-static (instance) — belongs to each object
    private int instanceId;
    private String name;

    public Counter(String name) {
        count++;                   // shared across all instances
        this.instanceId = count;   // unique per instance
        this.name = name;
    }

    // static method — can only access static members
    public static int getCount() {
        return count;
    }

    // instance method — can access both static and instance members
    public String getInfo() {
        return name + " (id=" + instanceId + ", total=" + count + ")";
    }

    public static void main(String[] args) {
        Counter c1 = new Counter("Alpha");
        Counter c2 = new Counter("Beta");
        Counter c3 = new Counter("Gamma");

        System.out.println(Counter.getCount()); // 3 — call via class name
        System.out.println(c1.getInfo());       // Alpha (id=1, total=3)
        System.out.println(c2.getInfo());       // Beta  (id=2, total=3)
    }
}
```

**Key rules:**
- Static methods cannot use `this` or access instance variables
- Static members are initialized when the class is loaded
- Instance members are initialized when an object is created
- Call static members via class name: `Counter.getCount()`

---

### Q7. What is the `final` keyword in Java?

**Answer:**

```java
// 1. final variable — value cannot be changed (constant)
final int MAX_SIZE = 100;
MAX_SIZE = 200; // Error: cannot assign to final variable

final double PI = 3.14159;

// 2. final reference — reference cannot change, but object contents can
final StringBuilder sb = new StringBuilder("hello");
sb.append(" world"); // OK — modifying content
sb = new StringBuilder("new"); // Error — cannot reassign reference

// 3. final method — cannot be overridden in subclass
class Parent {
    public final void display() {
        System.out.println("Parent display");
    }
}
class Child extends Parent {
    // public void display() {} // Error: cannot override final method
}

// 4. final class — cannot be subclassed (extended)
final class Singleton {
    private static final Singleton INSTANCE = new Singleton();
    private Singleton() {}
    public static Singleton getInstance() { return INSTANCE; }
}
// class SubSingleton extends Singleton {} // Error: cannot extend final class

// String is a final class in Java
// class MyString extends String {} // Error
```

---

## 2. OBJECT-ORIENTED PROGRAMMING (OOP)

---

### Q8. What are the four pillars of OOP in Java?

**Answer:**

**1. Encapsulation — Bundling data + methods; hiding internal state**

```java
public class Employee {
    private String name;    // hidden from outside
    private double salary;  // hidden from outside

    // Controlled access through getters/setters
    public String getName() { return name; }

    public void setSalary(double salary) {
        if (salary < 0) throw new IllegalArgumentException("Salary cannot be negative");
        this.salary = salary;
    }
    public double getSalary() { return salary; }
}
```

**2. Inheritance — A class acquires properties of another class**

```java
class Animal {
    String name;
    void eat() { System.out.println(name + " eats"); }
}

class Dog extends Animal {
    void bark() { System.out.println(name + " barks"); }
}

class GoldenRetriever extends Dog {
    void fetch() { System.out.println(name + " fetches"); }
}

// GoldenRetriever HAS: eat(), bark(), fetch(), name
```

**3. Polymorphism — Same interface, different behavior**

```java
// Method Overloading (Compile-time / Static polymorphism)
class Calculator {
    int add(int a, int b) { return a + b; }
    double add(double a, double b) { return a + b; }
    int add(int a, int b, int c) { return a + b + c; }
}

// Method Overriding (Runtime / Dynamic polymorphism)
class Shape {
    double area() { return 0; }
}
class Circle extends Shape {
    double radius;
    Circle(double r) { radius = r; }
    @Override double area() { return Math.PI * radius * radius; }
}
class Square extends Shape {
    double side;
    Square(double s) { side = s; }
    @Override double area() { return side * side; }
}

Shape s1 = new Circle(5);   // runtime: calls Circle.area()
Shape s2 = new Square(4);   // runtime: calls Square.area()
```

**4. Abstraction — Hiding implementation details, showing only essential features**

```java
// Abstract class
abstract class Vehicle {
    abstract void start();  // must implement
    void stop() { System.out.println("Vehicle stopped"); } // shared
}

// Interface — 100% abstraction (before Java 8)
interface Flyable {
    void fly();
    void land();
}

class Airplane extends Vehicle implements Flyable {
    public void start() { System.out.println("Airplane engine starts"); }
    public void fly() { System.out.println("Airplane flying"); }
    public void land() { System.out.println("Airplane landing"); }
}
```

---

### Q9. What is the difference between abstract class and interface?

**Answer:**

| Feature | Abstract Class | Interface |
|---|---|---|
| Keyword | `abstract class` | `interface` |
| Methods | Abstract + concrete | Abstract + default + static (Java 8+) |
| Variables | Any type (instance variables) | Only `public static final` constants |
| Constructor | Yes (cannot instantiate) | No |
| Extends/Implements | `extends` (single) | `implements` (multiple) |
| Access Modifiers | Any | `public` (implicit) |
| When to use | Shared code + "IS-A" relationship | Contract definition + multiple inheritance |
| Speed | Slightly faster | Slightly slower (indirection) |

```java
// Abstract class
abstract class DatabaseConnection {
    protected String url;         // instance variable
    protected Connection conn;

    public DatabaseConnection(String url) {  // constructor
        this.url = url;
    }

    // Concrete — shared by all subclasses
    public void logQuery(String sql) {
        System.out.println("Executing: " + sql);
    }

    // Abstract — each DB implements differently
    public abstract void connect();
    public abstract ResultSet executeQuery(String sql);
}

// Interface
interface Cacheable {
    int TTL = 3600; // implicitly public static final

    void cache(String key, Object value); // implicitly public abstract
    Object retrieve(String key);

    // Default method (Java 8+) — has implementation
    default void invalidate(String key) {
        System.out.println("Invalidating cache key: " + key);
    }

    // Static method (Java 8+)
    static boolean isValidKey(String key) {
        return key != null && !key.isEmpty();
    }
}

// A class can only extend ONE abstract class
// but implement MULTIPLE interfaces
class MySQLConnection extends DatabaseConnection implements Cacheable, Closeable {
    public MySQLConnection(String url) { super(url); }
    public void connect() { /* MySQL specific */ }
    public ResultSet executeQuery(String sql) { return null; }
    public void cache(String key, Object value) { /* ... */ }
    public Object retrieve(String key) { return null; }
    public void close() { /* ... */ }
}
```

---

### Q10. What is method overloading vs method overriding?

**Answer:**

**Method Overloading (Compile-time polymorphism):**
- Same method name, different parameters (type, number, or order)
- In the SAME class
- Return type can be different (but not the sole differentiator)
- Resolved at compile time

```java
class Printer {
    void print(int n) { System.out.println("int: " + n); }
    void print(double d) { System.out.println("double: " + d); }
    void print(String s) { System.out.println("String: " + s); }
    void print(int a, int b) { System.out.println("two ints: " + a + ", " + b); }

    // Cannot overload by return type alone:
    // int getValue() and double getValue() would be ambiguous
}

Printer p = new Printer();
p.print(5);          // int: 5
p.print(3.14);       // double: 3.14
p.print("hello");    // String: hello
p.print(1, 2);       // two ints: 1, 2
```

**Method Overriding (Runtime polymorphism):**
- Same method name, same parameters, same return type (or covariant)
- In SUBCLASS (inheriting from parent)
- `@Override` annotation recommended
- Access modifier can only be broadened (not restricted)
- Cannot override `static`, `final`, or `private` methods
- Resolved at runtime (dynamic dispatch)

```java
class Animal {
    public String sound() { return "Generic sound"; }
    public Animal create() { return new Animal(); } // covariant return allowed
}

class Dog extends Animal {
    @Override
    public String sound() { return "Woof"; }

    @Override
    public Dog create() { return new Dog(); } // covariant — Dog extends Animal
}

class Cat extends Animal {
    @Override
    public String sound() { return "Meow"; }
}

// Dynamic dispatch
Animal[] animals = { new Dog(), new Cat(), new Animal() };
for (Animal a : animals) {
    System.out.println(a.sound()); // Woof, Meow, Generic sound
}
```

---

### Q11. What is the difference between `this` and `super` keywords?

**Answer:**

```java
class Person {
    String name;
    int age;

    Person(String name, int age) {
        this.name = name;   // 'this' refers to current object
        this.age = age;
    }

    void display() {
        System.out.println("Person: " + name + ", " + age);
    }
}

class Student extends Person {
    String course;
    double gpa;

    Student(String name, int age, String course, double gpa) {
        super(name, age);       // 'super' calls parent constructor — MUST be first line
        this.course = course;   // 'this' refers to current Student object
        this.gpa = gpa;
    }

    // Constructor chaining with 'this'
    Student(String name, String course) {
        this(name, 20, course, 0.0); // calls another constructor in same class
    }

    @Override
    void display() {
        super.display();       // calls parent method
        System.out.println("Student: " + course + ", GPA: " + gpa);
    }

    void showInfo() {
        System.out.println(this);       // current object
        System.out.println(this.name);  // access own field
        super.display();                // call parent's display
    }
}
```

**Rules:**
- `super()` must be the first statement in constructor
- `this()` must be the first statement in constructor
- Both `super()` and `this()` cannot be used together in same constructor
- Cannot use `super` or `this` in static methods

---

### Q12. What is constructor chaining in Java?

**Answer:**

Constructor chaining is calling one constructor from another, either in the same class (`this()`) or parent class (`super()`).

```java
class Employee {
    String name;
    int age;
    String department;
    double salary;

    // Most specific constructor
    Employee(String name, int age, String department, double salary) {
        this.name = name;
        this.age = age;
        this.department = department;
        this.salary = salary;
        System.out.println("4-arg constructor called");
    }

    // Delegates to 4-arg
    Employee(String name, int age, String department) {
        this(name, age, department, 30000.0); // default salary
        System.out.println("3-arg constructor called");
    }

    // Delegates to 3-arg
    Employee(String name, int age) {
        this(name, age, "General"); // default department
        System.out.println("2-arg constructor called");
    }

    // Delegates to 2-arg
    Employee(String name) {
        this(name, 25); // default age
        System.out.println("1-arg constructor called");
    }
}

// new Employee("Kiran") prints in this ORDER:
// 4-arg constructor called
// 3-arg constructor called
// 2-arg constructor called
// 1-arg constructor called
// (innermost completes first, then unwinds)
```

---

## 3. JAVA DATA TYPES & VARIABLES

---

### Q13. What are primitive data types in Java?

**Answer:**

Java has 8 primitive types. They are NOT objects and stored on the stack.

| Type | Size | Range | Default | Example |
|---|---|---|---|---|
| `byte` | 1 byte | -128 to 127 | 0 | `byte b = 100;` |
| `short` | 2 bytes | -32,768 to 32,767 | 0 | `short s = 1000;` |
| `int` | 4 bytes | -2^31 to 2^31-1 | 0 | `int i = 42;` |
| `long` | 8 bytes | -2^63 to 2^63-1 | 0L | `long l = 100L;` |
| `float` | 4 bytes | ~±3.4e38 (7 digits) | 0.0f | `float f = 3.14f;` |
| `double` | 8 bytes | ~±1.7e308 (15 digits) | 0.0d | `double d = 3.14;` |
| `char` | 2 bytes | '\u0000' to '\uffff' | '\u0000' | `char c = 'A';` |
| `boolean` | 1 bit | true / false | false | `boolean b = true;` |

```java
// Type casting
int i = 1000;
byte b = (byte) i;     // narrowing — explicit cast required; may lose data
double d = i;          // widening — automatic (no cast needed)

// Widening order (automatic):
// byte → short → int → long → float → double
// char → int

// Wrapper classes (Objects for primitives)
Integer num = 42;         // autoboxing (int → Integer)
int primitive = num;      // unboxing (Integer → int)

Integer.parseInt("42");   // String → int
String.valueOf(42);       // int → String
Integer.MAX_VALUE;        // 2147483647
Integer.toBinaryString(10); // "1010"
```

---

### Q14. What is autoboxing and unboxing?

**Answer:**

**Autoboxing** — Automatic conversion of primitive to its wrapper class object
**Unboxing** — Automatic conversion of wrapper class object to primitive

```java
// Autoboxing
Integer obj = 42;           // auto: int → Integer
List<Integer> list = new ArrayList<>();
list.add(10);               // auto: int → Integer

// Unboxing
int val = obj;              // auto: Integer → int
int sum = obj + 5;          // auto: unbox obj, then add

// Performance warning — autoboxing in loops is expensive
// BAD:
Long sum1 = 0L;
for (int i = 0; i < 1_000_000; i++) {
    sum1 += i; // creates 1 million Long objects!
}

// GOOD:
long sum2 = 0L;
for (int i = 0; i < 1_000_000; i++) {
    sum2 += i; // no object creation
}

// NullPointerException with unboxing
Integer x = null;
int y = x; // NullPointerException at runtime!
```

---

## 4. CONTROL FLOW & LOOPS

---

### Q15. What is the difference between `break`, `continue`, and `return`?

**Answer:**

```java
// break — exits the loop/switch entirely
for (int i = 0; i < 10; i++) {
    if (i == 5) break;
    System.out.print(i + " ");
}
// Output: 0 1 2 3 4

// continue — skips current iteration, continues loop
for (int i = 0; i < 10; i++) {
    if (i % 2 == 0) continue;
    System.out.print(i + " ");
}
// Output: 1 3 5 7 9

// return — exits the entire method
public int findFirst(int[] arr, int target) {
    for (int i = 0; i < arr.length; i++) {
        if (arr[i] == target) return i; // exits method immediately
    }
    return -1;
}

// Labeled break — break outer loop from inner loop
outer:
for (int i = 0; i < 3; i++) {
    for (int j = 0; j < 3; j++) {
        if (i == 1 && j == 1) break outer; // exits outer loop
        System.out.println(i + "," + j);
    }
}
// Output: 0,0  0,1  0,2  1,0  (stops at 1,1)
```

---

## 5. ARRAYS & STRINGS

---

### Q16. How do arrays work in Java?

**Answer:**

```java
// Declaration and initialization
int[] arr1 = new int[5];           // {0, 0, 0, 0, 0}
int[] arr2 = {10, 20, 30, 40, 50}; // direct initialization
int[] arr3 = new int[]{1, 2, 3};   // anonymous array

// 2D array
int[][] matrix = new int[3][4];    // 3 rows, 4 columns
int[][] grid = {{1,2,3}, {4,5,6}, {7,8,9}};

// Jagged array (rows with different lengths)
int[][] jagged = new int[3][];
jagged[0] = new int[2];
jagged[1] = new int[4];
jagged[2] = new int[1];

// Array operations
System.out.println(arr2.length);   // 5
Arrays.sort(arr2);                 // in-place sort
Arrays.fill(arr1, 7);              // fill all with 7
int[] copy = Arrays.copyOf(arr2, arr2.length);       // full copy
int[] range = Arrays.copyOfRange(arr2, 1, 4);        // elements 1–3
System.out.println(Arrays.toString(arr2));            // [10, 20, 30, 40, 50]
System.out.println(Arrays.deepToString(grid));        // [[1,2,3],[4,5,6],[7,8,9]]
int idx = Arrays.binarySearch(arr2, 30);             // binary search (sorted array)
```

---

### Q17. What is the difference between String, StringBuilder, and StringBuffer?

**Answer:**

| Feature | String | StringBuilder | StringBuffer |
|---|---|---|---|
| Mutability | Immutable | Mutable | Mutable |
| Thread Safety | Thread-safe (immutable) | Not thread-safe | Thread-safe (synchronized) |
| Performance | Slow for concatenation | Fast | Slower than StringBuilder |
| Storage | String Pool / Heap | Heap | Heap |
| Use When | String constants, keys | Single-threaded string ops | Multi-threaded string ops |

```java
// String — immutable
String s = "Hello";
s.concat(" World");          // creates NEW string, s unchanged
s = s + " World";            // creates NEW string, then assigns
System.out.println(s);       // "Hello World"

// String methods
String str = "  Hello, World!  ";
str.length();                // 17
str.trim();                  // "Hello, World!"
str.toLowerCase();           // "  hello, world!  "
str.toUpperCase();           // "  HELLO, WORLD!  "
str.substring(2, 7);         // "Hello"
str.indexOf("World");        // 9
str.contains("Hello");       // true
str.replace("Hello", "Hi");  // "  Hi, World!  "
str.split(",");              // ["  Hello", " World!  "]
str.charAt(2);               // 'H'
str.startsWith("  H");       // true
str.endsWith("!  ");         // true
str.isEmpty();               // false
str.isBlank();               // false (Java 11+)
String.format("Hi %s, you are %d", "Kiran", 25);

// StringBuilder — mutable, not thread-safe
StringBuilder sb = new StringBuilder("Hello");
sb.append(" World");         // modifies same object
sb.insert(5, ",");           // "Hello, World"
sb.delete(5, 6);             // "Hello World"
sb.replace(6, 11, "Java");   // "Hello Java"
sb.reverse();                // "avaJ olleH"
sb.toString();               // convert to String

// Performance comparison:
// BAD — creates 10,000 String objects
String result = "";
for (int i = 0; i < 10000; i++) {
    result += i;  // new String every iteration
}

// GOOD — one StringBuilder object
StringBuilder sb2 = new StringBuilder();
for (int i = 0; i < 10000; i++) {
    sb2.append(i);
}
String result2 = sb2.toString();
```

---

### Q18. What is String interning and the String pool?

**Answer:**

```java
// String Pool (String Constant Pool) — in Heap (Java 7+)
// String literals are automatically stored in the pool

String s1 = "hello";   // stored in pool
String s2 = "hello";   // reuses same pool object
System.out.println(s1 == s2);  // true — same reference

// new String() — bypasses pool, creates heap object
String s3 = new String("hello");
System.out.println(s1 == s3);       // false — different objects
System.out.println(s1.equals(s3));  // true — same content

// intern() — manually add to pool or get existing pool reference
String s4 = s3.intern();
System.out.println(s1 == s4);  // true — s4 now points to pool object

// String concatenation at compile time goes to pool
String s5 = "hel" + "lo";  // compile-time constant — goes to pool
System.out.println(s1 == s5); // true

// Runtime concatenation does NOT go to pool
String part = "hel";
String s6 = part + "lo";    // runtime — new heap object
System.out.println(s1 == s6); // false
```

---

## 6. COLLECTIONS FRAMEWORK

---

### Q19. What is the Java Collections Framework?

**Answer:**

The Java Collections Framework provides a set of **interfaces and classes** for storing and manipulating groups of objects.

```
Collection (Interface)
├── List (Interface) — ordered, allows duplicates
│   ├── ArrayList
│   ├── LinkedList
│   └── Vector (legacy)
│       └── Stack (legacy)
├── Set (Interface) — no duplicates
│   ├── HashSet — no order
│   ├── LinkedHashSet — insertion order
│   └── TreeSet — sorted order
└── Queue (Interface) — FIFO
    ├── LinkedList
    ├── PriorityQueue
    └── Deque (Interface) — double-ended queue
        └── ArrayDeque

Map (Interface) — key-value pairs (NOT Collection)
├── HashMap — no order
├── LinkedHashMap — insertion order
├── TreeMap — sorted by key
└── Hashtable (legacy)
    └── Properties (legacy)
```

---

### Q20. What is the difference between ArrayList and LinkedList?

**Answer:**

| Operation | ArrayList | LinkedList |
|---|---|---|
| Internal Structure | Dynamic array | Doubly linked list |
| Access by index | O(1) — fast | O(n) — slow |
| Add/remove at end | O(1) amortized | O(1) |
| Add/remove at middle | O(n) — shift elements | O(n) — traverse + O(1) insert |
| Memory | Less (just data + index) | More (data + two pointers per node) |
| Iterator performance | Better | Worse for random access |
| Use When | Frequent reads | Frequent insert/delete at beginning/middle |

```java
import java.util.*;

// ArrayList
List<String> arrayList = new ArrayList<>();
arrayList.add("A");
arrayList.add("B");
arrayList.add(0, "Z");          // insert at index — shifts elements
arrayList.remove(1);            // remove by index
arrayList.remove("B");          // remove by value
arrayList.get(0);               // O(1) random access
arrayList.set(0, "X");          // update by index
arrayList.size();               // size
arrayList.contains("A");        // true/false
arrayList.indexOf("A");         // first index of
Collections.sort(arrayList);    // sort
Collections.reverse(arrayList); // reverse

// LinkedList — also implements Deque
LinkedList<String> linkedList = new LinkedList<>();
linkedList.addFirst("A");    // add to front
linkedList.addLast("B");     // add to back
linkedList.removeFirst();    // remove from front
linkedList.removeLast();     // remove from back
linkedList.peekFirst();      // view front without removing
linkedList.peekLast();       // view back without removing
```

---

### Q21. What is the difference between HashMap, LinkedHashMap, and TreeMap?

**Answer:**

```java
// HashMap — unordered, O(1) average get/put, allows one null key
Map<String, Integer> hashMap = new HashMap<>();
hashMap.put("banana", 2);
hashMap.put("apple", 1);
hashMap.put("cherry", 3);
hashMap.put(null, 0);       // null key allowed
// Iteration order: unpredictable

// LinkedHashMap — maintains insertion order, O(1) get/put
Map<String, Integer> linkedHashMap = new LinkedHashMap<>();
linkedHashMap.put("banana", 2);
linkedHashMap.put("apple", 1);
linkedHashMap.put("cherry", 3);
// Iteration order: banana, apple, cherry (insertion order)

// TreeMap — sorted by key (natural or custom), O(log n) get/put
Map<String, Integer> treeMap = new TreeMap<>();
treeMap.put("banana", 2);
treeMap.put("apple", 1);
treeMap.put("cherry", 3);
// Iteration order: apple, banana, cherry (sorted alphabetically)

// Common Map operations
hashMap.get("apple");                      // 1
hashMap.getOrDefault("mango", 0);          // 0 (not found)
hashMap.put("apple", 5);                   // update
hashMap.putIfAbsent("apple", 10);          // only adds if key absent
hashMap.remove("banana");                  // remove by key
hashMap.containsKey("apple");              // true
hashMap.containsValue(5);                  // true
hashMap.size();                            // count
hashMap.isEmpty();                         // false

// Iterating a Map
for (Map.Entry<String, Integer> entry : hashMap.entrySet()) {
    System.out.println(entry.getKey() + ": " + entry.getValue());
}

// Java 8+
hashMap.forEach((key, value) -> System.out.println(key + ": " + value));
hashMap.computeIfAbsent("mango", k -> 0);
hashMap.merge("apple", 1, Integer::sum);   // add 1 to existing value
```

---

### Q22. What is the difference between HashSet, LinkedHashSet, and TreeSet?

**Answer:**

```java
// HashSet — no duplicates, unordered, O(1) add/contains
Set<String> hashSet = new HashSet<>();
hashSet.add("banana");
hashSet.add("apple");
hashSet.add("banana");   // duplicate — ignored
hashSet.add(null);       // null allowed (once)
System.out.println(hashSet.size()); // 2

// LinkedHashSet — no duplicates, maintains insertion order
Set<String> linkedHashSet = new LinkedHashSet<>();
linkedHashSet.add("banana");
linkedHashSet.add("apple");
linkedHashSet.add("cherry");
// Iteration: banana, apple, cherry

// TreeSet — no duplicates, sorted order (natural/custom), O(log n)
Set<String> treeSet = new TreeSet<>();
treeSet.add("banana");
treeSet.add("apple");
treeSet.add("cherry");
// Iteration: apple, banana, cherry (sorted)

TreeSet<Integer> numSet = new TreeSet<>();
numSet.addAll(Arrays.asList(5, 3, 8, 1, 9, 2));
numSet.first();     // 1
numSet.last();      // 9
numSet.floor(6);    // 5 (greatest <= 6)
numSet.ceiling(6);  // 8 (smallest >= 6)
numSet.headSet(5);  // {1, 2, 3} (less than 5)
numSet.tailSet(5);  // {5, 8, 9} (>=5)

// Set operations
Set<Integer> a = new HashSet<>(Arrays.asList(1, 2, 3, 4));
Set<Integer> b = new HashSet<>(Arrays.asList(3, 4, 5, 6));

Set<Integer> union = new HashSet<>(a); union.addAll(b);        // {1,2,3,4,5,6}
Set<Integer> intersection = new HashSet<>(a); intersection.retainAll(b); // {3,4}
Set<Integer> difference = new HashSet<>(a); difference.removeAll(b);     // {1,2}
```

---

### Q23. What is the difference between Iterator and ListIterator?

**Answer:**

```java
List<String> list = new ArrayList<>(Arrays.asList("A", "B", "C", "D"));

// Iterator — forward only, works with any Collection
Iterator<String> it = list.iterator();
while (it.hasNext()) {
    String s = it.next();
    if (s.equals("B")) {
        it.remove(); // safe removal during iteration
    }
}

// ListIterator — bidirectional, only for List
ListIterator<String> lit = list.listIterator();

// Forward
while (lit.hasNext()) {
    System.out.println(lit.nextIndex() + ": " + lit.next());
}

// Backward
while (lit.hasPrevious()) {
    System.out.println(lit.previousIndex() + ": " + lit.previous());
}

// Can add and set during iteration
ListIterator<String> lit2 = list.listIterator();
while (lit2.hasNext()) {
    String s = lit2.next();
    lit2.set(s.toLowerCase());  // replace current element
    if (s.equals("C")) {
        lit2.add("C+");          // add after current position
    }
}

// ConcurrentModificationException
// Modifying collection during enhanced for-loop throws this
// Always use Iterator.remove() for safe removal
```

---

## 7. EXCEPTION HANDLING

---

### Q24. What is the exception hierarchy in Java?

**Answer:**

```
Throwable
├── Error (serious problems — don't catch)
│   ├── OutOfMemoryError
│   ├── StackOverflowError
│   └── VirtualMachineError
└── Exception
    ├── RuntimeException (Unchecked — don't need to declare/catch)
    │   ├── NullPointerException
    │   ├── ArrayIndexOutOfBoundsException
    │   ├── ClassCastException
    │   ├── IllegalArgumentException
    │   ├── ArithmeticException
    │   ├── NumberFormatException
    │   └── IllegalStateException
    └── Checked Exceptions (must declare or catch)
        ├── IOException
        ├── FileNotFoundException
        ├── SQLException
        ├── ClassNotFoundException
        └── InterruptedException
```

**Checked vs Unchecked:**
- **Checked:** Compiler forces you to handle them (try-catch or `throws`)
- **Unchecked (RuntimeException):** Not forced — programming mistakes
- **Error:** JVM-level problems — should NOT be caught

```java
// Checked exception — must handle
public void readFile(String path) throws IOException {
    BufferedReader br = new BufferedReader(new FileReader(path));
    // OR use try-catch
}

// Unchecked — no need to declare
public int divide(int a, int b) {
    return a / b; // throws ArithmeticException if b=0 — no throws clause needed
}
```

---

### Q25. How does exception handling work in Java?

**Answer:**

```java
public class ExceptionDemo {

    // try-catch-finally
    public static void example1() {
        try {
            int result = 10 / 0;            // throws ArithmeticException
            System.out.println(result);      // not reached
        } catch (ArithmeticException e) {
            System.out.println("Math error: " + e.getMessage()); // / by zero
        } catch (Exception e) {
            System.out.println("General error: " + e.getMessage()); // fallback
        } finally {
            System.out.println("finally always runs"); // even if return/exception
        }
    }

    // Multi-catch (Java 7+)
    public static void example2(String s, String[] arr) {
        try {
            int len = s.length();      // may throw NullPointerException
            int val = Integer.parseInt(s); // may throw NumberFormatException
            System.out.println(arr[10]);   // may throw ArrayIndexOutOfBoundsException
        } catch (NullPointerException | NumberFormatException e) {
            System.out.println("Input error: " + e.getMessage());
        } catch (ArrayIndexOutOfBoundsException e) {
            System.out.println("Index error: " + e.getMessage());
        }
    }

    // try-with-resources (Java 7+) — auto-closes resources
    public static void readFile(String path) {
        try (
            FileReader fr = new FileReader(path);         // auto-closed
            BufferedReader br = new BufferedReader(fr)    // auto-closed
        ) {
            String line;
            while ((line = br.readLine()) != null) {
                System.out.println(line);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
        // fr and br are automatically closed (Closeable interface)
    }

    // Custom exception
    static class InsufficientFundsException extends Exception {
        private double amount;

        public InsufficientFundsException(double amount) {
            super("Insufficient funds: need " + amount + " more");
            this.amount = amount;
        }

        public double getAmount() { return amount; }
    }

    // throw vs throws
    public void withdraw(double amount) throws InsufficientFundsException {
        if (amount > balance) {
            throw new InsufficientFundsException(amount - balance); // throw an instance
        }
        balance -= amount;
    }

    // Exception chaining
    public void processData(String data) throws RuntimeException {
        try {
            int value = Integer.parseInt(data);
        } catch (NumberFormatException e) {
            throw new RuntimeException("Failed to process data: " + data, e); // wrap with cause
        }
    }
}
```

---

## 8. JAVA MEMORY MANAGEMENT

---

### Q26. How does Java manage memory? Explain the Heap and Stack.

**Answer:**

```
JVM Memory Layout
├── Stack (per thread)
│   ├── Method call frames
│   ├── Local variables (primitives + object references)
│   └── Fast allocation/deallocation (LIFO)
│
├── Heap (shared by all threads)
│   ├── Young Generation
│   │   ├── Eden Space (new objects)
│   │   ├── Survivor Space S0
│   │   └── Survivor Space S1
│   └── Old Generation (Tenured) — long-lived objects
│
├── Metaspace (Java 8+, was PermGen)
│   ├── Class metadata
│   ├── Method bytecode
│   └── Static variables
│
├── Code Cache — JIT-compiled native code
└── PC Registers — per-thread program counter
```

```java
public class MemoryDemo {
    // Static field — Metaspace
    static int staticVar = 10;

    public static void main(String[] args) {
        // Primitives — Stack
        int x = 5;
        double d = 3.14;

        // Object — reference on Stack, object on Heap
        String name = "Kiran";           // string pool (heap)
        StringBuilder sb = new StringBuilder(); // heap

        // Array — reference on Stack, array on Heap
        int[] arr = new int[10];

        calculate(x);
    }

    static int calculate(int n) {
        // New stack frame created for this method call
        int result = n * 2;   // Stack
        return result;
        // Frame popped off stack when method returns
    }
}
```

---

### Q27. What is Garbage Collection in Java?

**Answer:**

Garbage Collection (GC) automatically **reclaims memory** occupied by objects that are no longer reachable (no live references to them).

```java
public class GCDemo {
    public static void main(String[] args) {
        // Object eligible for GC when no more references
        String s = new String("hello"); // object in heap
        s = null;                        // eligible for GC now

        // Or when reference goes out of scope
        {
            Object obj = new Object();   // created
        }                                // obj out of scope — eligible for GC

        // Request GC (JVM may ignore!)
        System.gc();             // suggestion, not a command
        Runtime.getRuntime().gc();

        // finalize() — called before GC (deprecated Java 9+)
        // DON'T rely on it for cleanup — use try-with-resources instead
    }
}
```

**GC Algorithms:**
- **Serial GC** — Single-threaded; for small apps
- **Parallel GC** — Multiple threads; throughput-focused (Java 8 default)
- **G1 GC** — Balanced; predictable pause times (Java 9+ default)
- **ZGC / Shenandoah** — Ultra-low pause times; for large heaps (Java 15+)

**Minor GC** — Young generation collection (frequent, fast)
**Major/Full GC** — Old generation or full heap (infrequent, slow — "stop the world")

---

## 9. MULTITHREADING & CONCURRENCY

---

### Q28. What is multithreading in Java? How do you create threads?

**Answer:**

Multithreading allows **concurrent execution** of two or more threads (units of a process).

```java
// Method 1: Extend Thread class
class MyThread extends Thread {
    private String name;

    MyThread(String name) { this.name = name; }

    @Override
    public void run() {
        for (int i = 0; i < 5; i++) {
            System.out.println(name + ": " + i);
            try { Thread.sleep(100); } catch (InterruptedException e) {}
        }
    }
}

// Method 2: Implement Runnable interface (PREFERRED)
class MyRunnable implements Runnable {
    @Override
    public void run() {
        System.out.println("Runnable running in: " + Thread.currentThread().getName());
    }
}

// Method 3: Lambda (Java 8+)
Runnable lambda = () -> System.out.println("Lambda thread");

public class ThreadDemo {
    public static void main(String[] args) throws InterruptedException {
        // Using Thread subclass
        MyThread t1 = new MyThread("Thread-1");
        t1.start(); // starts new thread — DO NOT call run() directly!

        // Using Runnable
        Thread t2 = new Thread(new MyRunnable(), "Thread-2");
        t2.start();

        // Using Lambda
        Thread t3 = new Thread(() -> System.out.println("Lambda"), "Thread-3");
        t3.start();

        // Thread methods
        t1.join();              // wait for t1 to finish
        t1.isAlive();           // check if running
        Thread.currentThread(); // get current thread
        Thread.sleep(1000);     // pause current thread (ms)
        t1.setPriority(Thread.MAX_PRIORITY); // 1-10, default 5

        // Thread states: NEW → RUNNABLE → BLOCKED/WAITING/TIMED_WAITING → TERMINATED
        System.out.println(t1.getState()); // TERMINATED
    }
}
```

---

### Q29. What is synchronization in Java?

**Answer:**

Synchronization prevents **race conditions** by ensuring only one thread accesses a critical section at a time.

```java
class BankAccount {
    private double balance;

    // Problem without sync: two threads might both read balance=100,
    // both add 50, both write 150 — losing one update (race condition)

    // Synchronized method — lock on 'this' object
    public synchronized void deposit(double amount) {
        balance += amount;
    }

    public synchronized double getBalance() {
        return balance;
    }

    // Synchronized block — more granular lock
    public void transfer(BankAccount target, double amount) {
        synchronized(this) {            // lock this account
            this.balance -= amount;
        }
        synchronized(target) {          // lock target account
            target.balance += amount;
        }
    }

    // Static synchronized — lock on Class object
    private static int transactionCount = 0;
    public static synchronized void incrementCount() {
        transactionCount++;
    }
}

// Volatile — visibility guarantee across threads (not atomicity)
class SharedData {
    volatile boolean flag = false;  // changes visible to all threads immediately
}

// Atomic classes — thread-safe without synchronized (lock-free)
import java.util.concurrent.atomic.*;

AtomicInteger counter = new AtomicInteger(0);
counter.incrementAndGet();              // atomic increment
counter.compareAndSet(5, 10);           // atomic CAS operation
AtomicReference<String> ref = new AtomicReference<>("hello");
```

---

### Q30. What is the difference between `wait()`, `notify()`, and `notifyAll()`?

**Answer:**

These methods are used for **inter-thread communication** and must be called within a `synchronized` block.

```java
class ProducerConsumer {
    private final List<Integer> buffer = new ArrayList<>();
    private final int MAX = 5;

    // Producer thread
    public synchronized void produce(int item) throws InterruptedException {
        while (buffer.size() == MAX) {
            System.out.println("Buffer full — producer waiting");
            wait();                    // releases lock, waits for notify
        }
        buffer.add(item);
        System.out.println("Produced: " + item);
        notifyAll();                   // wake up ALL waiting threads
    }

    // Consumer thread
    public synchronized int consume() throws InterruptedException {
        while (buffer.isEmpty()) {
            System.out.println("Buffer empty — consumer waiting");
            wait();                    // releases lock, waits for notify
        }
        int item = buffer.remove(0);
        System.out.println("Consumed: " + item);
        notifyAll();                   // wake up all waiting threads
        return item;
    }
}

// wait()      — current thread waits (releases lock) until notified
// notify()    — wakes up ONE waiting thread (which one? unpredictable)
// notifyAll() — wakes up ALL waiting threads (they compete for lock)
// All three must be called inside synchronized block/method
// Calling wait() in a loop (not if) prevents spurious wakeup issues
```

---

### Q31. What is the ExecutorService in Java?

**Answer:**

`ExecutorService` provides a **thread pool** for managing and executing tasks asynchronously, without manually creating threads.

```java
import java.util.concurrent.*;

// Fixed thread pool — always 4 threads
ExecutorService executor = Executors.newFixedThreadPool(4);

// Single thread executor
ExecutorService single = Executors.newSingleThreadExecutor();

// Cached thread pool — creates threads as needed, reuses idle ones
ExecutorService cached = Executors.newCachedThreadPool();

// Scheduled executor — for delayed/periodic tasks
ScheduledExecutorService scheduler = Executors.newScheduledThreadPool(2);

// Submit a Runnable (no return value)
executor.execute(() -> System.out.println("Task running"));

// Submit a Callable (returns a Future)
Future<Integer> future = executor.submit(() -> {
    Thread.sleep(1000);
    return 42;
});

// Get result (blocks until done)
try {
    Integer result = future.get();          // blocks
    Integer result2 = future.get(2, TimeUnit.SECONDS); // timeout
} catch (ExecutionException | InterruptedException | TimeoutException e) {
    e.printStackTrace();
}

future.isDone();    // check if completed
future.cancel(true); // cancel (true = interrupt if running)

// CompletableFuture (Java 8+) — non-blocking async
CompletableFuture<String> cf = CompletableFuture.supplyAsync(() -> "Hello")
    .thenApply(s -> s + " World")
    .thenApply(String::toUpperCase);

cf.thenAccept(System.out::println);  // "HELLO WORLD"

// Always shutdown executor when done
executor.shutdown();                    // waits for tasks to complete
executor.shutdownNow();                 // interrupts running tasks
executor.awaitTermination(60, TimeUnit.SECONDS);
```

---

## 10. JAVA 8+ FEATURES

---

### Q32. What are lambda expressions in Java 8?

**Answer:**

Lambda expressions provide a **concise way to represent functional interfaces** (interfaces with a single abstract method).

```java
// Syntax: (parameters) -> expression
//         (parameters) -> { statements; }

// Before Java 8 — anonymous inner class
Runnable r = new Runnable() {
    @Override
    public void run() {
        System.out.println("Running");
    }
};

// Java 8 lambda
Runnable r = () -> System.out.println("Running");

// With parameters
Comparator<String> comp = (s1, s2) -> s1.compareTo(s2);
// Or method reference:
Comparator<String> comp2 = String::compareTo;

// Functional interfaces (java.util.function)
// Predicate<T>  — takes T, returns boolean
Predicate<String> isEmpty = s -> s.isEmpty();
Predicate<Integer> isEven = n -> n % 2 == 0;
isEven.and(n -> n > 0);     // composed predicate
isEven.or(n -> n < 0);
isEven.negate();             // NOT

// Function<T, R>  — takes T, returns R
Function<String, Integer> strLen = s -> s.length();
Function<Integer, String> intToStr = n -> "Number: " + n;
Function<String, String> combined = strLen.andThen(intToStr); // compose

// Consumer<T>  — takes T, returns void
Consumer<String> printer = s -> System.out.println(s);
Consumer<String> printer2 = System.out::println; // method reference

// Supplier<T>  — takes nothing, returns T
Supplier<String> greeting = () -> "Hello, World!";
Supplier<List<String>> listFactory = ArrayList::new;

// BiFunction<T, U, R>  — takes T and U, returns R
BiFunction<String, Integer, String> repeat = (s, n) -> s.repeat(n);

// UnaryOperator<T>  — takes T, returns T
UnaryOperator<String> upper = String::toUpperCase;

// BinaryOperator<T>  — takes two T, returns T
BinaryOperator<Integer> add = (a, b) -> a + b;
BinaryOperator<Integer> add2 = Integer::sum;
```

---

### Q33. What is the Stream API in Java 8?

**Answer:**

The Stream API provides a **functional, declarative way to process collections** — like pipelines of operations.

```java
List<String> names = Arrays.asList("Alice", "Bob", "Charlie", "David", "Eve");
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

// --- INTERMEDIATE OPERATIONS (lazy, return Stream) ---

// filter — keep elements matching predicate
names.stream()
    .filter(n -> n.length() > 3)
    .collect(Collectors.toList()); // [Alice, Charlie, David]

// map — transform each element
names.stream()
    .map(String::toUpperCase)
    .collect(Collectors.toList()); // [ALICE, BOB, ...]

// flatMap — flatten nested collections
List<List<Integer>> nested = Arrays.asList(
    Arrays.asList(1, 2), Arrays.asList(3, 4), Arrays.asList(5, 6)
);
nested.stream()
    .flatMap(Collection::stream)
    .collect(Collectors.toList()); // [1, 2, 3, 4, 5, 6]

// sorted — natural or custom order
names.stream().sorted().collect(Collectors.toList());
names.stream().sorted(Comparator.comparingInt(String::length));

// distinct — remove duplicates
Arrays.asList(1,2,2,3,3,3).stream().distinct().collect(Collectors.toList()); // [1,2,3]

// limit / skip
numbers.stream().limit(5).collect(Collectors.toList()); // [1,2,3,4,5]
numbers.stream().skip(5).collect(Collectors.toList());  // [6,7,8,9,10]

// peek — for debugging (doesn't modify stream)
numbers.stream().peek(n -> System.out.print(n + " ")).collect(Collectors.toList());

// --- TERMINAL OPERATIONS (eager, produce result) ---

// collect
List<String> longNames = names.stream()
    .filter(n -> n.length() > 3)
    .collect(Collectors.toList());

// Various collectors
Collectors.toSet();
Collectors.toUnmodifiableList();
Collectors.joining(", ", "[", "]");       // "[Alice, Charlie, David]"
Collectors.counting();                     // count
Collectors.groupingBy(String::length);     // Map<Integer, List<String>>
Collectors.partitioningBy(n -> n.length() > 3); // Map<Boolean, List<String>>
Collectors.toMap(s -> s, String::length); // Map<String, Integer>
Collectors.summarizingInt(String::length); // IntSummaryStatistics

// forEach
names.stream().forEach(System.out::println);

// reduce
int sum = numbers.stream().reduce(0, Integer::sum);
Optional<Integer> max = numbers.stream().reduce(Integer::max);

// count, min, max, sum, average (for numbers)
long count = numbers.stream().count();
OptionalInt min = numbers.stream().mapToInt(Integer::intValue).min();
int total = numbers.stream().mapToInt(Integer::intValue).sum();
OptionalDouble avg = numbers.stream().mapToInt(Integer::intValue).average();

// findFirst / findAny
Optional<String> first = names.stream().filter(n -> n.startsWith("A")).findFirst();

// anyMatch / allMatch / noneMatch
boolean hasLong = names.stream().anyMatch(n -> n.length() > 5);  // true
boolean allShort = names.stream().allMatch(n -> n.length() < 10); // true
boolean noneEmpty = names.stream().noneMatch(String::isEmpty);    // true

// Parallel stream — uses ForkJoinPool
long result = numbers.parallelStream()
    .filter(n -> n % 2 == 0)
    .mapToLong(Integer::longValue)
    .sum();
```

---

### Q34. What is `Optional` in Java 8?

**Answer:**

`Optional<T>` is a container that **may or may not contain a non-null value** — designed to avoid NullPointerException.

```java
// Creating Optional
Optional<String> empty = Optional.empty();
Optional<String> present = Optional.of("hello");          // throws NPE if null
Optional<String> nullable = Optional.ofNullable(null);   // safe — allows null

// Checking and getting
present.isPresent();                   // true
present.isEmpty();                     // false (Java 11+)
present.get();                         // "hello" (throws if empty!)

// Safe access
present.orElse("default");            // "hello"
empty.orElse("default");              // "default"
empty.orElseGet(() -> computeDefault()); // lazy — only called if empty
empty.orElseThrow(() -> new RuntimeException("No value")); // throw if empty

// Transformations
present.map(String::toUpperCase);     // Optional<String> containing "HELLO"
present.filter(s -> s.length() > 3); // Optional.empty() (length 5 > 3 = present)
present.flatMap(s -> Optional.of(s.length())); // Optional<Integer>

// ifPresent
present.ifPresent(System.out::println);                    // prints "hello"
present.ifPresentOrElse(                                   // Java 9+
    s -> System.out.println("Found: " + s),
    () -> System.out.println("Not found")
);

// Real-world usage
public Optional<User> findUserById(int id) {
    return userRepository.findById(id); // returns Optional
}

findUserById(1)
    .filter(user -> user.isActive())
    .map(User::getEmail)
    .orElse("no-email@example.com");
```

---

### Q35. What are default and static methods in interfaces (Java 8)?

**Answer:**

```java
interface Collection<E> {
    void add(E element);
    void remove(E element);

    // Default method — has body; can be overridden
    default boolean isEmpty() {
        return size() == 0;
    }
    int size();

    // Static method — belongs to interface, not inherited
    static <T> Collection<T> empty() {
        return new EmptyCollection<>();
    }
}

// Interface evolution — can add default methods without breaking existing implementations
interface Validator<T> {
    boolean validate(T value);

    // Default — compose validators
    default Validator<T> and(Validator<T> other) {
        return value -> this.validate(value) && other.validate(value);
    }

    default Validator<T> or(Validator<T> other) {
        return value -> this.validate(value) || other.validate(value);
    }

    default Validator<T> negate() {
        return value -> !this.validate(value);
    }
}

Validator<String> notEmpty = s -> !s.isEmpty();
Validator<String> notTooLong = s -> s.length() <= 100;
Validator<String> combined = notEmpty.and(notTooLong);

// Java 9+ — private methods in interfaces
interface Logger {
    default void logInfo(String msg) { log("INFO", msg); }
    default void logError(String msg) { log("ERROR", msg); }

    private void log(String level, String msg) {     // private helper
        System.out.println("[" + level + "] " + msg);
    }
}
```

---

## 11. GENERICS

---

### Q36. What are generics in Java?

**Answer:**

Generics provide **type safety at compile time** — allowing classes, interfaces, and methods to work with different types without casting.

```java
// Generic class
public class Box<T> {
    private T value;

    public Box(T value) { this.value = value; }
    public T getValue() { return value; }

    @Override
    public String toString() { return "Box[" + value + "]"; }
}

Box<String> strBox = new Box<>("hello");
Box<Integer> intBox = new Box<>(42);
// Box<int> rawBox; // Error — generics don't work with primitives

// Generic method
public static <T extends Comparable<T>> T max(T a, T b) {
    return a.compareTo(b) >= 0 ? a : b;
}

max(3, 7);           // 7
max("apple", "mango"); // "mango"

// Multiple type parameters
public class Pair<A, B> {
    public final A first;
    public final B second;

    public Pair(A first, B second) {
        this.first = first;
        this.second = second;
    }
}
Pair<String, Integer> p = new Pair<>("age", 25);

// Bounded type parameters
// Upper bound — T must be Number or subclass
public <T extends Number> double sum(List<T> list) {
    return list.stream().mapToDouble(Number::doubleValue).sum();
}

// Wildcards
List<? extends Number> upperBounded;   // read-only list of Number or subtype
List<? super Integer> lowerBounded;    // writable list of Integer or supertype
List<?> unbounded;                     // any type — read-only (returns Object)

// PECS principle — Producer Extends, Consumer Super
public <T> void copy(List<? extends T> source, List<? super T> dest) {
    for (T item : source) {
        dest.add(item);
    }
}

// Type erasure — generics are removed at compile time
// At runtime, Box<String> and Box<Integer> are both just Box
// Cannot do: new T(); or (T) obj; or instanceof T; or T.class
```

---

## 12. FILE I/O & SERIALIZATION

---

### Q37. How do you perform File I/O in Java?

**Answer:**

```java
import java.io.*;
import java.nio.file.*;
import java.nio.charset.StandardCharsets;

// --- Old I/O (java.io) ---

// Writing to file
try (BufferedWriter writer = new BufferedWriter(new FileWriter("output.txt"))) {
    writer.write("Hello, World!");
    writer.newLine();
    writer.write("Second line");
}

// Reading from file
try (BufferedReader reader = new BufferedReader(new FileReader("output.txt"))) {
    String line;
    while ((line = reader.readLine()) != null) {
        System.out.println(line);
    }
}

// --- New I/O (java.nio — Java 7+, PREFERRED) ---

Path path = Paths.get("output.txt");

// Write all bytes/lines at once
Files.write(path, "Hello NIO".getBytes(StandardCharsets.UTF_8));
Files.write(path, Arrays.asList("line1", "line2"), StandardCharsets.UTF_8);

// Read all content at once
String content = Files.readString(path);              // Java 11+
List<String> lines = Files.readAllLines(path);
byte[] bytes = Files.readAllBytes(path);

// Stream lines (lazy — good for large files)
try (Stream<String> stream = Files.lines(path)) {
    stream.filter(l -> l.contains("Hello"))
          .forEach(System.out::println);
}

// File operations
Files.copy(source, target, StandardCopyOption.REPLACE_EXISTING);
Files.move(source, target, StandardCopyOption.REPLACE_EXISTING);
Files.delete(path);
Files.deleteIfExists(path);
Files.createDirectory(Paths.get("newdir"));
Files.createDirectories(Paths.get("a/b/c")); // creates all
Files.exists(path);
Files.isDirectory(path);
Files.size(path);

// List directory contents
try (Stream<Path> files = Files.list(Paths.get("."))) {
    files.forEach(System.out::println);
}

// Walk directory tree
Files.walk(Paths.get("."))
     .filter(Files::isRegularFile)
     .filter(p -> p.toString().endsWith(".java"))
     .forEach(System.out::println);
```

---

### Q38. What is serialization in Java?

**Answer:**

Serialization converts an object into a **byte stream** to store or transmit it. Deserialization reconstructs the object from the byte stream.

```java
import java.io.*;

// Must implement Serializable to be serializable
class User implements Serializable {
    private static final long serialVersionUID = 1L; // version identifier

    private String name;
    private int age;
    private transient String password; // transient — NOT serialized

    // Static fields are NOT serialized (belong to class, not instance)
    private static int count = 0;

    public User(String name, int age, String password) {
        this.name = name;
        this.age = age;
        this.password = password;
    }

    @Override
    public String toString() {
        return "User{name=" + name + ", age=" + age + ", password=" + password + "}";
    }
}

// Serialize
public static void serialize(User user, String filename) throws IOException {
    try (ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream(filename))) {
        oos.writeObject(user);
        System.out.println("Serialized: " + user);
    }
}

// Deserialize
public static User deserialize(String filename) throws IOException, ClassNotFoundException {
    try (ObjectInputStream ois = new ObjectInputStream(new FileInputStream(filename))) {
        User user = (User) ois.readObject();
        System.out.println("Deserialized: " + user);
        return user;
    }
}

// Usage
User u = new User("Kiran", 25, "secret123");
serialize(u, "user.ser");
// Serialized: User{name=Kiran, age=25, password=secret123}

User loaded = deserialize("user.ser");
// Deserialized: User{name=Kiran, age=25, password=null} — password is null (transient)
```

---

## 13. DESIGN PATTERNS IN JAVA

---

### Q39. What is the Singleton design pattern?

**Answer:**

Singleton ensures **only one instance** of a class exists and provides a global access point to it.

```java
// Thread-safe Singleton using enum (BEST approach)
public enum DatabaseConnection {
    INSTANCE;

    private Connection connection;

    DatabaseConnection() {
        // initialize connection
    }

    public Connection getConnection() { return connection; }
}

// Thread-safe Singleton using double-checked locking
public class Logger {
    private static volatile Logger instance; // volatile prevents instruction reordering

    private Logger() {}

    public static Logger getInstance() {
        if (instance == null) {                  // first check (no lock)
            synchronized (Logger.class) {
                if (instance == null) {          // second check (with lock)
                    instance = new Logger();
                }
            }
        }
        return instance;
    }

    public void log(String message) {
        System.out.println("[LOG] " + message);
    }
}

// Initialization-on-demand holder (lazy, thread-safe, no synchronization cost)
public class ConfigManager {
    private ConfigManager() {}

    private static class Holder {
        private static final ConfigManager INSTANCE = new ConfigManager();
    }

    public static ConfigManager getInstance() {
        return Holder.INSTANCE; // class loaded only when this method is called
    }
}
```

---

### Q40. What are the Factory and Builder design patterns?

**Answer:**

```java
// --- FACTORY PATTERN ---
// Creates objects without exposing instantiation logic

interface Shape {
    double area();
    String name();
}

class Circle implements Shape {
    private double radius;
    Circle(double radius) { this.radius = radius; }
    public double area() { return Math.PI * radius * radius; }
    public String name() { return "Circle"; }
}

class Rectangle implements Shape {
    private double w, h;
    Rectangle(double w, double h) { this.w = w; this.h = h; }
    public double area() { return w * h; }
    public String name() { return "Rectangle"; }
}

class ShapeFactory {
    public static Shape create(String type, double... params) {
        return switch (type.toLowerCase()) {
            case "circle"    -> new Circle(params[0]);
            case "rectangle" -> new Rectangle(params[0], params[1]);
            default -> throw new IllegalArgumentException("Unknown shape: " + type);
        };
    }
}

Shape c = ShapeFactory.create("circle", 5.0);
Shape r = ShapeFactory.create("rectangle", 4.0, 6.0);

// --- BUILDER PATTERN ---
// Step-by-step construction of complex objects

public class HttpRequest {
    // Required fields
    private final String url;
    private final String method;

    // Optional fields
    private final Map<String, String> headers;
    private final String body;
    private final int timeout;
    private final boolean followRedirects;

    private HttpRequest(Builder builder) {
        this.url = builder.url;
        this.method = builder.method;
        this.headers = Collections.unmodifiableMap(builder.headers);
        this.body = builder.body;
        this.timeout = builder.timeout;
        this.followRedirects = builder.followRedirects;
    }

    public static class Builder {
        private final String url;     // required
        private final String method;  // required
        private Map<String, String> headers = new HashMap<>();
        private String body = null;
        private int timeout = 30000;
        private boolean followRedirects = true;

        public Builder(String url, String method) {
            this.url = url;
            this.method = method;
        }

        public Builder header(String key, String value) {
            this.headers.put(key, value);
            return this;
        }

        public Builder body(String body) {
            this.body = body;
            return this;
        }

        public Builder timeout(int timeout) {
            this.timeout = timeout;
            return this;
        }

        public Builder followRedirects(boolean follow) {
            this.followRedirects = follow;
            return this;
        }

        public HttpRequest build() {
            if (url == null || url.isEmpty())
                throw new IllegalStateException("URL is required");
            return new HttpRequest(this);
        }
    }
}

// Usage — fluent API
HttpRequest request = new HttpRequest.Builder("https://api.example.com/users", "POST")
    .header("Content-Type", "application/json")
    .header("Authorization", "Bearer token123")
    .body("{\"name\":\"Kiran\"}")
    .timeout(5000)
    .followRedirects(false)
    .build();
```

---

## 14. JAVA INTERNALS & JVM

---

### Q41. How does the Java ClassLoader work?

**Answer:**

The ClassLoader is responsible for **loading `.class` files** into the JVM at runtime.

```
ClassLoader Hierarchy (Parent Delegation Model):

Bootstrap ClassLoader (C++ — loads JDK core: java.lang, java.util)
        ↑ parent
Extension/Platform ClassLoader (loads JDK extensions: javax.*)
        ↑ parent
Application ClassLoader (loads your app classes from classpath)
        ↑ parent
Custom ClassLoader (user-defined)
```

**Parent Delegation Model:**
1. ClassLoader receives a load request
2. Delegates to parent ClassLoader first
3. If parent can't find it, child loads it
4. Prevents malicious override of core classes

```java
// Getting ClassLoader info
System.out.println(String.class.getClassLoader());      // null (bootstrap)
System.out.println(Main.class.getClassLoader());         // AppClassLoader

// Custom ClassLoader
class CustomClassLoader extends ClassLoader {
    @Override
    protected Class<?> findClass(String name) throws ClassNotFoundException {
        byte[] classData = loadClassData(name); // load from custom source
        return defineClass(name, classData, 0, classData.length);
    }

    private byte[] loadClassData(String name) {
        // Load from database, network, encrypted file, etc.
        return new byte[]{};
    }
}
```

---

### Q42. What is reflection in Java?

**Answer:**

Reflection allows a program to **examine and modify its own structure at runtime** — classes, methods, fields, constructors.

```java
import java.lang.reflect.*;

class Person {
    private String name;
    public int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    private String getSecret() {
        return "Secret: " + name;
    }

    public String greet(String greeting) {
        return greeting + ", I'm " + name;
    }
}

// Get Class object
Class<?> clazz = Person.class;
Class<?> clazz2 = Class.forName("com.example.Person"); // dynamic loading

// Inspect class
clazz.getName();             // "com.example.Person"
clazz.getSimpleName();       // "Person"
clazz.getSuperclass();       // Object.class

// Get and use constructors
Constructor<?> ctor = clazz.getConstructor(String.class, int.class);
Person p = (Person) ctor.newInstance("Kiran", 25);

// Get and access fields (including private)
Field nameField = clazz.getDeclaredField("name");
nameField.setAccessible(true);                  // bypass private
nameField.get(p);                               // "Kiran"
nameField.set(p, "Raj");                        // change private field

// Get and invoke methods (including private)
Method secretMethod = clazz.getDeclaredMethod("getSecret");
secretMethod.setAccessible(true);
String secret = (String) secretMethod.invoke(p);

Method greetMethod = clazz.getMethod("greet", String.class);
String result = (String) greetMethod.invoke(p, "Hello");

// Get all methods / fields
Field[] fields = clazz.getDeclaredFields();
Method[] methods = clazz.getDeclaredMethods();

// Annotations
Annotation[] annotations = clazz.getAnnotations();

// Performance note: reflection is 10-100x slower than direct calls
// Use it for: frameworks, testing, serialization — not hot code paths
```

---

### Q43. What are annotations in Java?

**Answer:**

Annotations provide **metadata** about code — used by the compiler, frameworks, or at runtime via reflection.

```java
// Built-in annotations
@Override          // tells compiler this method overrides a parent method
@Deprecated        // marks as outdated; compiler warns on usage
@SuppressWarnings("unchecked")  // suppress specific compiler warnings
@FunctionalInterface  // marks interface as having exactly one abstract method
@SafeVarargs       // suppress heap pollution warnings

// Meta-annotations (annotations on annotations)
@Target(ElementType.METHOD)     // where annotation can be used
@Retention(RetentionPolicy.RUNTIME) // how long annotation is kept
@Documented                     // included in JavaDoc
@Inherited                      // subclass inherits parent's annotation
@Repeatable(Schedules.class)    // can be applied multiple times

// Custom annotation
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.METHOD, ElementType.TYPE})
public @interface Log {
    String level() default "INFO";
    String message() default "";
}

// Using custom annotation
@Log(level = "DEBUG", message = "User service method")
public User findUser(int id) {
    return repository.findById(id);
}

// Processing annotation at runtime via reflection
for (Method method : clazz.getDeclaredMethods()) {
    if (method.isAnnotationPresent(Log.class)) {
        Log log = method.getAnnotation(Log.class);
        System.out.println("Method " + method.getName() +
            " has @Log with level=" + log.level());
    }
}
```

---

## 15. QUICK REFERENCE: TOP 60 INTERVIEW Q&A

---

| # | Question | Short Answer |
|---|---|---|
| 1 | What is Java? | High-level, OOP, platform-independent language (WORA) |
| 2 | JDK vs JRE vs JVM | JVM runs bytecode; JRE = JVM + libraries; JDK = JRE + dev tools |
| 3 | `==` vs `.equals()` | `==` compares references; `.equals()` compares content |
| 4 | What is OOP? | Encapsulation, Inheritance, Polymorphism, Abstraction |
| 5 | Abstract class vs Interface | Abstract: shared code + single inheritance; Interface: contract + multiple |
| 6 | Overloading vs Overriding | Overload: same name, diff params, same class; Override: same signature, subclass |
| 7 | `final` keyword | final var = constant; final method = not overridable; final class = not extendable |
| 8 | `static` keyword | Belongs to class, not instance; shared by all objects |
| 9 | `this` vs `super` | `this` = current object; `super` = parent class |
| 10 | Access modifiers | private < default < protected < public |
| 11 | Checked vs Unchecked exceptions | Checked: must handle at compile time; Unchecked: runtime, optional |
| 12 | `throw` vs `throws` | `throw` creates exception instance; `throws` declares method may throw |
| 13 | `try-with-resources` | Auto-closes resources implementing AutoCloseable |
| 14 | What is `finally`? | Block that always executes after try/catch |
| 15 | Stack vs Heap | Stack: local vars + primitives (per thread); Heap: objects (shared) |
| 16 | Garbage Collection | JVM auto-frees unreachable objects from heap |
| 17 | String immutability | String objects cannot be changed after creation |
| 18 | String vs StringBuilder | String: immutable; StringBuilder: mutable, faster for concatenation |
| 19 | String pool | Cache of string literals in heap to save memory |
| 20 | ArrayList vs LinkedList | ArrayList: fast read O(1); LinkedList: fast insert/delete at head |
| 21 | HashMap vs TreeMap | HashMap: O(1), no order; TreeMap: O(log n), sorted |
| 22 | HashSet vs TreeSet | HashSet: O(1), no order; TreeSet: O(log n), sorted |
| 23 | Iterator vs ListIterator | ListIterator: bidirectional, can add/set; Iterator: forward only |
| 24 | `Comparable` vs `Comparator` | Comparable: natural order in class; Comparator: external custom order |
| 25 | `Collections` vs `Collection` | Collection: interface; Collections: utility class (sort, shuffle, etc.) |
| 26 | What is autoboxing? | Auto conversion: primitive ↔ wrapper (int ↔ Integer) |
| 27 | What is `volatile`? | Ensures visibility of changes to variables across threads |
| 28 | `synchronized` keyword | Prevents concurrent access to a block/method; mutual exclusion |
| 29 | `wait()` vs `sleep()` | `wait()` releases lock; `sleep()` keeps lock |
| 30 | Thread lifecycle | NEW → RUNNABLE → BLOCKED/WAITING → TIMED_WAITING → TERMINATED |
| 31 | `Runnable` vs `Thread` | Runnable preferred; allows extending another class |
| 32 | ExecutorService | Thread pool management; submit tasks, get Futures |
| 33 | Lambda expression | Concise anonymous function; implements functional interface |
| 34 | Stream API | Functional pipeline for processing collections |
| 35 | `Optional` | Container to handle null safely; avoids NullPointerException |
| 36 | Default interface method | Method with body in interface; won't break existing implementations |
| 37 | What are generics? | Type-safe, reusable code with type parameters |
| 38 | Type erasure | Generics are removed at compile time; runtime only sees raw types |
| 39 | Wildcard `?` | Unknown type: `? extends T` (upper), `? super T` (lower) |
| 40 | Serialization | Convert object to byte stream; class must implement Serializable |
| 41 | `transient` keyword | Field excluded from serialization |
| 42 | `serialVersionUID` | Version ID; used to verify compatibility during deserialization |
| 43 | Reflection | Inspect/modify class structure at runtime |
| 44 | Annotations | Metadata added to code; processed by compiler or at runtime |
| 45 | Singleton pattern | Ensures one instance; use enum or double-checked locking |
| 46 | Factory pattern | Creates objects without exposing instantiation logic |
| 47 | Builder pattern | Construct complex objects step by step |
| 48 | ClassLoader | Loads .class files; follows parent delegation model |
| 49 | JIT compiler | Compiles hot bytecode to native machine code at runtime |
| 50 | `instanceof` operator | Checks if object is instance of a class/interface |
| 51 | Covariant return type | Overriding method can return subtype of parent's return type |
| 52 | `var` keyword (Java 10+) | Local variable type inference: `var list = new ArrayList<>()` |
| 53 | Records (Java 16+) | Immutable data classes: `record Point(int x, int y) {}` |
| 54 | Sealed classes (Java 17+) | Restrict which classes can extend/implement |
| 55 | Pattern matching `instanceof` | `if (obj instanceof String s) { s.length(); }` |
| 56 | Text blocks (Java 15+) | Multi-line string literals with `"""` |
| 57 | `ConcurrentHashMap` | Thread-safe HashMap; better than synchronized HashMap |
| 58 | `CountDownLatch` | Let threads wait until a count reaches zero |
| 59 | `CyclicBarrier` | Threads wait until all reach a barrier point |
| 60 | `ReentrantLock` | Explicit lock with more control than synchronized |

---

### Bonus: Java 14+ Records

```java
// Record — concise immutable data class
// auto-generates: constructor, getters, equals, hashCode, toString
public record Point(int x, int y) {}

Point p = new Point(3, 4);
p.x();     // 3 (accessor, not getX())
p.y();     // 4
System.out.println(p); // Point[x=3, y=4]

// Compact constructor (validation)
public record Range(int min, int max) {
    Range {
        if (min > max) throw new IllegalArgumentException("min > max");
    }
}
```

### Bonus: Java 17+ Sealed Classes

```java
// Restrict which classes can extend Shape
public sealed class Shape permits Circle, Rectangle, Triangle {}

public final class Circle extends Shape {
    public double radius;
}

public final class Rectangle extends Shape {
    public double width, height;
}

public non-sealed class Triangle extends Shape {
    // non-sealed — can be extended freely
}

// Pattern matching with sealed classes
double area = switch (shape) {
    case Circle c    -> Math.PI * c.radius * c.radius;
    case Rectangle r -> r.width * r.height;
    case Triangle t  -> 0.5 * t.base * t.height;
    // No default needed — compiler knows all cases are covered!
};
```

### Bonus: `Comparable` vs `Comparator`

```java
// Comparable — natural ordering; class implements it
class Student implements Comparable<Student> {
    String name;
    double gpa;

    @Override
    public int compareTo(Student other) {
        return Double.compare(this.gpa, other.gpa); // sort by GPA ascending
    }
}

List<Student> students = new ArrayList<>();
Collections.sort(students); // uses compareTo

// Comparator — external custom ordering
Comparator<Student> byName = Comparator.comparing(s -> s.name);
Comparator<Student> byGpaDesc = Comparator.comparingDouble(Student::getGpa).reversed();
Comparator<Student> complex = Comparator.comparingDouble(Student::getGpa)
                                         .reversed()
                                         .thenComparing(Student::getName);

students.sort(byName);
students.sort(byGpaDesc);
students.stream().sorted(complex).collect(Collectors.toList());
```

---

*Good luck with your Java interviews!*
