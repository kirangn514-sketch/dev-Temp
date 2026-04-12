# TypeScript Interview Topics: Basic to Advanced
### Questions & Answers with Detailed Explanations

---

## TABLE OF CONTENTS

1. [Basic Level](#1-basic-level)
2. [Types & Type System](#2-types--type-system)
3. [Functions in TypeScript](#3-functions-in-typescript)
4. [Interfaces & Type Aliases](#4-interfaces--type-aliases)
5. [Classes in TypeScript](#5-classes-in-typescript)
6. [Generics](#6-generics)
7. [Advanced Types](#7-advanced-types)
8. [Modules & Namespaces](#8-modules--namespaces)
9. [Decorators](#9-decorators)
10. [TypeScript with DOM & React](#10-typescript-with-dom--react)
11. [TypeScript Configuration](#11-typescript-configuration)
12. [Advanced Patterns](#12-advanced-patterns)
13. [Quick Reference: Top 50 Interview Q&A](#13-quick-reference-top-50-interview-qa)

---

## 1. BASIC LEVEL

---

### Q1. What is TypeScript? How is it different from JavaScript?

**Answer:**

TypeScript is a **strongly typed, open-source superset of JavaScript** developed by Microsoft. It adds optional static typing and advanced object-oriented features to JavaScript. TypeScript code is **transpiled** (compiled) into plain JavaScript before execution — browsers and Node.js cannot run TypeScript directly.

**Key Differences:**

| Feature | JavaScript | TypeScript |
|---|---|---|
| Typing | Dynamic (runtime) | Static (compile-time) |
| Type errors | Found at runtime | Found at compile-time |
| Interfaces | No | Yes |
| Generics | No | Yes |
| Access modifiers | No | Yes (`public`, `private`, `protected`) |
| Enums | No | Yes |
| Decorators | Stage 3 proposal | Supported |
| Tooling/IDE support | Limited | Excellent |
| Learning curve | Lower | Higher |

```ts
// JavaScript — error discovered at runtime
function add(a, b) {
  return a + b;
}
add("5", 3); // "53" — wrong but no warning

// TypeScript — error caught at compile time
function add(a: number, b: number): number {
  return a + b;
}
add("5", 3); // Error: Argument of type 'string' is not assignable to parameter of type 'number'
```

---

### Q2. What are the benefits of using TypeScript?

**Answer:**

1. **Early error detection** — Type errors are caught during development, not in production
2. **Better IDE support** — Autocompletion, IntelliSense, refactoring tools
3. **Self-documenting code** — Types serve as inline documentation
4. **Safer refactoring** — TypeScript alerts you to all affected places
5. **Improved collaboration** — Clearer contracts between functions and modules
6. **Gradual adoption** — You can add TypeScript incrementally to JavaScript projects
7. **Modern JavaScript features** — TypeScript supports latest ECMAScript features with backward-compatible compilation

---

### Q3. How do you install and set up TypeScript?

**Answer:**

```bash
# Install TypeScript globally
npm install -g typescript

# Check version
tsc --version

# Initialize a TypeScript project (creates tsconfig.json)
tsc --init

# Compile a TypeScript file
tsc app.ts

# Compile in watch mode
tsc --watch

# Install locally (recommended)
npm install --save-dev typescript
npx tsc --init
```

**tsconfig.json (basic setup):**
```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "commonjs",
    "strict": true,
    "outDir": "./dist",
    "rootDir": "./src",
    "esModuleInterop": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules"]
}
```

---

### Q4. What is type inference in TypeScript?

**Answer:**

TypeScript can **automatically determine the type** of a variable based on its assigned value — you don't always have to annotate types explicitly.

```ts
// Type inference — TypeScript infers the type
let name = "Kiran";         // inferred as: string
let age = 25;               // inferred as: number
let isActive = true;        // inferred as: boolean
let scores = [90, 85, 92];  // inferred as: number[]

// TypeScript knows name is a string, so this gives an error:
name = 42; // Error: Type 'number' is not assignable to type 'string'

// Inference in functions
function multiply(a: number, b: number) {
  return a * b; // return type inferred as: number
}

// Inference with objects
const user = {
  name: "Kiran",
  age: 25
};
// Inferred as: { name: string; age: number }

user.name = 100; // Error: Type 'number' is not assignable to type 'string'
```

**When to annotate explicitly:**
- Function parameters (TypeScript does NOT infer these)
- When the inferred type is too broad
- For complex objects or return types for clarity

---

### Q5. What is the `any` type? Why should you avoid it?

**Answer:**

`any` is a special type that **disables type checking** for that variable. It allows assignment of any value and any operation on it.

```ts
let value: any = "hello";
value = 42;          // OK
value = true;        // OK
value = { x: 1 };   // OK
value.foo.bar.baz;   // OK at compile time — but may crash at runtime!
value();             // OK at compile time — but may crash at runtime!
```

**Why avoid it:**
- Defeats the purpose of TypeScript
- Errors won't be caught at compile time
- Loses IDE autocompletion and refactoring support
- Acts like a virus: `any` spreads to related types

**Better alternatives:**
```ts
// Use unknown — must narrow before using
let input: unknown = getUserInput();
if (typeof input === "string") {
  console.log(input.toUpperCase()); // safe — narrowed to string
}

// Use union types
let id: string | number;

// Use generics
function identity<T>(value: T): T {
  return value;
}
```

---

### Q6. What is the difference between `unknown` and `any`?

**Answer:**

Both `unknown` and `any` can hold any value, but they differ in how you can USE that value.

```ts
// any — no restrictions; TypeScript trusts you
let a: any = "hello";
a.toUpperCase();   // OK — no error
a();               // OK — no error (dangerous!)
let b: string = a; // OK — no error

// unknown — safe version of any; must narrow type before use
let x: unknown = "hello";
x.toUpperCase();   // Error — cannot call method on unknown
x();               // Error — cannot call unknown as function
let y: string = x; // Error — cannot assign unknown to string

// Must use type narrowing first:
if (typeof x === "string") {
  x.toUpperCase(); // OK — narrowed to string
}
```

**Rule:** Use `unknown` instead of `any` when you don't know the type upfront. It forces you to check the type before using it.

---

### Q7. What are TypeScript's primitive types?

**Answer:**

```ts
// number — all numeric values (integers and floats)
let age: number = 25;
let price: number = 99.99;
let hex: number = 0xff;
let binary: number = 0b1010;

// string
let name: string = "Kiran";
let greeting: string = `Hello, ${name}`;

// boolean
let isLoggedIn: boolean = true;

// null — intentional absence
let data: null = null;

// undefined — uninitialized value
let result: undefined = undefined;

// symbol — unique identifier
let id: symbol = Symbol("id");

// bigint — integers larger than Number.MAX_SAFE_INTEGER
let bigNumber: bigint = 9007199254740991n;

// void — used for functions that return nothing
function log(msg: string): void {
  console.log(msg);
}

// never — function that never returns (throws or infinite loop)
function throwError(msg: string): never {
  throw new Error(msg);
}
```

---

## 2. TYPES & TYPE SYSTEM

---

### Q8. What are union types and intersection types?

**Answer:**

**Union Types (`|`)** — A value can be ONE OF several types

```ts
// Union type — string OR number
let id: string | number;
id = "abc123"; // OK
id = 42;       // OK
id = true;     // Error

// Union in function
function printId(id: string | number): void {
  if (typeof id === "string") {
    console.log(id.toUpperCase()); // narrowed to string
  } else {
    console.log(id.toFixed(2)); // narrowed to number
  }
}

// Union with objects
type ApiResponse = SuccessResponse | ErrorResponse;
```

**Intersection Types (`&`)** — A value must satisfy ALL types simultaneously

```ts
type Employee = {
  name: string;
  company: string;
};

type Developer = {
  skills: string[];
  level: "junior" | "senior";
};

// Intersection — must have ALL properties from both
type TechEmployee = Employee & Developer;

const dev: TechEmployee = {
  name: "Kiran",
  company: "TechCorp",
  skills: ["TypeScript", "React"],
  level: "senior"
};
```

---

### Q9. What are literal types in TypeScript?

**Answer:**

Literal types allow you to specify **exact values** a variable can hold, not just a general type.

```ts
// String literal type
let direction: "north" | "south" | "east" | "west";
direction = "north"; // OK
direction = "up";    // Error

// Number literal type
type DiceRoll = 1 | 2 | 3 | 4 | 5 | 6;
let roll: DiceRoll = 4; // OK
let bad: DiceRoll = 7;  // Error

// Boolean literal type (less common)
type AlwaysTrue = true;

// Useful in function signatures
function setAlignment(align: "left" | "center" | "right"): void {
  // ...
}

// Template literal types (TypeScript 4.1+)
type EventName = "click" | "focus" | "blur";
type Handler = `on${Capitalize<EventName>}`;
// Handler = "onClick" | "onFocus" | "onBlur"

// Practical example
type HttpMethod = "GET" | "POST" | "PUT" | "DELETE" | "PATCH";
type StatusCode = 200 | 201 | 400 | 401 | 403 | 404 | 500;
```

---

### Q10. What is type narrowing? What are the different ways to narrow types?

**Answer:**

Type narrowing is the process of **refining a broad type to a more specific type** within a conditional block. TypeScript automatically tracks these refinements.

```ts
// 1. typeof narrowing
function process(value: string | number) {
  if (typeof value === "string") {
    return value.toUpperCase(); // TypeScript knows it's string here
  }
  return value.toFixed(2);     // TypeScript knows it's number here
}

// 2. instanceof narrowing
function formatDate(date: Date | string) {
  if (date instanceof Date) {
    return date.toISOString();  // narrowed to Date
  }
  return new Date(date).toISOString(); // narrowed to string
}

// 3. in operator narrowing
type Fish = { swim: () => void };
type Bird = { fly: () => void };

function move(animal: Fish | Bird) {
  if ("swim" in animal) {
    animal.swim(); // narrowed to Fish
  } else {
    animal.fly();  // narrowed to Bird
  }
}

// 4. Truthiness narrowing
function printLength(value: string | null | undefined) {
  if (value) {
    console.log(value.length); // narrowed to string (non-null/undefined)
  }
}

// 5. Equality narrowing
function check(x: string | number, y: string | boolean) {
  if (x === y) {
    // Both must be string (the only common type)
    x.toUpperCase();
    y.toUpperCase();
  }
}

// 6. Discriminated union narrowing
type Shape =
  | { kind: "circle"; radius: number }
  | { kind: "square"; side: number }
  | { kind: "rectangle"; width: number; height: number };

function area(shape: Shape): number {
  switch (shape.kind) {
    case "circle":
      return Math.PI * shape.radius ** 2;
    case "square":
      return shape.side ** 2;
    case "rectangle":
      return shape.width * shape.height;
  }
}

// 7. Type predicates (user-defined type guards)
function isString(value: unknown): value is string {
  return typeof value === "string";
}

if (isString(someValue)) {
  someValue.toUpperCase(); // narrowed to string
}
```

---

### Q11. What are enums in TypeScript?

**Answer:**

Enums define a set of **named constants**. They make code more readable by replacing magic numbers or strings.

```ts
// Numeric enum (default — auto-increments from 0)
enum Direction {
  Up,     // 0
  Down,   // 1
  Left,   // 2
  Right   // 3
}
let dir: Direction = Direction.Up; // 0
console.log(Direction[0]); // "Up" — reverse mapping

// Custom starting value
enum StatusCode {
  OK = 200,
  Created = 201,
  BadRequest = 400,
  Unauthorized = 401,
  NotFound = 404
}

// String enum (no reverse mapping — preferred for readability)
enum Role {
  Admin = "ADMIN",
  User = "USER",
  Guest = "GUEST"
}
let role: Role = Role.Admin; // "ADMIN"

// Const enum — inlined at compile time (no runtime object generated)
const enum Color {
  Red = "RED",
  Green = "GREEN",
  Blue = "BLUE"
}
// Compiled to: let c = "RED"; (not Color.Red)
let c: Color = Color.Red;

// Heterogeneous enum (avoid — mix of number and string)
enum Mixed {
  Yes = 1,
  No = "NO"
}
```

**Enum vs Union of Literals:**
```ts
// Enum approach
enum Direction { Up, Down, Left, Right }

// Union literal approach (often preferred in modern TS)
type Direction = "Up" | "Down" | "Left" | "Right";
```

Union literals are often preferred because they don't generate runtime code and are simpler.

---

### Q12. What is the `never` type?

**Answer:**

`never` represents values that **never occur**. It is the return type of functions that always throw or loop infinitely, and appears in exhaustive type checks.

```ts
// Function that always throws
function fail(message: string): never {
  throw new Error(message);
}

// Function with infinite loop
function infiniteLoop(): never {
  while (true) {}
}

// Exhaustive check with never
type Shape = "circle" | "square" | "triangle";

function getArea(shape: Shape): number {
  switch (shape) {
    case "circle": return Math.PI * 5 ** 2;
    case "square": return 5 ** 2;
    case "triangle": return 0.5 * 5 * 5;
    default:
      // If all cases are handled, shape is 'never' here
      // If you add a new Shape without handling it, TypeScript errors here
      const exhaustiveCheck: never = shape;
      throw new Error(`Unhandled shape: ${exhaustiveCheck}`);
  }
}
```

**never vs void:**
- `void` — function returns, but with no value
- `never` — function does NOT return at all

---

### Q13. What is a tuple in TypeScript?

**Answer:**

A tuple is a **fixed-length array where each position has a known type**.

```ts
// Basic tuple
let point: [number, number] = [10, 20];

// Named tuple (TypeScript 4.0+)
let person: [name: string, age: number] = ["Kiran", 25];

// Accessing tuple elements
let [x, y] = point;
console.log(x); // 10
console.log(y); // 20

// Tuple with optional element
type Coordinate = [number, number, number?];
let pos2D: Coordinate = [0, 0];
let pos3D: Coordinate = [0, 0, 5];

// Rest element in tuples
type StringsAndNumber = [string, ...string[], number];
let data: StringsAndNumber = ["a", "b", "c", 42];

// Use case: function return types
function getUser(): [string, number, boolean] {
  return ["Kiran", 25, true];
}
const [name, age, isAdmin] = getUser();

// Use case: useState-like hook
function useState<T>(initial: T): [T, (value: T) => void] {
  let state = initial;
  const setState = (value: T) => { state = value; };
  return [state, setState];
}
```

---

## 3. FUNCTIONS IN TYPESCRIPT

---

### Q14. How are functions typed in TypeScript?

**Answer:**

```ts
// Basic typed function
function add(a: number, b: number): number {
  return a + b;
}

// Arrow function with type annotation
const multiply = (a: number, b: number): number => a * b;

// Optional parameters (must come after required)
function greet(name: string, greeting?: string): string {
  return `${greeting ?? "Hello"}, ${name}!`;
}

// Default parameters
function createUser(name: string, role: string = "user"): object {
  return { name, role };
}

// Rest parameters
function sum(...numbers: number[]): number {
  return numbers.reduce((a, b) => a + b, 0);
}

// Function type as variable
let operation: (a: number, b: number) => number;
operation = add;
operation = multiply;

// Function type alias
type MathOperation = (a: number, b: number) => number;
const divide: MathOperation = (a, b) => a / b;

// Void return type
function log(message: string): void {
  console.log(message);
}

// Callback typing
function fetchData(url: string, callback: (error: Error | null, data?: string) => void): void {
  // ...
}
```

---

### Q15. What are function overloads in TypeScript?

**Answer:**

Function overloads allow a function to accept **different parameter types and return different types** based on the arguments provided.

```ts
// Overload signatures (no implementation)
function format(value: string): string;
function format(value: number): string;
function format(value: Date): string;

// Implementation signature (must be compatible with all overloads)
function format(value: string | number | Date): string {
  if (typeof value === "string") {
    return value.trim();
  }
  if (typeof value === "number") {
    return value.toLocaleString();
  }
  return value.toISOString();
}

// TypeScript uses the correct return type based on argument
const s = format("  hello  "); // string
const n = format(12345);       // string
const d = format(new Date());  // string

// More practical example
function createElement(tag: "input"): HTMLInputElement;
function createElement(tag: "div"): HTMLDivElement;
function createElement(tag: "span"): HTMLSpanElement;
function createElement(tag: string): HTMLElement {
  return document.createElement(tag);
}

const input = createElement("input");   // HTMLInputElement
input.value = "hello"; // no error!
```

---

### Q16. What are type predicates?

**Answer:**

A type predicate is a function whose **return type is `parameterName is Type`**, acting as a custom type guard.

```ts
// Without type predicate — TypeScript doesn't know the narrowed type
function isString(value: unknown) {
  return typeof value === "string";
}
// Return type is: boolean

// With type predicate — TypeScript narrows the type
function isString(value: unknown): value is string {
  return typeof value === "string";
}

let val: unknown = "hello";
if (isString(val)) {
  val.toUpperCase(); // OK — narrowed to string
}

// Practical example with custom types
interface Cat { meow(): void; }
interface Dog { bark(): void; }

function isCat(animal: Cat | Dog): animal is Cat {
  return (animal as Cat).meow !== undefined;
}

function makeSound(animal: Cat | Dog) {
  if (isCat(animal)) {
    animal.meow(); // narrowed to Cat
  } else {
    animal.bark(); // narrowed to Dog
  }
}

// Array filter with type predicate
const mixed: (string | null | undefined)[] = ["a", null, "b", undefined, "c"];
const strings: string[] = mixed.filter((x): x is string => x !== null && x !== undefined);
// strings is correctly typed as string[]
```

---

## 4. INTERFACES & TYPE ALIASES

---

### Q17. What is an interface in TypeScript?

**Answer:**

An interface defines the **shape (contract)** of an object — the properties and methods it must have.

```ts
// Basic interface
interface User {
  id: number;
  name: string;
  email: string;
  age?: number;        // optional property
  readonly createdAt: Date; // cannot be changed after assignment
}

const user: User = {
  id: 1,
  name: "Kiran",
  email: "kiran@example.com",
  createdAt: new Date()
};

user.createdAt = new Date(); // Error: Cannot assign to 'createdAt' — readonly

// Interface with methods
interface Animal {
  name: string;
  speak(): string;
  move(distance?: number): void;
}

// Interface for function type
interface MathFn {
  (a: number, b: number): number;
}
const add: MathFn = (a, b) => a + b;

// Interface for indexable types
interface StringArray {
  [index: number]: string;
}
const arr: StringArray = ["a", "b", "c"];

// Interface extending another interface
interface Admin extends User {
  permissions: string[];
  accessLevel: 1 | 2 | 3;
}

// Interface extending multiple interfaces
interface SuperAdmin extends Admin, Auditable {
  masterKey: string;
}
```

---

### Q18. What is the difference between `interface` and `type`?

**Answer:**

Both define types for objects, but they have key differences:

```ts
// --- INTERFACE ---
interface Point {
  x: number;
  y: number;
}

// Declaration merging — interfaces can be declared multiple times and merged
interface Point {
  z?: number; // merged into the original Point
}
// Now Point = { x: number; y: number; z?: number }

// --- TYPE ALIAS ---
type Point = {
  x: number;
  y: number;
};
// Cannot re-declare — Error: Duplicate identifier 'Point'

// --- EXTENSION ---
// Interface extends interface
interface Animal { name: string; }
interface Dog extends Animal { breed: string; }

// Type uses intersection
type Animal = { name: string };
type Dog = Animal & { breed: string };

// --- WHAT ONLY TYPE CAN DO ---
// Union types
type ID = string | number;

// Tuple types
type Pair = [string, number];

// Primitive aliases
type Name = string;

// Computed types (mapped, conditional)
type Nullable<T> = T | null;

// --- WHAT ONLY INTERFACE CAN DO ---
// Declaration merging (module augmentation)
interface Window {
  myCustomProperty: string;
}
```

**When to use which:**
- Use `interface` for **object shapes and class contracts** — especially when you expect extension or declaration merging
- Use `type` for **unions, tuples, primitives, and computed types**
- In most practical cases they are interchangeable — be consistent within a project

---

### Q19. What is declaration merging?

**Answer:**

Declaration merging allows multiple declarations of the same interface to be **automatically combined** into a single definition.

```ts
// Original interface
interface Config {
  host: string;
  port: number;
}

// Extended in another file or later in the same file
interface Config {
  timeout: number;
  retries?: number;
}

// Result: Config has all four properties
const config: Config = {
  host: "localhost",
  port: 3000,
  timeout: 5000
};

// Practical use: extending third-party library types
// e.g., adding a property to Express Request
declare namespace Express {
  interface Request {
    user?: User;
  }
}

// Or extending Window in a browser
interface Window {
  analytics: AnalyticsLibrary;
}
```

---

## 5. CLASSES IN TYPESCRIPT

---

### Q20. How do classes work in TypeScript?

**Answer:**

TypeScript enhances JavaScript classes with **access modifiers, readonly, abstract classes, and more**.

```ts
class Person {
  // Access modifiers
  public name: string;       // accessible everywhere (default)
  private age: number;       // only within this class
  protected email: string;   // this class and subclasses
  readonly id: number;       // cannot be changed after initialization

  // Constructor with shorthand (parameter properties)
  constructor(
    public firstName: string,
    private lastName: string,
    protected birthYear: number
  ) {
    this.name = `${firstName} ${lastName}`;
    this.age = new Date().getFullYear() - birthYear;
    this.email = "";
    this.id = Math.random();
  }

  // Method
  greet(): string {
    return `Hi, I'm ${this.name}`;
  }

  // Getter
  get fullName(): string {
    return `${this.firstName} ${this.lastName}`;
  }

  // Setter
  set fullName(value: string) {
    const [first, ...rest] = value.split(" ");
    this.firstName = first;
    this.lastName = rest.join(" ");
  }

  // Static method
  static create(name: string): Person {
    return new Person(name, "", 1990);
  }
}

const person = new Person("Kiran", "Dev", 1998);
person.name;          // OK
person.age;           // Error — private
person.fullName;      // getter
person.fullName = "John Doe"; // setter
```

---

### Q21. What are abstract classes in TypeScript?

**Answer:**

Abstract classes are **base classes that cannot be instantiated directly**. They can contain both abstract methods (must be implemented by subclasses) and concrete methods (with implementation).

```ts
abstract class Shape {
  color: string;

  constructor(color: string) {
    this.color = color;
  }

  // Abstract method — no implementation, subclass MUST implement
  abstract area(): number;
  abstract perimeter(): number;

  // Concrete method — has implementation, can be inherited
  describe(): string {
    return `A ${this.color} shape with area ${this.area().toFixed(2)}`;
  }
}

// Cannot instantiate abstract class directly
// const s = new Shape("red"); // Error

class Circle extends Shape {
  constructor(public radius: number, color: string) {
    super(color);
  }

  area(): number {
    return Math.PI * this.radius ** 2;
  }

  perimeter(): number {
    return 2 * Math.PI * this.radius;
  }
}

class Rectangle extends Shape {
  constructor(public width: number, public height: number, color: string) {
    super(color);
  }

  area(): number {
    return this.width * this.height;
  }

  perimeter(): number {
    return 2 * (this.width + this.height);
  }
}

const c = new Circle(5, "red");
console.log(c.describe()); // "A red shape with area 78.54"
```

---

### Q22. How do classes implement interfaces in TypeScript?

**Answer:**

A class uses the `implements` keyword to declare it satisfies an interface. TypeScript verifies all required members are present.

```ts
interface Serializable {
  serialize(): string;
  deserialize(data: string): void;
}

interface Loggable {
  log(message: string): void;
}

// Implementing multiple interfaces
class UserModel implements Serializable, Loggable {
  private data: Record<string, unknown> = {};

  serialize(): string {
    return JSON.stringify(this.data);
  }

  deserialize(data: string): void {
    this.data = JSON.parse(data);
  }

  log(message: string): void {
    console.log(`[UserModel] ${message}`);
  }
}

// Interface vs Abstract class:
// Interface: defines what a class SHOULD DO (contract)
// Abstract class: defines what a class IS (common base with some shared logic)
```

---

## 6. GENERICS

---

### Q23. What are generics in TypeScript?

**Answer:**

Generics allow you to write **reusable, type-safe code that works with multiple types** without losing type information. They act like type variables.

```ts
// Without generics — loses type info
function identity(value: any): any {
  return value;
}
const result = identity("hello"); // type is 'any', not 'string'

// With generics — preserves type info
function identity<T>(value: T): T {
  return value;
}
const s = identity("hello");   // type is 'string'
const n = identity(42);        // type is 'number'
const b = identity(true);      // type is 'boolean'

// Multiple type parameters
function pair<T, U>(first: T, second: U): [T, U] {
  return [first, second];
}
const p = pair("name", 25); // [string, number]

// Generic arrow function
const wrap = <T>(value: T): { value: T } => ({ value });

// Generic interface
interface ApiResponse<T> {
  data: T;
  status: number;
  message: string;
}

type UserResponse = ApiResponse<User>;
type PostsResponse = ApiResponse<Post[]>;

// Generic class
class Stack<T> {
  private items: T[] = [];

  push(item: T): void {
    this.items.push(item);
  }

  pop(): T | undefined {
    return this.items.pop();
  }

  peek(): T | undefined {
    return this.items[this.items.length - 1];
  }

  get size(): number {
    return this.items.length;
  }
}

const numberStack = new Stack<number>();
numberStack.push(1);
numberStack.push(2);
numberStack.pop(); // 2

const stringStack = new Stack<string>();
stringStack.push("a");
```

---

### Q24. What are generic constraints in TypeScript?

**Answer:**

Generic constraints (**`extends`**) limit what types can be used as generic type arguments.

```ts
// Without constraint — T could be anything
function getLength<T>(value: T): number {
  return value.length; // Error: 'length' doesn't exist on type T
}

// With constraint — T must have a .length property
interface HasLength {
  length: number;
}

function getLength<T extends HasLength>(value: T): number {
  return value.length; // OK
}

getLength("hello");       // OK — string has .length
getLength([1, 2, 3]);     // OK — array has .length
getLength({ length: 5 }); // OK — object has .length
getLength(42);            // Error — number doesn't have .length

// keyof constraint
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

const user = { name: "Kiran", age: 25 };
getProperty(user, "name"); // OK, returns string
getProperty(user, "age");  // OK, returns number
getProperty(user, "phone");// Error — 'phone' doesn't exist in type

// Constrain to primitives
type Primitive = string | number | boolean;
function processPrimitive<T extends Primitive>(value: T): T {
  return value;
}

// Multiple constraints
interface Printable { print(): void; }
interface Serializable { serialize(): string; }

function saveAndPrint<T extends Printable & Serializable>(item: T): void {
  item.print();
  const data = item.serialize();
  // save data...
}
```

---

### Q25. What are utility types in TypeScript?

**Answer:**

TypeScript provides built-in generic utility types for common type transformations.

```ts
interface User {
  id: number;
  name: string;
  email: string;
  age: number;
  role: "admin" | "user";
}

// Partial<T> — makes all properties optional
type PartialUser = Partial<User>;
// { id?: number; name?: string; email?: string; age?: number; role?: ... }

function updateUser(id: number, changes: Partial<User>): User {
  // Only the fields you want to update
}

// Required<T> — makes all properties required (opposite of Partial)
type RequiredUser = Required<PartialUser>;

// Readonly<T> — makes all properties readonly
type ReadonlyUser = Readonly<User>;
const user: ReadonlyUser = { id: 1, name: "Kiran", email: "", age: 25, role: "user" };
user.name = "John"; // Error — readonly

// Pick<T, Keys> — pick specific properties
type UserPreview = Pick<User, "id" | "name">;
// { id: number; name: string }

// Omit<T, Keys> — omit specific properties
type PublicUser = Omit<User, "email" | "age">;
// { id: number; name: string; role: "admin" | "user" }

// Record<Keys, Type> — create object type with specific key/value types
type RolePermissions = Record<"admin" | "user" | "guest", string[]>;
const permissions: RolePermissions = {
  admin: ["read", "write", "delete"],
  user: ["read", "write"],
  guest: ["read"]
};

// Exclude<T, U> — remove types from union
type NumberOrString = string | number | boolean;
type OnlyStringOrNumber = Exclude<NumberOrString, boolean>;
// string | number

// Extract<T, U> — keep types that are assignable to U
type OnlyString = Extract<NumberOrString, string>;
// string

// NonNullable<T> — removes null and undefined
type MaybeString = string | null | undefined;
type DefinitelyString = NonNullable<MaybeString>;
// string

// ReturnType<T> — extract return type of a function
function getUser(): { name: string; age: number } {
  return { name: "Kiran", age: 25 };
}
type UserType = ReturnType<typeof getUser>;
// { name: string; age: number }

// Parameters<T> — extract parameter types as tuple
function add(a: number, b: number): number { return a + b; }
type AddParams = Parameters<typeof add>;
// [a: number, b: number]

// InstanceType<T> — extract instance type of a constructor
class Connection { url: string = ""; }
type ConnectionInstance = InstanceType<typeof Connection>;
// Connection

// Awaited<T> — unwrap Promise types (TypeScript 4.5+)
type ResolvedUser = Awaited<Promise<User>>;
// User
```

---

## 7. ADVANCED TYPES

---

### Q26. What are mapped types in TypeScript?

**Answer:**

Mapped types create new types by **transforming each property** of an existing type.

```ts
// Basic mapped type
type Optional<T> = {
  [K in keyof T]?: T[K];
};
// Same as Partial<T>

type Readonly<T> = {
  readonly [K in keyof T]: T[K];
};

// Mapping with type transformation
type Stringify<T> = {
  [K in keyof T]: string;
};

interface Numbers {
  a: number;
  b: number;
}
type StringNumbers = Stringify<Numbers>;
// { a: string; b: string }

// Adding/removing readonly and optional modifiers
type Mutable<T> = {
  -readonly [K in keyof T]: T[K]; // remove readonly
};

type Required<T> = {
  [K in keyof T]-?: T[K]; // remove optional (-)
};

// Remapping keys with 'as' (TypeScript 4.1+)
type Getters<T> = {
  [K in keyof T as `get${Capitalize<string & K>}`]: () => T[K];
};

interface User {
  name: string;
  age: number;
}
type UserGetters = Getters<User>;
// { getName: () => string; getAge: () => number }

// Filtering properties by type
type OnlyStrings<T> = {
  [K in keyof T as T[K] extends string ? K : never]: T[K];
};

interface Mixed {
  name: string;
  age: number;
  email: string;
  isActive: boolean;
}
type StringOnly = OnlyStrings<Mixed>;
// { name: string; email: string }
```

---

### Q27. What are conditional types in TypeScript?

**Answer:**

Conditional types select a type based on a condition using the `T extends U ? X : Y` syntax.

```ts
// Basic conditional type
type IsString<T> = T extends string ? "yes" : "no";

type A = IsString<string>;  // "yes"
type B = IsString<number>;  // "no"
type C = IsString<"hello">; // "yes" (string literal extends string)

// Practical: extract return type
type ReturnType<T> = T extends (...args: any[]) => infer R ? R : never;

function getUser(): { name: string } { return { name: "Kiran" }; }
type Result = ReturnType<typeof getUser>; // { name: string }

// infer keyword — infer a type from within a conditional
type UnwrapArray<T> = T extends Array<infer Item> ? Item : T;
type A = UnwrapArray<string[]>;  // string
type B = UnwrapArray<number[]>;  // number
type C = UnwrapArray<string>;    // string (not array, returns T itself)

type UnwrapPromise<T> = T extends Promise<infer R> ? UnwrapPromise<R> : T;
type Resolved = UnwrapPromise<Promise<Promise<string>>>; // string

// Distributive conditional types (over unions)
type ToArray<T> = T extends any ? T[] : never;
type StringOrNumberArray = ToArray<string | number>;
// string[] | number[]  (distributed over each member of the union)

// Practical example: filter union types
type NonNullable<T> = T extends null | undefined ? never : T;
type Clean = NonNullable<string | null | undefined | number>;
// string | number
```

---

### Q28. What are template literal types?

**Answer:**

Template literal types (TypeScript 4.1+) let you build string types by **combining string literals and types** using template literal syntax.

```ts
// Basic template literal type
type Greeting = `Hello, ${string}!`;
const g: Greeting = "Hello, Kiran!"; // OK
const bad: Greeting = "Hi, Kiran!";  // Error

// Combining literal unions
type EventPrefix = "click" | "focus" | "blur";
type EventName = `on${Capitalize<EventPrefix>}`;
// "onClick" | "onFocus" | "onBlur"

// Building API route types
type HttpMethod = "GET" | "POST" | "PUT" | "DELETE";
type ApiVersion = "v1" | "v2";
type ApiRoute = `/${ApiVersion}/${string}`;
const route: ApiRoute = "/v1/users"; // OK

// Practical: CSS property generator
type CSSProperty = "margin" | "padding" | "border";
type Direction = "top" | "right" | "bottom" | "left";
type CSSKey = `${CSSProperty}-${Direction}`;
// "margin-top" | "margin-right" | ... | "border-left"

// Type-safe event system
type EventMap = {
  click: MouseEvent;
  keydown: KeyboardEvent;
  submit: SubmitEvent;
};

type EventHandler<T extends keyof EventMap> = (event: EventMap[T]) => void;

// Accessor types
type Getter<T, K extends string & keyof T> = `get${Capitalize<K>}`;
```

---

### Q29. What is the `infer` keyword in TypeScript?

**Answer:**

`infer` is used within conditional types to **extract (infer) a type from another type** and bind it to a variable.

```ts
// Infer return type of a function
type ReturnType<T> = T extends (...args: any[]) => infer R ? R : never;
type Fn = () => string;
type R = ReturnType<Fn>; // string

// Infer element type from array
type ArrayElement<T> = T extends (infer E)[] ? E : never;
type E = ArrayElement<number[]>; // number

// Infer first argument
type FirstArg<T> = T extends (first: infer F, ...rest: any[]) => any ? F : never;
type F = FirstArg<(name: string, age: number) => void>; // string

// Infer Promise resolution value
type Awaited<T> = T extends Promise<infer R> ? Awaited<R> : T;
type Resolved = Awaited<Promise<string>>; // string

// Infer from object structure
type ValueOf<T> = T extends Record<string, infer V> ? V : never;
type Config = { host: string; port: number; debug: boolean };
type ConfigValues = ValueOf<Config>; // string | number | boolean

// Infer constructor parameter type
type ConstructorParam<T> = T extends new (arg: infer P) => any ? P : never;
class Greeter { constructor(public name: string) {} }
type P = ConstructorParam<typeof Greeter>; // string
```

---

## 8. MODULES & NAMESPACES

---

### Q30. How do modules work in TypeScript?

**Answer:**

TypeScript supports ES Modules (ESM) and CommonJS. Any file with a top-level `import` or `export` is a module.

```ts
// math.ts — named exports
export const PI = 3.14159;

export function add(a: number, b: number): number {
  return a + b;
}

export interface MathResult {
  value: number;
  formatted: string;
}

// Default export
export default class Calculator {
  add(a: number, b: number): number { return a + b; }
}

// main.ts — imports
import Calculator, { PI, add, MathResult } from "./math";
import * as MathUtils from "./math";     // namespace import
import { add as mathAdd } from "./math"; // rename import

// Re-export
export { add, PI } from "./math";
export * from "./math";
export { default as Calculator } from "./math";

// Dynamic import (lazy loading)
async function loadMath() {
  const math = await import("./math");
  math.add(1, 2);
}

// Type-only imports (TypeScript 3.8+)
import type { MathResult } from "./math"; // erased at compile time, no runtime code
```

---

### Q31. What are declaration files (`.d.ts`)?

**Answer:**

Declaration files provide **type information for JavaScript code** without including actual implementation. They tell TypeScript the types of external modules.

```ts
// math.d.ts — type declarations only
declare function add(a: number, b: number): number;
declare const PI: number;

declare interface Config {
  host: string;
  port: number;
}

declare class EventEmitter {
  on(event: string, listener: Function): this;
  off(event: string, listener: Function): this;
  emit(event: string, ...args: any[]): boolean;
}

// Module declaration for third-party libraries
declare module "some-library" {
  export function doSomething(value: string): void;
  export default class SomeClass {
    constructor(options: object);
  }
}

// Augmenting existing modules
declare module "express" {
  interface Request {
    user?: { id: number; name: string };
  }
}

// Global declarations
declare global {
  interface Window {
    analytics: { track(event: string): void };
  }

  var __DEV__: boolean;
}
```

**`@types` packages:** Community-maintained type declarations for popular libraries.
```bash
npm install --save-dev @types/node
npm install --save-dev @types/react
npm install --save-dev @types/lodash
```

---

## 9. DECORATORS

---

### Q32. What are decorators in TypeScript?

**Answer:**

Decorators are a **design pattern and TypeScript feature** that let you attach metadata or modify classes, methods, properties, and parameters at design time.

```ts
// Enable in tsconfig.json:
// "experimentalDecorators": true,
// "emitDecoratorMetadata": true

// Class decorator
function Component(options: { selector: string; template: string }) {
  return function <T extends { new (...args: any[]): {} }>(constructor: T) {
    return class extends constructor {
      selector = options.selector;
      template = options.template;
    };
  };
}

@Component({ selector: "app-root", template: "<div>Hello</div>" })
class AppComponent {
  title = "My App";
}

// Method decorator
function Log(target: any, key: string, descriptor: PropertyDescriptor) {
  const original = descriptor.value;
  descriptor.value = function (...args: any[]) {
    console.log(`Calling ${key} with`, args);
    const result = original.apply(this, args);
    console.log(`${key} returned`, result);
    return result;
  };
  return descriptor;
}

class MathService {
  @Log
  add(a: number, b: number): number {
    return a + b;
  }
}

// Property decorator
function Required(target: any, key: string) {
  let value = target[key];
  Object.defineProperty(target, key, {
    get: () => value,
    set: (newValue) => {
      if (!newValue) throw new Error(`${key} is required`);
      value = newValue;
    }
  });
}

// Parameter decorator
function LogParam(target: any, key: string, parameterIndex: number) {
  console.log(`Parameter ${parameterIndex} of ${key} decorated`);
}

class Service {
  greet(@LogParam name: string): void {
    console.log("Hello", name);
  }
}
```

---

## 10. TYPESCRIPT WITH DOM & REACT

---

### Q33. How do you use TypeScript with the DOM?

**Answer:**

```ts
// DOM element type assertions
const input = document.getElementById("name") as HTMLInputElement;
const button = document.querySelector<HTMLButtonElement>(".btn");
const form = document.querySelector<HTMLFormElement>("#myForm");

// Null check before using
if (input) {
  input.value = "Hello"; // safe
}

// Optional chaining
document.getElementById("btn")?.addEventListener("click", handler);

// Event typing
function handleClick(event: MouseEvent): void {
  const target = event.target as HTMLButtonElement;
  console.log(target.textContent);
}

function handleInput(event: Event): void {
  const target = event.target as HTMLInputElement;
  console.log(target.value);
}

document.addEventListener("click", handleClick);
document.addEventListener("keydown", (e: KeyboardEvent) => {
  console.log(e.key);
});

// Creating elements
const div = document.createElement("div"); // HTMLDivElement
const anchor = document.createElement("a"); // HTMLAnchorElement

// NodeList typing
const items = document.querySelectorAll<HTMLLIElement>("li");
items.forEach(item => {
  console.log(item.textContent); // item is HTMLLIElement
});
```

---

### Q34. How do you use TypeScript with React?

**Answer:**

```tsx
import React, { useState, useEffect, useRef, FC, ReactNode } from "react";

// Component props typing
interface ButtonProps {
  label: string;
  onClick: () => void;
  disabled?: boolean;
  variant?: "primary" | "secondary" | "danger";
  children?: ReactNode;
}

// Functional component
const Button: FC<ButtonProps> = ({ label, onClick, disabled = false, variant = "primary" }) => {
  return (
    <button
      className={`btn btn-${variant}`}
      onClick={onClick}
      disabled={disabled}
    >
      {label}
    </button>
  );
};

// useState with type
const [count, setCount] = useState<number>(0);
const [user, setUser] = useState<User | null>(null);
const [items, setItems] = useState<string[]>([]);

// useRef with type
const inputRef = useRef<HTMLInputElement>(null);
const timerRef = useRef<ReturnType<typeof setTimeout> | null>(null);

// useEffect
useEffect(() => {
  const fetchUser = async () => {
    const res = await fetch("/api/user");
    const data: User = await res.json();
    setUser(data);
  };
  fetchUser();
}, []);

// Event handlers
const handleChange = (e: React.ChangeEvent<HTMLInputElement>): void => {
  console.log(e.target.value);
};

const handleSubmit = (e: React.FormEvent<HTMLFormElement>): void => {
  e.preventDefault();
};

const handleClick = (e: React.MouseEvent<HTMLButtonElement>): void => {
  console.log("clicked");
};

// Generic component
interface ListProps<T> {
  items: T[];
  renderItem: (item: T, index: number) => ReactNode;
  keyExtractor: (item: T) => string;
}

function List<T>({ items, renderItem, keyExtractor }: ListProps<T>) {
  return (
    <ul>
      {items.map((item, i) => (
        <li key={keyExtractor(item)}>{renderItem(item, i)}</li>
      ))}
    </ul>
  );
}

// Custom hook
function useFetch<T>(url: string): { data: T | null; loading: boolean; error: string | null } {
  const [data, setData] = useState<T | null>(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    fetch(url)
      .then(res => res.json())
      .then(setData)
      .catch(err => setError(err.message))
      .finally(() => setLoading(false));
  }, [url]);

  return { data, loading, error };
}
```

---

## 11. TYPESCRIPT CONFIGURATION

---

### Q35. What are the important `tsconfig.json` options?

**Answer:**

```json
{
  "compilerOptions": {

    // --- TARGET & OUTPUT ---
    "target": "ES2020",       // JS version to compile to
    "module": "commonjs",     // Module system: commonjs | ESNext | ES2020
    "outDir": "./dist",       // Output directory
    "rootDir": "./src",       // Source directory
    "sourceMap": true,        // Generate .map files for debugging

    // --- STRICT MODE (HIGHLY RECOMMENDED) ---
    "strict": true,           // Enables all strict checks below
    "noImplicitAny": true,    // Error on implicit 'any' types
    "strictNullChecks": true, // null/undefined not assignable to other types
    "strictFunctionTypes": true,
    "strictBindCallApply": true,
    "strictPropertyInitialization": true,

    // --- ADDITIONAL CHECKS ---
    "noUnusedLocals": true,       // Error on unused local variables
    "noUnusedParameters": true,   // Error on unused parameters
    "noImplicitReturns": true,    // All code paths must return a value
    "noFallthroughCasesInSwitch": true,

    // --- MODULE RESOLUTION ---
    "moduleResolution": "node",   // node | bundler | node16
    "baseUrl": "./src",           // Base for non-relative imports
    "paths": {                    // Path aliases
      "@components/*": ["components/*"],
      "@utils/*": ["utils/*"]
    },
    "esModuleInterop": true,      // Allow default imports from CJS modules
    "allowSyntheticDefaultImports": true,
    "resolveJsonModule": true,    // Allow importing .json files

    // --- ADVANCED ---
    "declaration": true,          // Generate .d.ts files
    "declarationMap": true,       // Source maps for .d.ts files
    "incremental": true,          // Faster subsequent builds
    "experimentalDecorators": true,
    "emitDecoratorMetadata": true,
    "skipLibCheck": true,         // Skip type checking .d.ts files
    "lib": ["ES2020", "DOM", "DOM.Iterable"]
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist", "**/*.test.ts"]
}
```

---

## 12. ADVANCED PATTERNS

---

### Q36. What is the Builder Pattern in TypeScript?

**Answer:**

```ts
class QueryBuilder<T> {
  private query: {
    table: string;
    conditions: string[];
    fields: (keyof T)[];
    limitValue?: number;
    orderByField?: keyof T;
  } = { table: "", conditions: [], fields: [] };

  from(table: string): this {
    this.query.table = table;
    return this;
  }

  select(...fields: (keyof T)[]): this {
    this.query.fields = fields;
    return this;
  }

  where(condition: string): this {
    this.query.conditions.push(condition);
    return this;
  }

  limit(n: number): this {
    this.query.limitValue = n;
    return this;
  }

  orderBy(field: keyof T): this {
    this.query.orderByField = field;
    return this;
  }

  build(): string {
    const fields = this.query.fields.length ? this.query.fields.join(", ") : "*";
    let sql = `SELECT ${fields} FROM ${this.query.table}`;
    if (this.query.conditions.length) sql += ` WHERE ${this.query.conditions.join(" AND ")}`;
    if (this.query.orderByField) sql += ` ORDER BY ${String(this.query.orderByField)}`;
    if (this.query.limitValue) sql += ` LIMIT ${this.query.limitValue}`;
    return sql;
  }
}

interface User { id: number; name: string; email: string; age: number; }

const sql = new QueryBuilder<User>()
  .from("users")
  .select("id", "name", "email")
  .where("age > 18")
  .where("isActive = true")
  .orderBy("name")
  .limit(10)
  .build();
// SELECT id, name, email FROM users WHERE age > 18 AND isActive = true ORDER BY name LIMIT 10
```

---

### Q37. What is the `satisfies` operator in TypeScript (4.9+)?

**Answer:**

The `satisfies` operator **validates** that a value matches a type without changing the inferred type of the variable.

```ts
type Colors = "red" | "green" | "blue";
type ColorMap = Record<Colors, string | [number, number, number]>;

// Without satisfies — typed as ColorMap, lose specific types
const palette: ColorMap = {
  red: [255, 0, 0],
  green: "#00ff00",
  blue: [0, 0, 255]
};
palette.green.toUpperCase(); // Error! TypeScript thinks it could be array or string

// With satisfies — validates against ColorMap but keeps specific inferred types
const palette2 = {
  red: [255, 0, 0],
  green: "#00ff00",
  blue: [0, 0, 255]
} satisfies ColorMap;

palette2.green.toUpperCase(); // OK! TypeScript knows this specific value is string
palette2.red[0];              // OK! TypeScript knows this specific value is number[]

// Catch typos while keeping specific types
const config = {
  host: "localhost",
  port: 3000,
  debug: true
} satisfies { host: string; port: number; debug: boolean };
```

---

### Q38. What are discriminated unions and why are they useful?

**Answer:**

A discriminated union is a union type where **each member has a common literal property (the discriminant)** that uniquely identifies it.

```ts
// Each shape has a unique 'kind' — the discriminant
type Circle = {
  kind: "circle";
  radius: number;
};

type Rectangle = {
  kind: "rectangle";
  width: number;
  height: number;
};

type Triangle = {
  kind: "triangle";
  base: number;
  height: number;
};

type Shape = Circle | Rectangle | Triangle;

// TypeScript narrows correctly in each case
function area(shape: Shape): number {
  switch (shape.kind) {
    case "circle":
      return Math.PI * shape.radius ** 2;
      // shape is Circle here — only radius is accessible
    case "rectangle":
      return shape.width * shape.height;
      // shape is Rectangle here
    case "triangle":
      return 0.5 * shape.base * shape.height;
      // shape is Triangle here
    default:
      // Exhaustiveness check
      const _exhaustive: never = shape;
      throw new Error("Unknown shape");
  }
}

// Real-world: Redux-style actions
type Action =
  | { type: "INCREMENT"; payload: number }
  | { type: "DECREMENT"; payload: number }
  | { type: "RESET" }
  | { type: "SET_USER"; payload: User };

function reducer(state: State, action: Action): State {
  switch (action.type) {
    case "INCREMENT":
      return { ...state, count: state.count + action.payload };
    case "RESET":
      return initialState; // no payload needed
    case "SET_USER":
      return { ...state, user: action.payload };
  }
}

// API response pattern
type ApiResult<T> =
  | { status: "success"; data: T }
  | { status: "error"; error: string }
  | { status: "loading" };

function handleResult<T>(result: ApiResult<T>) {
  switch (result.status) {
    case "success":
      console.log(result.data);   // result.data is T
      break;
    case "error":
      console.error(result.error); // result.error is string
      break;
    case "loading":
      console.log("Loading...");   // no data or error accessible
  }
}
```

---

### Q39. What is module augmentation in TypeScript?

**Answer:**

Module augmentation lets you **add new declarations to existing modules** without modifying their source.

```ts
// Augmenting Express to add user to Request
// types/express.d.ts
import "express";

declare module "express" {
  interface Request {
    user?: {
      id: string;
      email: string;
      role: "admin" | "user";
    };
    requestId?: string;
  }
}

// Now in your middleware:
import { Request, Response, NextFunction } from "express";

function authMiddleware(req: Request, res: Response, next: NextFunction) {
  req.user = { id: "123", email: "a@b.com", role: "admin" }; // No error!
  next();
}

// Augmenting a third-party library
declare module "some-library" {
  interface SomeOptions {
    myCustomOption: boolean;
  }
}

// Augmenting global
declare global {
  interface Array<T> {
    last(): T | undefined;
  }
}

Array.prototype.last = function () {
  return this[this.length - 1];
};
```

---

### Q40. What is covariance and contravariance in TypeScript?

**Answer:**

These describe how **type compatibility works with generics and function types** when dealing with subtypes.

```ts
class Animal { name: string = ""; }
class Dog extends Animal { bark() {} }
class Cat extends Animal { meow() {} }

// COVARIANCE — subtype relationship is preserved
// Array<Dog> is assignable to Array<Animal>
let dogs: Dog[] = [new Dog()];
let animals: Animal[] = dogs; // OK — covariant (read-only arrays)

// CONTRAVARIANCE — subtype relationship is reversed for function parameters
// A function accepting Animal is assignable to one accepting Dog
type AnimalHandler = (a: Animal) => void;
type DogHandler = (d: Dog) => void;

const handleAnimal: AnimalHandler = (a) => console.log(a.name);
const handleDog: DogHandler = handleAnimal; // OK — contravariant parameters
// A handler that accepts any Animal can certainly handle a Dog

// BIVARIANCE — TypeScript (by default, pre strictFunctionTypes) allowed both
// With "strictFunctionTypes: true" — method parameters are checked bivariantly
//   function parameters are checked contravariantly (stricter, safer)

// Return types are covariant
type GetAnimal = () => Animal;
type GetDog = () => Dog;

const getDog: GetDog = () => new Dog();
const getAnimal: GetAnimal = getDog; // OK — Dog is assignable to Animal
```

---

## 13. QUICK REFERENCE: TOP 50 INTERVIEW Q&A

---

| # | Question | Short Answer |
|---|---|---|
| 1 | What is TypeScript? | Typed superset of JavaScript compiled to JS |
| 2 | What is `any`? | Disables type checking — avoid it |
| 3 | `any` vs `unknown` | `unknown` requires type narrowing before use; safer |
| 4 | What is type inference? | TypeScript auto-deduces types from values |
| 5 | `interface` vs `type` | `interface` supports merging; `type` supports unions/tuples |
| 6 | What are generics? | Type variables for reusable, type-safe code |
| 7 | What is `never`? | Type for code that never returns (throws/infinite loop) |
| 8 | What is `void`? | Return type of functions that return nothing |
| 9 | `null` vs `undefined` | `null` = intentional absence; `undefined` = uninitialized |
| 10 | What is `readonly`? | Property cannot be reassigned after initialization |
| 11 | What is `Partial<T>`? | Makes all properties optional |
| 12 | What is `Required<T>`? | Makes all properties required |
| 13 | What is `Pick<T,K>`? | Creates type with only selected keys |
| 14 | What is `Omit<T,K>`? | Creates type without specified keys |
| 15 | What is `Record<K,V>`? | Creates object type with given key/value types |
| 16 | What is `Exclude<T,U>`? | Removes U from union T |
| 17 | What is `Extract<T,U>`? | Keeps only members of T assignable to U |
| 18 | What is `NonNullable<T>`? | Removes null and undefined from T |
| 19 | What is `ReturnType<T>`? | Extracts return type of function type T |
| 20 | What is `Parameters<T>`? | Extracts parameter types of function as tuple |
| 21 | What is type narrowing? | Refining a broad type to a specific type using checks |
| 22 | What is a discriminated union? | Union with a shared literal property (discriminant) for narrowing |
| 23 | What are type predicates? | Function returning `value is Type` for type narrowing |
| 24 | What is declaration merging? | Same interface declared multiple times — they merge |
| 25 | What is a tuple? | Fixed-length array where each position has a known type |
| 26 | What is an enum? | Named set of constants (numeric or string) |
| 27 | `const enum` vs `enum` | `const enum` is inlined at compile time; no runtime object |
| 28 | What are mapped types? | Transform each property of a type |
| 29 | What are conditional types? | `T extends U ? X : Y` — choose type based on condition |
| 30 | What is `infer`? | Extract a type from within a conditional type |
| 31 | What are decorators? | Functions that modify classes/methods at design time |
| 32 | What is `strictNullChecks`? | Makes null/undefined separate from all other types |
| 33 | What is `noImplicitAny`? | Errors when TypeScript infers `any` implicitly |
| 34 | What is a `.d.ts` file? | Type declaration file for JavaScript libraries |
| 35 | What is `@types/*`? | NPM packages with type declarations for libraries |
| 36 | What is module augmentation? | Adding declarations to existing modules |
| 37 | What is the `satisfies` operator? | Validates value matches type without changing inferred type |
| 38 | What are template literal types? | Build string types using template literal syntax |
| 39 | What is structural typing? | Types compatible if they have the same shape (duck typing) |
| 40 | Structural vs Nominal typing | TS uses structural; Java/C# use nominal |
| 41 | What is `as const`? | Makes object/array deeply readonly with literal types |
| 42 | What is `keyof`? | Gets union of all keys of a type |
| 43 | What is `typeof` (in types)? | Gets type of a variable or expression |
| 44 | What is indexed access type? | `T[K]` — gets type of property K in type T |
| 45 | What is `abstract class`? | Base class that cannot be instantiated; may have abstract methods |
| 46 | What are access modifiers? | `public`, `private`, `protected` — control class member visibility |
| 47 | What is `override`? | Explicitly marks method as overriding a parent method |
| 48 | What are function overloads? | Multiple signatures for one function with different params/returns |
| 49 | What is `Awaited<T>`? | Unwraps Promise types recursively |
| 50 | What is `as const`? | Narrows to most specific literal types; makes deeply readonly |

---

### Bonus: Important `as const` Examples

```ts
// Without as const — widened types
const directions = ["north", "south", "east", "west"];
// type: string[]

// With as const — tuple of literals, deeply readonly
const directions = ["north", "south", "east", "west"] as const;
// type: readonly ["north", "south", "east", "west"]

type Direction = typeof directions[number];
// type: "north" | "south" | "east" | "west"

// Object as const
const config = {
  host: "localhost",
  port: 3000,
  env: "development"
} as const;
// type: { readonly host: "localhost"; readonly port: 3000; readonly env: "development" }
// No widening — port is literally 3000, not number

// Useful for deriving types from constants
const HTTP_METHODS = ["GET", "POST", "PUT", "DELETE"] as const;
type HttpMethod = typeof HTTP_METHODS[number]; // "GET" | "POST" | "PUT" | "DELETE"
```

---

### Bonus: `keyof` and `typeof` Operators

```ts
// keyof — produces union of keys
interface User {
  id: number;
  name: string;
  email: string;
}

type UserKeys = keyof User; // "id" | "name" | "email"

function getField<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key]; // type-safe access
}

// typeof — get type from a value
const defaultUser = { id: 0, name: "", email: "", role: "user" as const };
type UserShape = typeof defaultUser;
// { id: number; name: string; email: string; role: "user" }

// Combining keyof + typeof
type DefaultUserKeys = keyof typeof defaultUser;
// "id" | "name" | "email" | "role"

// Very useful with enums/constants
const Routes = {
  Home: "/",
  About: "/about",
  Contact: "/contact"
} as const;

type Route = typeof Routes[keyof typeof Routes];
// "/" | "/about" | "/contact"
```

---

*Good luck with your TypeScript interviews!*
