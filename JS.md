# JavaScript Interview Topics: Basic to Advanced

---

## TABLE OF CONTENTS

1. [Basic Level](#1-basic-level)
2. [Intermediate Level](#2-intermediate-level)
3. [Advanced Level](#3-advanced-level)
4. [JavaScript Runtime & Engine](#4-javascript-runtime--engine)
5. [Design Patterns](#5-design-patterns)
6. [Modern JavaScript (ES6+)](#6-modern-javascript-es6)
7. [Testing & Debugging](#7-testing--debugging)

---

## 1. BASIC LEVEL

---

### 1.1 Variables: var, let, const

JavaScript has three ways to declare variables.

**var**
- Function-scoped (not block-scoped)
- Can be re-declared and updated
- Hoisted to the top of its scope with `undefined`

```js
var x = 10;
var x = 20; // allowed
```

**let**
- Block-scoped
- Can be updated but NOT re-declared in the same scope
- Not initialized during hoisting (Temporal Dead Zone)

```js
let y = 10;
// let y = 20; // Error: Cannot re-declare
y = 20; // allowed
```

**const**
- Block-scoped
- Cannot be updated or re-declared
- Must be initialized at declaration
- Objects/arrays declared with const can have their contents mutated

```js
const z = 30;
// z = 40; // Error

const obj = { a: 1 };
obj.a = 2; // allowed — we're mutating, not reassigning
```

**Interview Tip:** Use `const` by default, `let` when reassignment is needed, and avoid `var`.

---

### 1.2 Data Types

JavaScript has 8 data types:

**Primitive Types (immutable, stored by value):**
- `string` — `"hello"`
- `number` — `42`, `3.14`
- `bigint` — `9007199254740991n`
- `boolean` — `true / false`
- `undefined` — variable declared but not assigned
- `null` — intentional absence of value
- `symbol` — unique identifier

**Non-Primitive (stored by reference):**
- `object` — includes arrays, functions, dates, etc.

```js
typeof "hello"     // "string"
typeof 42          // "number"
typeof undefined   // "undefined"
typeof null        // "object"  ← historical bug in JS
typeof []          // "object"
typeof function(){} // "function"
```

---

### 1.3 Type Coercion

JavaScript automatically converts types in certain operations.

**Implicit Coercion:**
```js
"5" + 3     // "53" (number coerced to string)
"5" - 3     // 2   (string coerced to number)
true + 1    // 2
false + 1   // 1
null + 1    // 1
undefined + 1 // NaN
```

**Explicit Coercion:**
```js
Number("5")     // 5
String(5)       // "5"
Boolean(0)      // false
Boolean("")     // false
Boolean("hi")   // true
```

**== vs ===**
- `==` (loose equality) — performs type coercion before comparing
- `===` (strict equality) — no coercion; checks type AND value

```js
0 == false   // true  (coercion)
0 === false  // false (strict)
null == undefined  // true
null === undefined // false
```

---

### 1.4 Functions

**Function Declaration**
```js
function greet(name) {
  return "Hello " + name;
}
```
- Hoisted fully (can be called before declaration)

**Function Expression**
```js
const greet = function(name) {
  return "Hello " + name;
};
```
- Not hoisted

**Arrow Function (ES6)**
```js
const greet = (name) => "Hello " + name;
```
- Shorter syntax
- Does NOT have its own `this`
- Cannot be used as a constructor

**Default Parameters**
```js
function greet(name = "World") {
  return "Hello " + name;
}
```

**Rest Parameters**
```js
function sum(...nums) {
  return nums.reduce((a, b) => a + b, 0);
}
sum(1, 2, 3); // 6
```

---

### 1.5 Scope

**Global Scope** — Variables accessible everywhere  
**Function Scope** — Variables declared inside a function  
**Block Scope** — Variables declared inside `{}` with `let` or `const`

**Scope Chain:**  
When a variable is not found in the current scope, JS looks up to the outer scope, and so on until it reaches the global scope.

```js
let a = "global";

function outer() {
  let b = "outer";

  function inner() {
    let c = "inner";
    console.log(a, b, c); // all accessible
  }
}
```

---

### 1.6 Hoisting

JavaScript moves declarations (not initializations) to the top of their scope during compilation.

```js
console.log(x); // undefined (not error)
var x = 5;

// Functions are fully hoisted:
sayHi(); // works
function sayHi() { console.log("Hi"); }

// let/const are hoisted but NOT initialized (Temporal Dead Zone):
// console.log(y); // ReferenceError
let y = 10;
```

---

### 1.7 Operators

**Arithmetic:** `+`, `-`, `*`, `/`, `%`, `**`  
**Comparison:** `==`, `===`, `!=`, `!==`, `>`, `<`, `>=`, `<=`  
**Logical:** `&&`, `||`, `!`  
**Nullish Coalescing (`??`):** Returns right-hand side if left is `null` or `undefined`

```js
let name = null;
console.log(name ?? "Default"); // "Default"
```

**Optional Chaining (`?.`):** Safely access nested properties

```js
let user = { address: { city: "Mumbai" } };
console.log(user?.address?.city);  // "Mumbai"
console.log(user?.phone?.number);  // undefined (no error)
```

**Ternary Operator:**
```js
let age = 18;
let status = age >= 18 ? "Adult" : "Minor";
```

---

### 1.8 Arrays

```js
let fruits = ["apple", "banana", "cherry"];

// Access
fruits[0]; // "apple"

// Common Methods
fruits.push("mango");      // add to end
fruits.pop();              // remove from end
fruits.unshift("kiwi");    // add to start
fruits.shift();            // remove from start
fruits.indexOf("banana");  // 1
fruits.includes("cherry"); // true
fruits.slice(1, 3);        // ["banana", "cherry"]
fruits.splice(1, 1);       // removes 1 element at index 1

// Iteration
fruits.forEach(f => console.log(f));
fruits.map(f => f.toUpperCase());
fruits.filter(f => f.length > 5);
fruits.reduce((acc, f) => acc + f, "");
fruits.find(f => f === "cherry");
fruits.some(f => f === "apple");   // true if at least one matches
fruits.every(f => f.length > 3);   // true if all match
```

---

### 1.9 Objects

```js
const person = {
  name: "Kiran",
  age: 25,
  greet() {
    return "Hi, I'm " + this.name;
  }
};

// Access
person.name;         // dot notation
person["age"];       // bracket notation

// Destructuring
const { name, age } = person;

// Spread
const updated = { ...person, city: "Mumbai" };

// Object Methods
Object.keys(person);    // ["name", "age", "greet"]
Object.values(person);  // ["Kiran", 25, function]
Object.entries(person); // [["name","Kiran"], ...]
```

---

## 2. INTERMEDIATE LEVEL

---

### 2.1 Closures

A closure is a function that **remembers the variables from its outer scope** even after the outer function has finished executing.

```js
function counter() {
  let count = 0;
  return function() {
    count++;
    return count;
  };
}

const increment = counter();
increment(); // 1
increment(); // 2
increment(); // 3
```

**Why closures matter:**
- Data encapsulation / private variables
- Factory functions
- Memoization

---

### 2.2 this Keyword

`this` refers to the object that is executing the current function.

| Context | `this` value |
|---|---|
| Global (non-strict) | `window` (browser) |
| Global (strict) | `undefined` |
| Method call | The calling object |
| Arrow function | Inherited from enclosing scope |
| `call` / `apply` | Explicitly set |
| `new` | Newly created object |

```js
const obj = {
  name: "Kiran",
  greet() {
    console.log(this.name); // "Kiran"
  },
  arrowGreet: () => {
    console.log(this.name); // undefined (arrow doesn't bind this)
  }
};
```

**Explicit Binding:**
```js
function greet(city) {
  console.log(`${this.name} from ${city}`);
}

const user = { name: "Kiran" };

greet.call(user, "Mumbai");        // immediate call with args
greet.apply(user, ["Mumbai"]);     // immediate call with array of args
const bound = greet.bind(user);    // returns a new function
bound("Mumbai");
```

---

### 2.3 Prototypes and Prototype Chain

Every JavaScript object has a `[[Prototype]]` — a reference to another object it inherits from.

```js
function Animal(name) {
  this.name = name;
}
Animal.prototype.speak = function() {
  return this.name + " makes a sound";
};

const dog = new Animal("Dog");
dog.speak(); // "Dog makes a sound"
```

**Prototype Chain:** When you access a property, JS looks at the object, then its prototype, then the prototype's prototype... until `null`.

```js
dog.hasOwnProperty("name");   // true
dog.hasOwnProperty("speak");  // false (inherited)
```

---

### 2.4 Classes (ES6)

Classes are syntactic sugar over prototype-based inheritance.

```js
class Animal {
  constructor(name) {
    this.name = name;
  }

  speak() {
    return `${this.name} makes a sound`;
  }

  static describe() {
    return "I am an Animal class";
  }
}

class Dog extends Animal {
  constructor(name, breed) {
    super(name);  // calls Animal constructor
    this.breed = breed;
  }

  speak() {
    return `${this.name} barks`;
  }
}

const d = new Dog("Rex", "Labrador");
d.speak();           // "Rex barks"
Animal.describe();   // static method
```

**Private Fields (ES2022):**
```js
class Counter {
  #count = 0;   // private field

  increment() { this.#count++; }
  get() { return this.#count; }
}
```

---

### 2.5 Asynchronous JavaScript

JavaScript is single-threaded but handles async via the **Event Loop**.

**Callbacks:**
```js
setTimeout(() => console.log("Hello after 1s"), 1000);

fs.readFile("file.txt", (err, data) => {
  if (err) throw err;
  console.log(data);
});
```

Callbacks lead to "Callback Hell" when nested deeply.

**Promises:**
A Promise represents a value that may be available now, later, or never.

```js
const fetchData = new Promise((resolve, reject) => {
  setTimeout(() => resolve("Data received"), 1000);
});

fetchData
  .then(data => console.log(data))
  .catch(err => console.error(err))
  .finally(() => console.log("Done"));
```

**Promise Chaining:**
```js
fetch("/api/user")
  .then(res => res.json())
  .then(user => fetch(`/api/posts/${user.id}`))
  .then(res => res.json())
  .then(posts => console.log(posts))
  .catch(console.error);
```

**Promise Methods:**
```js
Promise.all([p1, p2, p3])     // waits for all; fails if any fails
Promise.allSettled([p1, p2])  // waits for all; reports all outcomes
Promise.race([p1, p2])        // resolves/rejects with first to settle
Promise.any([p1, p2])         // resolves with first to succeed
```

---

### 2.6 async / await

Syntactic sugar over Promises. Makes async code look and behave like synchronous code.

```js
async function getUser(id) {
  try {
    const res = await fetch(`/api/users/${id}`);
    const user = await res.json();
    return user;
  } catch (error) {
    console.error("Error:", error);
  }
}
```

- `async` functions always return a Promise
- `await` pauses execution inside the async function until the Promise resolves
- Always use `try/catch` for error handling

**Parallel execution:**
```js
async function loadAll() {
  const [users, posts] = await Promise.all([
    fetch("/api/users").then(r => r.json()),
    fetch("/api/posts").then(r => r.json())
  ]);
}
```

---

### 2.7 Event Loop

The JavaScript Engine has:
- **Call Stack** — Executes code synchronously
- **Web APIs** — setTimeout, fetch, DOM events
- **Callback Queue (Task Queue)** — Queues macrotasks (setTimeout, setInterval)
- **Microtask Queue** — Queues microtasks (Promises, queueMicrotask)

**Order of execution:**
1. All synchronous code (call stack)
2. All microtasks (Promise callbacks)
3. One macrotask (setTimeout/setInterval)
4. Repeat

```js
console.log("1");

setTimeout(() => console.log("2"), 0);

Promise.resolve().then(() => console.log("3"));

console.log("4");

// Output: 1, 4, 3, 2
```

---

### 2.8 Error Handling

```js
try {
  let result = riskyOperation();
} catch (error) {
  console.error(error.message);
} finally {
  // always runs
}
```

**Custom Errors:**
```js
class ValidationError extends Error {
  constructor(message) {
    super(message);
    this.name = "ValidationError";
  }
}

throw new ValidationError("Invalid email");
```

**Error Types:**
- `ReferenceError` — Accessing undeclared variable
- `TypeError` — Wrong type operation
- `SyntaxError` — Invalid syntax
- `RangeError` — Value out of allowed range

---

### 2.9 Destructuring

**Array Destructuring:**
```js
const [a, b, ...rest] = [1, 2, 3, 4, 5];
// a=1, b=2, rest=[3,4,5]
```

**Object Destructuring:**
```js
const { name, age, city = "Unknown" } = user;
// city defaults to "Unknown" if not present

// Rename while destructuring
const { name: userName } = user;
```

**In function parameters:**
```js
function display({ name, age }) {
  console.log(name, age);
}
```

---

### 2.10 Spread and Rest Operators

**Spread (`...`)** — Expands an iterable
```js
const arr1 = [1, 2, 3];
const arr2 = [...arr1, 4, 5]; // [1,2,3,4,5]

const obj1 = { a: 1 };
const obj2 = { ...obj1, b: 2 }; // {a:1, b:2}
```

**Rest (`...`)** — Collects remaining elements
```js
function sum(first, ...others) {
  return first + others.reduce((a, b) => a + b, 0);
}
```

---

## 3. ADVANCED LEVEL

---

### 3.1 Currying

Currying is the technique of converting a function with multiple arguments into a sequence of functions each taking a single argument.

```js
// Normal function
function add(a, b, c) {
  return a + b + c;
}

// Curried version
function curriedAdd(a) {
  return function(b) {
    return function(c) {
      return a + b + c;
    };
  };
}

curriedAdd(1)(2)(3); // 6

// Arrow shorthand
const curry = a => b => c => a + b + c;
```

**Use case:** Partial application — pre-fill some arguments
```js
const add5 = curriedAdd(5);
add5(3)(2); // 10
```

---

### 3.2 Memoization

Caching the result of expensive function calls to avoid recomputation.

```js
function memoize(fn) {
  const cache = {};
  return function(...args) {
    const key = JSON.stringify(args);
    if (cache[key] !== undefined) {
      return cache[key];
    }
    cache[key] = fn(...args);
    return cache[key];
  };
}

const expensiveCalc = memoize((n) => {
  console.log("Computing...");
  return n * n;
});

expensiveCalc(5); // Computing... 25
expensiveCalc(5); // 25 (from cache, no recomputation)
```

---

### 3.3 Higher Order Functions (HOF)

A higher-order function is a function that **takes a function as an argument** or **returns a function**.

```js
// Takes a function as argument
[1, 2, 3].map(x => x * 2);

// Returns a function
function multiplier(factor) {
  return (number) => number * factor;
}

const double = multiplier(2);
const triple = multiplier(3);
double(5); // 10
triple(5); // 15
```

**Common HOFs:** `map`, `filter`, `reduce`, `forEach`, `sort`, `find`

---

### 3.4 Generators

A generator is a function that can be paused and resumed, yielding multiple values over time.

```js
function* count() {
  yield 1;
  yield 2;
  yield 3;
}

const gen = count();
gen.next(); // { value: 1, done: false }
gen.next(); // { value: 2, done: false }
gen.next(); // { value: 3, done: false }
gen.next(); // { value: undefined, done: true }
```

**Infinite sequence:**
```js
function* infinite() {
  let i = 0;
  while (true) {
    yield i++;
  }
}
```

**Use cases:** Lazy evaluation, infinite sequences, async flow control

---

### 3.5 Iterators and Iterables

An **iterator** is an object with a `next()` method returning `{ value, done }`.

An **iterable** is any object that implements `[Symbol.iterator]()` and returns an iterator.

```js
const range = {
  from: 1,
  to: 5,
  [Symbol.iterator]() {
    let current = this.from;
    const last = this.to;
    return {
      next() {
        if (current <= last) {
          return { value: current++, done: false };
        }
        return { value: undefined, done: true };
      }
    };
  }
};

for (let num of range) {
  console.log(num); // 1, 2, 3, 4, 5
}
```

---

### 3.6 Proxy and Reflect

**Proxy** wraps an object and intercepts operations on it.

```js
const handler = {
  get(target, prop) {
    return prop in target ? target[prop] : `Property ${prop} not found`;
  },
  set(target, prop, value) {
    if (typeof value !== "number") throw new TypeError("Only numbers");
    target[prop] = value;
    return true;
  }
};

const obj = new Proxy({}, handler);
obj.age = 25;
obj.age;   // 25
obj.name;  // "Property name not found"
```

**Reflect** provides default behavior for the same operations.
```js
Reflect.get(target, prop);
Reflect.set(target, prop, value);
```

---

### 3.7 Symbols

Symbols are unique, immutable values used as object property keys.

```js
const id1 = Symbol("id");
const id2 = Symbol("id");
id1 === id2; // false — every Symbol is unique

const user = {
  [id1]: 123,
  name: "Kiran"
};

user[id1]; // 123
// Symbol keys don't appear in for...in or Object.keys()
```

**Well-known Symbols:**
- `Symbol.iterator` — makes objects iterable
- `Symbol.toPrimitive` — customize type conversion
- `Symbol.hasInstance` — customize `instanceof`

---

### 3.8 WeakMap and WeakSet

**WeakMap** — keys must be objects; entries are weakly held (garbage collected when key is no longer referenced)

```js
const wm = new WeakMap();
let obj = {};
wm.set(obj, "data");
wm.get(obj); // "data"
obj = null;  // obj is garbage collected, entry removed
```

**WeakSet** — only stores objects; weakly held

```js
const ws = new WeakSet();
let el = document.querySelector("button");
ws.add(el);
ws.has(el); // true
```

**Use case:** Storing private metadata about objects without preventing GC.

---

### 3.9 Memory Management and Garbage Collection

JavaScript uses **automatic garbage collection** via the **mark-and-sweep** algorithm:
- All objects reachable from "roots" (global, call stack) are marked
- Unmarked objects are removed

**Memory Leaks to Avoid:**
1. **Global variables** — never cleaned up
2. **Detached DOM nodes** — DOM removed but still referenced in JS
3. **Event listeners** — not removed after element is deleted
4. **Closures** — holding references to large objects unnecessarily
5. **Timers** — `setInterval` not cleared

```js
// Bad: memory leak
let el = document.getElementById("btn");
el.addEventListener("click", handler);
document.body.removeChild(el); // DOM removed but listener still holds reference

// Good: clean up
el.removeEventListener("click", handler);
```

---

### 3.10 Functional Programming Concepts

**Pure Functions** — Given same input, always return same output; no side effects
```js
const add = (a, b) => a + b; // pure
```

**Immutability** — Don't mutate data; create new copies
```js
const newArr = [...arr, newItem]; // instead of arr.push()
const newObj = { ...obj, key: value }; // instead of obj.key = value
```

**Function Composition** — Combine functions where output of one becomes input of another
```js
const compose = (...fns) => x => fns.reduceRight((v, f) => f(v), x);
const pipe = (...fns) => x => fns.reduce((v, f) => f(v), x);

const process = pipe(
  x => x * 2,
  x => x + 1,
  x => x ** 2
);
process(3); // ((3*2)+1)^2 = 49
```

---

## 4. JAVASCRIPT RUNTIME & ENGINE

---

### 4.1 How JavaScript Engines Work

Popular engines: **V8** (Chrome/Node), **SpiderMonkey** (Firefox), **JavaScriptCore** (Safari)

**Pipeline:**
1. **Parsing** — Source code → AST (Abstract Syntax Tree)
2. **Compilation** — AST → Bytecode (Ignition in V8)
3. **Optimization** — Hot functions compiled to machine code (TurboFan in V8)
4. **Execution**

**JIT (Just-In-Time) Compilation:** JavaScript is compiled at runtime, not ahead of time.

---

### 4.2 Call Stack and Execution Context

An **Execution Context** is an environment where JavaScript code is evaluated and executed.

Types:
- **Global Execution Context** — Created when script starts
- **Function Execution Context** — Created when a function is called

Each context has:
- **Variable Environment** — stores `var` variables and function declarations
- **Lexical Environment** — stores `let`, `const`, and outer scope reference
- **`this` binding**

**Call Stack** — LIFO structure that tracks execution contexts.

```
global EC → function A EC → function B EC
                         ← pops when B returns
         ← pops when A returns
```

---

### 4.3 Module System

**CommonJS (Node.js):**
```js
// export
module.exports = { add, subtract };

// import
const { add } = require("./math");
```

**ES Modules (ESM):**
```js
// Named export
export const add = (a, b) => a + b;
export default function main() {}

// Import
import main, { add } from "./math.js";
import * as math from "./math.js";
```

**Differences:**
- ESM is static (tree-shakable), CJS is dynamic
- ESM uses live bindings; CJS uses copies
- ESM is the standard for modern JavaScript

---

## 5. DESIGN PATTERNS

---

### 5.1 Singleton

Ensures only one instance of a class exists.

```js
class Database {
  static instance = null;

  static getInstance() {
    if (!Database.instance) {
      Database.instance = new Database();
    }
    return Database.instance;
  }
}
```

---

### 5.2 Observer Pattern

An object (subject) notifies observers when its state changes.

```js
class EventEmitter {
  constructor() {
    this.events = {};
  }

  on(event, listener) {
    if (!this.events[event]) this.events[event] = [];
    this.events[event].push(listener);
  }

  emit(event, data) {
    (this.events[event] || []).forEach(fn => fn(data));
  }

  off(event, listener) {
    this.events[event] = (this.events[event] || [])
      .filter(fn => fn !== listener);
  }
}
```

---

### 5.3 Factory Pattern

Creates objects without exposing instantiation logic.

```js
function createUser(role) {
  const base = { login() { return "Logging in..."; } };
  
  if (role === "admin") {
    return { ...base, deleteUser() { return "Deleting user..."; } };
  }
  
  return { ...base, viewContent() { return "Viewing..."; } };
}
```

---

## 6. MODERN JAVASCRIPT (ES6+)

---

### 6.1 Template Literals
```js
const name = "Kiran";
const greeting = `Hello, ${name}! Today is ${new Date().toDateString()}`;

// Tagged template literals
function highlight(strings, ...values) {
  return strings.reduce((result, str, i) =>
    result + str + (values[i] ? `<b>${values[i]}</b>` : ""), "");
}
```

### 6.2 Map and Set

**Set** — Collection of unique values
```js
const set = new Set([1, 2, 2, 3]);
set.size; // 3
set.has(2); // true
set.add(4);
set.delete(1);
```

**Map** — Key-value pairs; keys can be any type
```js
const map = new Map();
map.set("name", "Kiran");
map.set(42, "answer");
map.get("name"); // "Kiran"
map.has(42); // true
map.size; // 2
```

### 6.3 Optional Chaining and Nullish Coalescing
```js
const city = user?.address?.city ?? "Unknown";
```

### 6.4 Logical Assignment Operators (ES2021)
```js
a ||= b;  // a = a || b
a &&= b;  // a = a && b
a ??= b;  // a = a ?? b
```

### 6.5 Array Methods (ES2019+)
```js
[1, [2, [3]]].flat();       // [1, 2, [3]]
[1, [2, [3]]].flat(Infinity); // [1, 2, 3]

[1, 2, 3].flatMap(x => [x, x * 2]); // [1,2,2,4,3,6]

Object.fromEntries([["a", 1], ["b", 2]]); // {a:1, b:2}
```

---

## 7. TESTING & DEBUGGING

---

### 7.1 Debugging Tools

```js
console.log("value:", x);
console.table([{ name: "Kiran", age: 25 }]);
console.time("timer"); /* code */ console.timeEnd("timer");
console.trace(); // shows call stack
debugger;       // pauses execution in DevTools
```

### 7.2 Unit Testing Concepts

**Jest** — Most popular JS testing framework

```js
// sum.js
export const sum = (a, b) => a + b;

// sum.test.js
import { sum } from "./sum";

describe("sum function", () => {
  test("adds 1 + 2 to equal 3", () => {
    expect(sum(1, 2)).toBe(3);
  });

  test("returns a number", () => {
    expect(typeof sum(1, 2)).toBe("number");
  });
});
```

**Types of Tests:**
- **Unit** — Test individual functions/components
- **Integration** — Test interaction between modules
- **E2E** — Test full user flow (Cypress, Playwright)

---

## QUICK REFERENCE: Common Interview Questions

| Topic | Key Concept |
|---|---|
| `var` vs `let` vs `const` | Scope, hoisting, mutability |
| Closure | Function retaining outer scope after execution |
| `this` | Context-dependent; arrow functions inherit from lexical scope |
| Prototype chain | Inheritance lookup mechanism |
| Event Loop | Microtask queue runs before macrotask queue |
| Promise vs async/await | async/await is syntactic sugar over Promises |
| `==` vs `===` | Type coercion vs strict equality |
| Shallow vs Deep Copy | Spread is shallow; `structuredClone()` is deep |
| Currying | `f(a, b)` → `f(a)(b)` |
| HOF | Function that takes/returns a function |
| Generator | Pausable function using `yield` |
| Memoization | Cache results of expensive function calls |
| Debounce vs Throttle | Delay execution vs limit rate of execution |

---

### Debounce vs Throttle

**Debounce** — Delay function execution until after a period of inactivity

```js
function debounce(fn, delay) {
  let timer;
  return function(...args) {
    clearTimeout(timer);
    timer = setTimeout(() => fn.apply(this, args), delay);
  };
}
// Use: search input — wait until user stops typing
```

**Throttle** — Limit function to run at most once per time interval

```js
function throttle(fn, limit) {
  let lastCall = 0;
  return function(...args) {
    const now = Date.now();
    if (now - lastCall >= limit) {
      lastCall = now;
      return fn.apply(this, args);
    }
  };
}
// Use: scroll/resize events
```

---

*Good luck with your JavaScript interviews!*
