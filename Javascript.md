# Complete JavaScript & Node.js Interview Explanations

---

# PART 1: JAVASCRIPT CORE CONCEPTS - INTERVIEW EXPLANATIONS

---

## 1. Variables & Data Types

### 🎯 How to Explain in Interview

**Simple One-Line Answer First:**
> "JavaScript has 3 ways to declare variables - var, let, and const - each with different scoping rules and behaviors."

### Complete Interview Explanation:

```
INTERVIEWER: "Can you explain var, let, and const?"

YOUR ANSWER STRUCTURE:
1. Start with the basic difference
2. Explain scoping
3. Explain hoisting behavior
4. Give practical example
5. Say which one you prefer and why
```

```javascript
// ============================================
// INTERVIEW EXPLANATION SCRIPT
// ============================================

/*
"Let me explain all three with their key differences:"

VAR:
- Function scoped (not block scoped)
- Gets hoisted and initialized with 'undefined'
- Can be redeclared in same scope
- OLD way, avoid using in modern code

LET:
- Block scoped (inside {})
- Gets hoisted but NOT initialized (TDZ)
- Cannot be redeclared in same scope
- CAN be reassigned

CONST:
- Block scoped (inside {})
- Gets hoisted but NOT initialized (TDZ)  
- Cannot be redeclared
- CANNOT be reassigned
- But object PROPERTIES can still be changed
*/

// EXAMPLE 1: Scope Difference
function scopeExample() {
    // VAR is function scoped
    if (true) {
        var varVariable = "I am var";
        let letVariable = "I am let";
        const constVariable = "I am const";
    }
    
    console.log(varVariable);   // "I am var" - accessible!
    // console.log(letVariable);   // ReferenceError - not accessible
    // console.log(constVariable); // ReferenceError - not accessible
}

// EXAMPLE 2: Redeclaration
var a = 1;
var a = 2; // No error - var allows redeclaration
console.log(a); // 2

let b = 1;
// let b = 2; // SyntaxError - let doesn't allow redeclaration

const c = 1;
// const c = 2; // SyntaxError

// EXAMPLE 3: Const with objects (common interview trap!)
const person = { name: "John", age: 30 };
person.name = "Jane"; // This is ALLOWED
person.age = 25;      // This is ALLOWED
console.log(person);  // { name: "Jane", age: 25 }

// person = { name: "Bob" }; // This is NOT ALLOWED - reassignment

/*
INTERVIEWER FOLLOW-UP: "Which one do you prefer?"

ANSWER: 
"In modern JavaScript, I always prefer:
- const by default for everything
- let when I know the variable will be reassigned
- Never var, because its function-scoping can lead to bugs"
*/
```

### Data Types - Interview Explanation:

```javascript
/*
INTERVIEWER: "What are the data types in JavaScript?"

ANSWER: "JavaScript has 8 data types divided into 2 categories:"

PRIMITIVE types (immutable, stored by VALUE):
1. String     - "hello"
2. Number     - 42, 3.14
3. Boolean    - true, false
4. Undefined  - variable declared but not assigned
5. Null       - intentional absence of value
6. Symbol     - unique identifier (ES6)
7. BigInt     - very large numbers (ES2020)

NON-PRIMITIVE (Reference types, stored by REFERENCE):
8. Object     - {}, [], functions
*/

// EXAMPLES TO SHOW IN INTERVIEW

// Primitive - stored by value
let x = 10;
let y = x;  // y gets a COPY of value
y = 20;
console.log(x); // 10 - x is unchanged
console.log(y); // 20

// Reference - stored by reference
let obj1 = { name: "John" };
let obj2 = obj1;  // obj2 points to SAME object
obj2.name = "Jane";
console.log(obj1.name); // "Jane" - obj1 is CHANGED!
// Because obj1 and obj2 point to same memory location

// typeof - common interview question
console.log(typeof "hello");    // "string"
console.log(typeof 42);         // "number"
console.log(typeof true);       // "boolean"
console.log(typeof undefined);  // "undefined"
console.log(typeof null);       // "object" ← TRICK QUESTION!
console.log(typeof {});         // "object"
console.log(typeof []);         // "object" ← Array is also object!
console.log(typeof function(){}); // "function"
console.log(typeof Symbol());   // "symbol"

/*
IMPORTANT: typeof null === "object" is a KNOWN BUG in JavaScript
that exists for historical reasons. They never fixed it to maintain
backward compatibility.

To check for null: 
value === null (use strict equality)
*/

// How to properly check types
function getType(value) {
    if (value === null) return "null";
    if (Array.isArray(value)) return "array";
    return typeof value;
}

console.log(getType(null));    // "null"
console.log(getType([]));      // "array"
console.log(getType("hello")); // "string"
console.log(getType(42));      // "number"
```

### Type Coercion - Must Know for Interviews:

```javascript
/*
INTERVIEWER: "Explain type coercion in JavaScript"

ANSWER: "Type coercion is JavaScript's automatic type conversion.
It happens when you use operators on different types."

Two types:
1. Implicit coercion - JavaScript does it automatically
2. Explicit coercion - Developer does it manually
*/

// IMPLICIT COERCION (JavaScript auto-converts)
// String + Number = String (concatenation)
console.log(1 + "2");      // "12" (number becomes string)
console.log("3" + 4 + 5);  // "345" (left to right)
console.log(3 + 4 + "5");  // "75" (3+4=7, then 7+"5"="75")

// Other operators convert to Number
console.log("5" - 2);  // 3 (string becomes number)
console.log("5" * 2);  // 10
console.log("5" / 2);  // 2.5
console.log("5" - "3"); // 2

// Boolean coercion (falsy values)
// These are ALL falsy:
console.log(Boolean(0));          // false
console.log(Boolean(""));         // false
console.log(Boolean(null));       // false
console.log(Boolean(undefined));  // false
console.log(Boolean(NaN));        // false
console.log(Boolean(false));      // false

// Everything else is truthy:
console.log(Boolean(1));          // true
console.log(Boolean("hello"));    // true
console.log(Boolean([]));         // true ← Empty array is truthy!
console.log(Boolean({}));         // true ← Empty object is truthy!

// EXPLICIT COERCION (you do it manually)
Number("42");     // 42
Number(true);     // 1
Number(false);    // 0
Number(null);     // 0
Number(undefined); // NaN
Number("");       // 0

String(42);       // "42"
String(true);     // "true"
String(null);     // "null"

Boolean(1);       // true
Boolean(0);       // false

// parseInt vs Number
Number("42abc");   // NaN
parseInt("42abc"); // 42 ← stops at non-numeric

/*
GOLDEN RULE TO TELL INTERVIEWER:
"Always use === instead of == to avoid unexpected type coercion"

0 == false   // true (coercion)
0 === false  // false (no coercion)
"" == false  // true (coercion)
"" === false // false (no coercion)
*/
```

---

## 2. Hoisting - Interview Explanation

### 🎯 How to Explain in Interview

**Simple One-Line Answer:**
> "Hoisting is JavaScript's behavior of moving declarations to the top of their scope before code execution."

```javascript
/*
INTERVIEWER: "What is hoisting?"

PERFECT ANSWER STRUCTURE:
"Hoisting is JavaScript's default behavior where variable and 
function declarations are moved to the top of their scope 
during the compilation phase, before code execution.

Three important points:
1. Only DECLARATIONS are hoisted, not INITIALIZATIONS
2. Function DECLARATIONS are fully hoisted
3. let and const are hoisted but in 'Temporal Dead Zone'
"
*/

// ============================================
// EXAMPLE 1: VAR HOISTING
// ============================================

// What you write:
console.log(name); // undefined (not error!)
var name = "John";
console.log(name); // "John"

// What JavaScript actually does internally:
var name;           // ← Declaration hoisted to top
console.log(name);  // undefined
name = "John";      // ← Assignment stays in place
console.log(name);  // "John"

// ============================================
// EXAMPLE 2: FUNCTION DECLARATION HOISTING
// ============================================

// Can call BEFORE declaration - it works!
greet("John"); // "Hello, John!"

function greet(name) {
    console.log(`Hello, ${name}!`);
}

// Why? Because ENTIRE function is hoisted
// JavaScript sees it like this:
function greet(name) {  // ← Entire function hoisted
    console.log(`Hello, ${name}!`);
}
greet("John"); // Now works!

// ============================================
// EXAMPLE 3: FUNCTION EXPRESSION - NOT hoisted
// ============================================

// sayHi(); // TypeError: sayHi is not a function

var sayHi = function() {  // Function expression
    console.log("Hi!");
};

// What JS sees:
var sayHi;    // ← Only declaration hoisted
sayHi();      // TypeError! sayHi is undefined, not a function
sayHi = function() {
    console.log("Hi!");
};

// ============================================
// EXAMPLE 4: LET & CONST - Temporal Dead Zone
// ============================================

// console.log(age); // ReferenceError: Cannot access 'age' before initialization
let age = 25;

/*
Temporal Dead Zone (TDZ):
- let and const ARE hoisted
- But they are NOT initialized
- They exist in TDZ from start of scope until declaration
- Accessing them in TDZ throws ReferenceError
*/

// TDZ visualization:
{
    // --- TDZ for 'myVar' starts here ---
    // console.log(myVar); // ReferenceError
    // console.log(myVar); // ReferenceError
    
    let myVar = "Hello"; // TDZ ends here
    
    console.log(myVar); // "Hello" - works fine
}

// ============================================
// PRACTICAL INTERVIEW EXAMPLE
// ============================================

var x = 1;
function hoistingTest() {
    console.log(x); // What prints here?
    var x = 2;
    console.log(x); // What prints here?
}
hoistingTest();

/*
ANSWER: undefined, then 2

Why? Inside function, var x is hoisted to top of FUNCTION:
function hoistingTest() {
    var x;          ← hoisted
    console.log(x); ← undefined (not outer x!)
    x = 2;
    console.log(x); ← 2
}

This is why var inside function creates its OWN x,
not the outer x = 1
*/
```

---

## 3. Scope & Closures - Interview Explanation

### 🎯 How to Explain in Interview

**Simple One-Line Answer for Scope:**
> "Scope determines where variables are accessible in your code."

**Simple One-Line Answer for Closure:**
> "A closure is a function that remembers the variables from its outer scope even after the outer function has finished executing."

```javascript
/*
INTERVIEWER: "Explain scope in JavaScript"

ANSWER:
"There are 4 types of scope in JavaScript:
1. Global scope - accessible everywhere
2. Function scope - only inside the function
3. Block scope - only inside {} (let and const)
4. Lexical/Module scope - based on where code is written
"
*/

// ============================================
// SCOPE TYPES - INTERVIEW WALKTHROUGH
// ============================================

// 1. GLOBAL SCOPE
let globalVar = "I'm global";

function example() {
    console.log(globalVar); // Accessible - global scope
}
example();
console.log(globalVar); // Accessible everywhere

// 2. FUNCTION SCOPE
function myFunction() {
    let functionVar = "I'm function scoped";
    console.log(functionVar); // Accessible inside function
}
myFunction();
// console.log(functionVar); // ReferenceError - not accessible outside

// 3. BLOCK SCOPE (let and const only!)
{
    let blockLet = "I'm block scoped";
    const blockConst = "Me too";
    var blockVar = "I'm NOT block scoped (var)";
    
    console.log(blockLet);   // Works
    console.log(blockConst); // Works
}
// console.log(blockLet);   // ReferenceError
// console.log(blockConst); // ReferenceError
console.log(blockVar);      // Works! var ignores block scope

// 4. SCOPE CHAIN (very important for interviews!)
/*
When JavaScript looks for a variable:
1. Checks current scope
2. Goes up to parent scope
3. Goes up to grandparent scope
4. Until it reaches global scope
5. If not found, throws ReferenceError
*/

const outerVar = "outer";

function outerFunction() {
    const middleVar = "middle";
    
    function innerFunction() {
        const innerVar = "inner";
        
        // Can access all outer variables (scope chain)
        console.log(innerVar);  // "inner" (own scope)
        console.log(middleVar); // "middle" (parent scope)
        console.log(outerVar);  // "outer" (grandparent scope)
    }
    
    innerFunction();
    // console.log(innerVar); // ReferenceError (can't go down)
}

outerFunction();
```

```javascript
// ============================================
// CLOSURES - MOST IMPORTANT INTERVIEW TOPIC
// ============================================

/*
INTERVIEWER: "What is a closure? Explain with a real-world example."

PERFECT ANSWER:
"A closure is when a function retains access to variables from 
its outer scope even after the outer function has returned.

Think of it like a backpack - when an inner function is created,
it 'packs' all the variables it needs from its outer scope.
Even when the outer function is gone, the inner function still
has its backpack with all those variables."
*/

// BASIC CLOSURE EXAMPLE
function outerFunction() {
    let count = 0; // This variable is 'closed over'
    
    function innerFunction() {
        count++; // Accessing outer variable
        console.log(count);
    }
    
    return innerFunction;
}

const counter = outerFunction(); // outerFunction is DONE executing
// But 'count' is still alive because innerFunction references it!

counter(); // 1
counter(); // 2
counter(); // 3
// count is remembered between calls!

// ============================================
// REAL WORLD CLOSURE EXAMPLES
// ============================================

// EXAMPLE 1: Private Variables (Encapsulation)
function createBankAccount(initialBalance) {
    // Private - can't access directly from outside
    let balance = initialBalance;
    let transactionHistory = [];
    
    return {
        deposit(amount) {
            if (amount <= 0) throw new Error("Amount must be positive");
            balance += amount;
            transactionHistory.push({ type: 'deposit', amount, date: new Date() });
            console.log(`Deposited $${amount}. Balance: $${balance}`);
        },
        withdraw(amount) {
            if (amount > balance) throw new Error("Insufficient funds");
            balance -= amount;
            transactionHistory.push({ type: 'withdrawal', amount, date: new Date() });
            console.log(`Withdrew $${amount}. Balance: $${balance}`);
        },
        getBalance() {
            return balance; // Controlled access
        },
        getHistory() {
            return [...transactionHistory]; // Return copy, not original
        }
    };
}

const account = createBankAccount(1000);
account.deposit(500);    // Deposited $500. Balance: $1500
account.withdraw(200);   // Withdrew $200. Balance: $1300
console.log(account.getBalance()); // 1300
// console.log(account.balance); // undefined - truly private!

// EXAMPLE 2: Function Factory (creating specialized functions)
function createMultiplier(multiplier) {
    // multiplier is 'closed over'
    return function(number) {
        return number * multiplier;
    };
}

const double = createMultiplier(2);
const triple = createMultiplier(3);
const tenX = createMultiplier(10);

console.log(double(5));  // 10
console.log(triple(5));  // 15
console.log(tenX(5));    // 50

// EXAMPLE 3: The Classic Bug (Very Common Interview Question!)
/*
INTERVIEWER: "What does this code output?"
*/

// BUG VERSION:
for (var i = 0; i < 3; i++) {
    setTimeout(function() {
        console.log(i); // What prints?
    }, 1000);
}
// Prints: 3, 3, 3 (NOT 0, 1, 2!)

/*
WHY? 
- var is function scoped
- All 3 setTimeout callbacks share the SAME 'i' variable
- By the time callbacks run, loop is done, i = 3
- All 3 callbacks see i = 3
*/

// FIX 1: Use let (block scoped - creates new i each iteration)
for (let i = 0; i < 3; i++) {
    setTimeout(function() {
        console.log(i); // Prints: 0, 1, 2 ✓
    }, 1000);
}

// FIX 2: Use closure (IIFE)
for (var i = 0; i < 3; i++) {
    (function(j) { // j captures the current value of i
        setTimeout(function() {
            console.log(j); // Prints: 0, 1, 2 ✓
        }, 1000);
    })(i); // Pass i immediately
}

// EXAMPLE 4: Memoization using Closure
function memoize(expensiveFunction) {
    const cache = {}; // Closed over cache
    
    return function(n) {
        if (cache[n] !== undefined) {
            console.log(`Cache hit for ${n}`);
            return cache[n];
        }
        
        const result = expensiveFunction(n);
        cache[n] = result;
        return result;
    };
}

const expensiveCalc = (n) => {
    // Simulate expensive calculation
    let result = 0;
    for (let i = 0; i <= n; i++) result += i;
    return result;
};

const memoCalc = memoize(expensiveCalc);
console.log(memoCalc(100)); // Calculates: 5050
console.log(memoCalc(100)); // Cache hit! Returns 5050 instantly
console.log(memoCalc(200)); // Calculates: 20100
```

---

## 4. 'this' Keyword - Interview Explanation

### 🎯 How to Explain in Interview

**Simple One-Line Answer:**
> "'this' refers to the object that is currently executing the function. Its value depends on HOW the function is called, not WHERE it's defined."

```javascript
/*
INTERVIEWER: "Explain the 'this' keyword"

PERFECT ANSWER:
"'this' is a special keyword that refers to the context object - 
the object that is calling the function. The value of 'this' 
changes based on how the function is called:

1. In global scope → window (browser) or global (Node.js)
2. In a regular function → depends on who calls it
3. In a method → the object that owns the method
4. In arrow function → inherits 'this' from surrounding scope
5. With new keyword → the newly created object
6. With call/apply/bind → explicitly set by developer
"
*/

// ============================================
// 6 RULES OF 'this'
// ============================================

// RULE 1: Global Context
console.log(this); // window in browser, {} in Node.js

// RULE 2: Regular Function
function showThis() {
    console.log(this); // window/global (non-strict) or undefined (strict mode)
}
showThis();

// RULE 3: Method (Object)
const car = {
    brand: "Toyota",
    model: "Camry",
    
    getInfo() {
        // 'this' = the car object
        return `${this.brand} ${this.model}`;
    }
};

console.log(car.getInfo()); // "Toyota Camry"

// Watch out! When you take method out of object:
const getInfo = car.getInfo;
// getInfo(); // this is now undefined/window - NOT car!

// RULE 4: Arrow Function (inherits 'this' from parent)
const team = {
    name: "Dev Team",
    members: ["Alice", "Bob"],
    
    // Regular function
    listRegular: function() {
        this.members.forEach(function(member) {
            // 'this' here is NOT team - it's undefined/window
            console.log(`${this.name}: ${member}`); // undefined: Alice
        });
    },
    
    // Arrow function
    listArrow: function() {
        this.members.forEach((member) => {
            // 'this' here IS team - arrow inherits from listArrow
            console.log(`${this.name}: ${member}`); // Dev Team: Alice
        });
    }
};

team.listRegular(); // undefined: Alice, undefined: Bob
team.listArrow();   // Dev Team: Alice, Dev Team: Bob

// RULE 5: Constructor (new keyword)
function Person(name, age) {
    // 'this' = newly created object
    this.name = name;
    this.age = age;
    this.introduce = function() {
        return `Hi, I'm ${this.name}, ${this.age} years old`;
    };
}

const john = new Person("John", 30);
console.log(john.introduce()); // Hi, I'm John, 30 years old

// RULE 6: call, apply, bind - Explicit Binding
function greet(greeting, punctuation) {
    return `${greeting}, ${this.name}${punctuation}`;
}

const user1 = { name: "Alice" };
const user2 = { name: "Bob" };

// call - invoke immediately, args individually
console.log(greet.call(user1, "Hello", "!"));   // Hello, Alice!
console.log(greet.call(user2, "Hi", "?"));      // Hi, Bob?

// apply - invoke immediately, args as array
console.log(greet.apply(user1, ["Hello", "!"])); // Hello, Alice!

// bind - creates NEW function with bound 'this'
const greetAlice = greet.bind(user1);
console.log(greetAlice("Hey", ".")); // Hey, Alice.

// Partial application with bind
const helloAlice = greet.bind(user1, "Hello"); // 'Hello' is pre-filled
console.log(helloAlice("!"));  // Hello, Alice!
console.log(helloAlice("??")); // Hello, Alice??

// ============================================
// COMMON 'this' INTERVIEW SCENARIOS
// ============================================

// SCENARIO 1: Event handlers (common)
/*
class Button {
    constructor(text) {
        this.text = text;
    }
    
    // PROBLEM: 'this' is lost when passed as callback
    handleClick() {
        console.log(`Clicked: ${this.text}`);
    }
    
    attach(element) {
        // WRONG: 'this' in handleClick will be the DOM element
        element.addEventListener('click', this.handleClick);
        
        // CORRECT FIX 1: Use bind
        element.addEventListener('click', this.handleClick.bind(this));
        
        // CORRECT FIX 2: Use arrow function
        element.addEventListener('click', () => this.handleClick());
    }
}
*/

// SCENARIO 2: Method shorthand
const obj = {
    value: 42,
    
    // Arrow function as method - PROBLEM
    getValueArrow: () => {
        return this.value; // 'this' is NOT obj - it's global!
    },
    
    // Regular function as method - CORRECT
    getValue: function() {
        return this.value; // 'this' IS obj
    },
    
    // ES6 shorthand - CORRECT
    getValueShorthand() {
        return this.value; // 'this' IS obj
    }
};

console.log(obj.getValueArrow());      // undefined (wrong!)
console.log(obj.getValue());           // 42 (correct!)
console.log(obj.getValueShorthand());  // 42 (correct!)
```

---

## 5. Prototypes & Inheritance - Interview Explanation

### 🎯 How to Explain in Interview

**Simple One-Line Answer:**
> "Every JavaScript object has a hidden link to another object called its prototype. When you access a property, JavaScript first checks the object itself, then its prototype chain until it finds it or reaches null."

```javascript
/*
INTERVIEWER: "Explain prototypal inheritance"

PERFECT ANSWER:
"JavaScript uses prototypal inheritance, which means objects
can inherit properties and methods from other objects through
a prototype chain.

Unlike classical inheritance (Java/C++), JavaScript doesn't
copy properties - it creates LINKS between objects.
When you access a property, JavaScript travels up the chain
until it finds it.

This is more memory efficient because methods aren't
copied to each instance - they're shared via the prototype."
*/

// ============================================
// PROTOTYPE CHAIN - VISUAL EXPLANATION
// ============================================

/*
Object Hierarchy:
john → Person.prototype → Object.prototype → null

When we do john.greet():
1. Check john (own property?) - NO
2. Check Person.prototype - YES! Found greet()
3. Execute it with this = john
*/

function Person(name, age) {
    // Own properties (unique per instance)
    this.name = name;
    this.age = age;
}

// Prototype methods (SHARED across all instances)
Person.prototype.greet = function() {
    return `Hi, I'm ${this.name}`;
};

Person.prototype.getAge = function() {
    return this.age;
};

const john = new Person("John", 30);
const jane = new Person("Jane", 25);

console.log(john.greet()); // Hi, I'm John
console.log(jane.greet()); // Hi, I'm Jane

// greet is SHARED - not copied to each instance
console.log(john.greet === jane.greet); // true (same function!)
// This is more memory efficient!

// Own vs Prototype properties
console.log(john.hasOwnProperty('name'));  // true (own)
console.log(john.hasOwnProperty('greet')); // false (prototype)

// ============================================
// PROTOTYPE CHAIN WITH INHERITANCE
// ============================================

// Parent class
function Animal(name, sound) {
    this.name = name;
    this.sound = sound;
}

Animal.prototype.speak = function() {
    return `${this.name} says ${this.sound}`;
};

Animal.prototype.describe = function() {
    return `I am ${this.name}, an animal`;
};

// Child class
function Dog(name, breed) {
    // Call parent constructor
    Animal.call(this, name, "Woof");
    this.breed = breed;
}

// Set up prototype chain
Dog.prototype = Object.create(Animal.prototype);
Dog.prototype.constructor = Dog; // Fix constructor reference

// Dog-specific methods
Dog.prototype.fetch = function() {
    return `${this.name} fetches the ball!`;
};

// Override parent method
Dog.prototype.speak = function() {
    return `${this.name} barks: ${this.sound}!`;
};

const rex = new Dog("Rex", "Labrador");

console.log(rex.speak());    // Rex barks: Woof! (own prototype)
console.log(rex.fetch());    // Rex fetches the ball! (Dog prototype)
console.log(rex.describe()); // I am Rex, an animal (Animal prototype)

// Check prototype chain
console.log(rex instanceof Dog);    // true
console.log(rex instanceof Animal); // true
console.log(rex instanceof Object); // true

// ============================================
// ES6 CLASS (Same thing, cleaner syntax)
// ============================================

/*
INTERVIEWER: "How do ES6 classes relate to prototypes?"

ANSWER: "ES6 classes are just syntactic sugar over prototypes.
Behind the scenes, they work exactly the same way."
*/

class AnimalClass {
    constructor(name, sound) {
        this.name = name;
        this.sound = sound;
    }
    
    speak() { // Goes to AnimalClass.prototype.speak
        return `${this.name} says ${this.sound}`;
    }
    
    static create(name, sound) { // Static - on the class itself
        return new AnimalClass(name, sound);
    }
}

class DogClass extends AnimalClass {
    constructor(name, breed) {
        super(name, "Woof"); // Calls Animal constructor
        this.breed = breed;
    }
    
    // Override
    speak() {
        return `${super.speak()}!!`; // Use parent's speak
    }
    
    fetch() {
        return `${this.name} fetches!`;
    }
}

const buddy = new DogClass("Buddy", "Poodle");
console.log(buddy.speak()); // Buddy says Woof!!

// Under the hood - same as before!
console.log(typeof DogClass); // "function" (it's still a function!)
console.log(DogClass.prototype.speak); // [Function: speak]
```

---

## 6. ES6+ Features - Interview Explanations

```javascript
// ============================================
// ARROW FUNCTIONS - Interview Explanation
// ============================================

/*
INTERVIEWER: "What is the difference between arrow functions 
and regular functions?"

KEY DIFFERENCES:
1. Syntax (shorter)
2. 'this' binding (no own 'this')
3. Cannot be used as constructors
4. No 'arguments' object
5. Cannot be generator functions
*/

// Regular function
function add(a, b) {
    return a + b;
}

// Arrow function variations
const add1 = (a, b) => a + b;           // Implicit return
const add2 = (a, b) => { return a + b; }; // Explicit return
const square = n => n * n;               // Single param, no ()
const greet = () => "Hello!";            // No params

// CRITICAL: 'this' difference
const timer = {
    seconds: 0,
    
    start: function() {
        // setInterval with regular function - 'this' is lost
        setInterval(function() {
            this.seconds++; // 'this' is NOT timer!
            console.log(this.seconds); // NaN
        }, 1000);
    },
    
    startFixed: function() {
        // Arrow function inherits 'this' from startFixed
        setInterval(() => {
            this.seconds++; // 'this' IS timer!
            console.log(this.seconds); // 1, 2, 3...
        }, 1000);
    }
};

// ============================================
// DESTRUCTURING - Interview Explanation
// ============================================

/*
INTERVIEWER: "Explain destructuring"

ANSWER: "Destructuring is a syntax that allows you to unpack
values from arrays or objects into separate variables.
It makes code cleaner and more readable."
*/

// ARRAY DESTRUCTURING
const coordinates = [10, 20, 30];
const [x, y, z] = coordinates;
// Same as:
// const x = coordinates[0];
// const y = coordinates[1];
// const z = coordinates[2];

// Skip values
const [first, , third] = [1, 2, 3];
console.log(first, third); // 1, 3

// Default values
const [a = 0, b = 0, c = 0] = [1, 2];
console.log(a, b, c); // 1, 2, 0

// Swap variables (elegant!)
let p = 1, q = 2;
[p, q] = [q, p];
console.log(p, q); // 2, 1

// Rest in arrays
const [head, ...tail] = [1, 2, 3, 4, 5];
console.log(head); // 1
console.log(tail); // [2, 3, 4, 5]

// OBJECT DESTRUCTURING
const user = {
    name: "John",
    age: 30,
    email: "john@example.com",
    address: {
        city: "New York",
        country: "USA"
    }
};

// Basic
const { name, age } = user;

// Rename variables
const { name: fullName, age: years } = user;
console.log(fullName, years); // John, 30

// Default values
const { name: n, salary = 50000 } = user;
console.log(salary); // 50000 (user doesn't have salary)

// Nested destructuring
const { address: { city, country } } = user;
console.log(city, country); // New York, USA

// Function parameter destructuring (very common in interviews!)
function displayUser({ name, age, email, salary = "Not specified" }) {
    console.log(`Name: ${name}, Age: ${age}, Salary: ${salary}`);
}
displayUser(user); // Name: John, Age: 30, Salary: Not specified

// REST in objects
const { name: uName, ...rest } = user;
console.log(uName); // "John"
console.log(rest);  // { age: 30, email: "john@...", address: {...} }

// ============================================
// SPREAD OPERATOR - Interview Explanation
// ============================================

/*
INTERVIEWER: "Explain spread operator"

ANSWER: "Spread operator (...) expands an iterable (array/object)
into individual elements. It's the opposite of rest operator.

Use cases:
1. Clone arrays/objects (shallow copy)
2. Merge arrays/objects
3. Pass array as function arguments
4. Convert iterables to arrays
"
*/

// Clone (shallow copy)
const originalArr = [1, 2, 3];
const clonedArr = [...originalArr]; // Independent copy
clonedArr.push(4);
console.log(originalArr); // [1, 2, 3] - unchanged
console.log(clonedArr);   // [1, 2, 3, 4]

// Merge arrays
const arr1 = [1, 2, 3];
const arr2 = [4, 5, 6];
const merged = [...arr1, ...arr2]; // [1, 2, 3, 4, 5, 6]
const withExtra = [...arr1, 10, ...arr2, 20]; // [1,2,3,10,4,5,6,20]

// Clone object
const originalObj = { a: 1, b: 2 };
const clonedObj = { ...originalObj };

// Merge objects
const obj1 = { a: 1, b: 2 };
const obj2 = { c: 3, d: 4 };
const mergedObj = { ...obj1, ...obj2 }; // {a:1, b:2, c:3, d:4}

// Override properties
const user2 = { name: "John", age: 30, city: "NYC" };
const updatedUser = { ...user2, age: 31 }; // Override age
console.log(updatedUser); // { name: "John", age: 31, city: "NYC" }

// Function arguments
function sum(a, b, c) {
    return a + b + c;
}
const numbers = [1, 2, 3];
console.log(sum(...numbers)); // 6 (spread as args)

// ============================================
// REST PARAMETERS - Interview Explanation
// ============================================

/*
REST (...) collects multiple elements INTO an array
SPREAD (...) expands an array INTO individual elements

Same syntax, opposite behavior - context determines which!
*/

// REST in functions
function sumAll(...numbers) { // Collects all args into array
    return numbers.reduce((total, n) => total + n, 0);
}

console.log(sumAll(1, 2, 3));       // 6
console.log(sumAll(1, 2, 3, 4, 5)); // 15

// Mix regular params with rest
function firstAndRest(first, second, ...others) {
    console.log(first);  // 1
    console.log(second); // 2
    console.log(others); // [3, 4, 5]
}
firstAndRest(1, 2, 3, 4, 5);
```

---

## 7. Promises & Async/Await - Interview Explanation

### 🎯 How to Explain in Interview

**Simple One-Line Answer:**
> "Promises are objects that represent the eventual result of an asynchronous operation. Async/await is syntactic sugar that makes working with promises look and feel like synchronous code."

```javascript
/*
INTERVIEWER: "Explain Promises and why we need them"

PERFECT ANSWER:
"Before Promises, we used callbacks for async operations.
This led to 'callback hell' - deeply nested, hard to read code.

Promises solve this by:
1. Representing a future value (pending → fulfilled/rejected)
2. Allowing method chaining (.then, .catch)
3. Better error handling
4. Avoiding callback hell

A Promise has 3 states:
- Pending: initial state, operation in progress
- Fulfilled: operation succeeded, has a value
- Rejected: operation failed, has a reason
"
*/

// ============================================
// WHY WE NEED PROMISES (Callback Hell First)
// ============================================

// CALLBACK HELL (the problem)
function getUserCallback(id, callback) {
    setTimeout(() => {
        callback(null, { id, name: "John" });
    }, 500);
}

function getPostsCallback(userId, callback) {
    setTimeout(() => {
        callback(null, [{ id: 1, title: "Post 1", userId }]);
    }, 500);
}

function getCommentsCallback(postId, callback) {
    setTimeout(() => {
        callback(null, [{ id: 1, text: "Nice post!", postId }]);
    }, 500);
}

// This is CALLBACK HELL - pyramid of doom!
getUserCallback(1, (err, user) => {
    if (err) return console.error(err);
    getPostsCallback(user.id, (err, posts) => {
        if (err) return console.error(err);
        getCommentsCallback(posts[0].id, (err, comments) => {
            if (err) return console.error(err);
            console.log({ user, posts, comments });
            // Imagine 2 more levels deep...
        });
    });
});

// ============================================
// PROMISES - THE SOLUTION
// ============================================

// Creating a Promise
function getUser(id) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            if (id <= 0) {
                reject(new Error("Invalid ID")); // Failure
            } else {
                resolve({ id, name: "John", email: "john@test.com" }); // Success
            }
        }, 500);
    });
}

function getPosts(userId) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve([{ id: 1, title: "Post 1", userId }]);
        }, 500);
    });
}

// Using Promises - CLEAN chaining!
getUser(1)
    .then(user => {
        console.log("User:", user);
        return getPosts(user.id); // Return next promise
    })
    .then(posts => {
        console.log("Posts:", posts);
        return posts[0];
    })
    .then(post => {
        console.log("First post:", post.title);
    })
    .catch(error => {
        // ONE catch handles ALL errors in the chain
        console.error("Error:", error.message);
    })
    .finally(() => {
        console.log("Always runs - cleanup here");
    });

// ============================================
// ASYNC/AWAIT - EVEN CLEANER
// ============================================

/*
INTERVIEWER: "What is async/await? How does it relate to Promises?"

ANSWER: "Async/await is syntactic sugar built on top of Promises.
The 'async' keyword makes a function return a Promise.
The 'await' keyword pauses execution inside an async function
until the Promise resolves.

It makes async code look and behave like synchronous code,
making it much easier to read and debug."
*/

async function getUserData(userId) {
    try {
        // Pause here until getUser promise resolves
        const user = await getUser(userId);
        console.log("User:", user);
        
        // Pause here until getPosts promise resolves
        const posts = await getPosts(user.id);
        console.log("Posts:", posts);
        
        return { user, posts };
        
    } catch (error) {
        // Catches both getUser AND getPosts errors
        console.error("Error:", error.message);
        throw error; // Re-throw if needed
    }
}

getUserData(1)
    .then(data => console.log("All data:", data))
    .catch(err => console.error("Final catch:", err));

// ============================================
// PROMISE METHODS - Interview Must Know
// ============================================

// SETUP: Multiple async operations
const fetchUser = () => new Promise(res => setTimeout(() => res({ id: 1, name: "Alice" }), 1000));
const fetchPosts = () => new Promise(res => setTimeout(() => res(["Post1", "Post2"]), 2000));
const fetchSettings = () => new Promise(res => setTimeout(() => res({ theme: "dark" }), 500));

// 1. Promise.all - ALL must succeed, runs in PARALLEL
async function allExample() {
    console.time('Promise.all');
    
    const [user, posts, settings] = await Promise.all([
        fetchUser(),    // 1 second
        fetchPosts(),   // 2 seconds
        fetchSettings() // 0.5 seconds
    ]);
    // Total time: ~2 seconds (not 3.5! - they run in parallel)
    
    console.timeEnd('Promise.all'); // ~2000ms
    console.log(user, posts, settings);
}

// IF ONE FAILS → entire Promise.all fails
const fail = () => Promise.reject(new Error("Failed!"));
Promise.all([fetchUser(), fail(), fetchSettings()])
    .then(results => console.log(results))
    .catch(err => console.log("Failed:", err.message)); // "Failed: Failed!"

// 2. Promise.allSettled - ALL run, get EACH result (never rejects)
Promise.allSettled([fetchUser(), fail(), fetchSettings()])
    .then(results => {
        results.forEach((result, index) => {
            if (result.status === 'fulfilled') {
                console.log(`✓ #${index}:`, result.value);
            } else {
                console.log(`✗ #${index}:`, result.reason.message);
            }
        });
    });
// ✓ #0: { id: 1, name: "Alice" }
// ✗ #1: Failed!
// ✓ #2: { theme: "dark" }

// 3. Promise.race - FIRST to settle (fulfill OR reject) wins
const slow = new Promise(res => setTimeout(() => res("slow"), 3000));
const fast = new Promise(res => setTimeout(() => res("fast"), 1000));
const failing = new Promise((_, rej) => setTimeout(() => rej("error"), 500));

Promise.race([slow, fast])
    .then(winner => console.log("Winner:", winner)); // "fast"

// 4. Promise.any - FIRST to FULFILL wins (ignores rejections)
Promise.any([failing, fast, slow])
    .then(winner => console.log("First success:", winner)); // "fast"
// Only fails if ALL reject

// ============================================
// SEQUENTIAL vs PARALLEL - Common Interview Q
// ============================================

// SEQUENTIAL (one by one) - SLOWER
async function sequential() {
    console.time('sequential');
    const user = await fetchUser();     // Wait 1s
    const posts = await fetchPosts();   // Then wait 2s
    const settings = await fetchSettings(); // Then wait 0.5s
    console.timeEnd('sequential'); // ~3.5 seconds total!
    return { user, posts, settings };
}

// PARALLEL (all at once) - FASTER
async function parallel() {
    console.time('parallel');
    const [user, posts, settings] = await Promise.all([
        fetchUser(),    // All start at same time
        fetchPosts(),
        fetchSettings()
    ]);
    console.timeEnd('parallel'); // ~2 seconds (longest one)
    return { user, posts, settings };
}

/*
IMPORTANT INTERVIEW INSIGHT:
"Use sequential when:
- Operations depend on each other
- Second operation needs result of first

Use parallel when:
- Operations are independent
- You want maximum performance"
*/
```

---

## 8. Event Loop - Interview Explanation

### 🎯 How to Explain in Interview

**Simple One-Line Answer:**
> "The Event Loop is the mechanism that allows JavaScript to be non-blocking even though it's single-threaded. It continuously checks if the call stack is empty and processes callbacks from the queue."

```javascript
/*
INTERVIEWER: "Explain the JavaScript Event Loop"

PERFECT ANSWER (with visual description):

"JavaScript is single-threaded - it can only do ONE thing at a time.
The Event Loop is what allows it to handle async operations without blocking.

Here's how it works:

COMPONENTS:
1. Call Stack - where code executes (LIFO - last in, first out)
2. Web APIs (Browser) / C++ APIs (Node.js) - handle async tasks
3. Callback Queue (Macrotask Queue) - setTimeout, setInterval callbacks
4. Microtask Queue - Promise callbacks (HIGHER PRIORITY)
5. Event Loop - checks if stack is empty, moves tasks to stack

ORDER OF EXECUTION:
1. Run all synchronous code (Call Stack)
2. Process ALL microtasks (Promises)
3. Process ONE macrotask (setTimeout)
4. Go back to step 2 (check microtasks again)
5. Repeat
"
*/

// ============================================
// EVENT LOOP DEMONSTRATION
// ============================================

console.log("1. SYNCHRONOUS - Start");

setTimeout(() => {
    console.log("4. MACROTASK - setTimeout 0ms");
}, 0);

setTimeout(() => {
    console.log("5. MACROTASK - setTimeout 100ms");
}, 100);

Promise.resolve()
    .then(() => {
        console.log("3. MICROTASK - Promise 1");
        return Promise.resolve();
    })
    .then(() => {
        console.log("3b. MICROTASK - Promise 2 (chained)");
    });

console.log("2. SYNCHRONOUS - End");

/*
OUTPUT:
1. SYNCHRONOUS - Start
2. SYNCHRONOUS - End
3. MICROTASK - Promise 1
3b. MICROTASK - Promise 2 (chained)
4. MACROTASK - setTimeout 0ms
5. MACROTASK - setTimeout 100ms

WHY?
1. console.log runs synchronously → "1"
2. setTimeout(0) → goes to Web API, callback to macrotask queue
3. setTimeout(100) → goes to Web API, callback to macrotask queue
4. Promise.resolve().then → callback goes to MICROTASK queue
5. console.log runs synchronously → "2"
6. Call Stack empty → Event Loop checks MICROTASKS first!
7. Promise 1 runs → "3"
8. Promise 2 (chained) runs → "3b"
9. Microtask queue empty → check macrotask queue
10. setTimeout 0 runs → "4"
11. setTimeout 100 runs → "5"
*/

// ============================================
// MICROTASKS vs MACROTASKS
// ============================================

/*
MICROTASKS (High Priority - runs before any macrotask):
- Promise.then / .catch / .finally
- async/await
- queueMicrotask()
- MutationObserver (browser)

MACROTASKS (Lower Priority):
- setTimeout
- setInterval
- setImmediate (Node.js)
- I/O callbacks
- UI rendering (browser)
*/

// Tricky Example (common interview question!)
setTimeout(() => console.log("setTimeout 1"), 0);
setTimeout(() => console.log("setTimeout 2"), 0);

Promise.resolve()
    .then(() => {
        console.log("Promise 1");
        // This creates ANOTHER microtask
        return Promise.resolve("Promise 1 result");
    })
    .then((result) => {
        console.log("Promise 2 (chained from 1):", result);
    });

Promise.resolve().then(() => console.log("Promise 3"));

/*
Output:
Promise 1
Promise 3
Promise 2 (chained from 1): Promise 1 result
setTimeout 1
setTimeout 2

WHY?
- Both setTimeout callbacks go to macrotask queue
- Promise 1 AND Promise 3 .then callbacks go to microtask queue
- After sync code: process ALL microtasks first
  - Run "Promise 1" (also creates new microtask for chain)
  - Run "Promise 3" (microtask queue had both)
  - Run "Promise 2" (new microtask created by Promise 1 chain)
- Then process macrotasks
  - setTimeout 1
  - setTimeout 2
*/

// ============================================
// WHY EVENT LOOP MATTERS IN INTERVIEWS
// ============================================

// Example: Why blocking the event loop is bad
function blockingTask() {
    const start = Date.now();
    // Blocking operation - prevents EVERYTHING else
    while (Date.now() - start < 3000) {
        // Stuck for 3 seconds!
    }
    console.log("Blocking task done");
}

// Better: Use async operation
async function nonBlockingTask() {
    await new Promise(resolve => setTimeout(resolve, 3000));
    console.log("Non-blocking task done");
}

// In Node.js - Why you shouldn't do CPU intensive tasks in main thread:
// Use Worker Threads for heavy computation instead!
```

---

## 9. Array Methods - Interview Explanations

```javascript
/*
INTERVIEWER: "Explain map, filter, and reduce"

PERFECT ANSWER:
"These are three fundamental higher-order array methods in JavaScript:

map() - TRANSFORMS each element, always returns array of SAME length
filter() - SELECTS elements, returns array of SAME or FEWER elements  
reduce() - REDUCES array to SINGLE value (any type)

The key insight is that they're all pure functions - they don't 
modify the original array, they return new values."
*/

// ============================================
// PRACTICAL SCENARIO (Use this in interviews!)
// ============================================

const employees = [
    { id: 1, name: "Alice", dept: "Engineering", salary: 90000, active: true },
    { id: 2, name: "Bob", dept: "Marketing", salary: 65000, active: true },
    { id: 3, name: "Charlie", dept: "Engineering", salary: 110000, active: false },
    { id: 4, name: "Diana", dept: "Engineering", salary: 95000, active: true },
    { id: 5, name: "Eve", dept: "Marketing", salary: 70000, active: false }
];

// MAP: Get list of employee names
const names = employees.map(emp => emp.name);
console.log(names); // ["Alice", "Bob", "Charlie", "Diana", "Eve"]

// MAP: Transform objects
const employeeSummaries = employees.map(emp => ({
    id: emp.id,
    displayName: `${emp.name} (${emp.dept})`,
    annualBonus: emp.salary * 0.1
}));

// FILTER: Get only active employees
const activeEmployees = employees.filter(emp => emp.active);
console.log(activeEmployees.length); // 3

// FILTER: Get Engineering dept
const engineers = employees.filter(emp => emp.dept === "Engineering");

// REDUCE: Calculate total salary
const totalSalary = employees.reduce((total, emp) => {
    return total + emp.salary;
}, 0);
console.log(totalSalary); // 430000

// REDUCE: Group by department (very impressive in interviews!)
const byDepartment = employees.reduce((groups, emp) => {
    const dept = emp.dept;
    if (!groups[dept]) {
        groups[dept] = [];
    }
    groups[dept].push(emp.name);
    return groups;
}, {});
console.log(byDepartment);
// { Engineering: ["Alice", "Charlie", "Diana"], Marketing: ["Bob", "Eve"] }

// CHAINING (shows mastery!)
// "Find total salary of active engineers"
const activeEngineersSalary = employees
    .filter(emp => emp.active && emp.dept === "Engineering")
    .map(emp => emp.salary)
    .reduce((total, salary) => total + salary, 0);
console.log(activeEngineersSalary); // 185000 (Alice + Diana)

// ============================================
// OTHER IMPORTANT ARRAY METHODS
// ============================================

// FIND vs FILTER
const firstEngineer = employees.find(emp => emp.dept === "Engineering");
// Returns FIRST match or undefined

const allEngineers = employees.filter(emp => emp.dept === "Engineering");
// Returns ALL matches in array (possibly empty array)

// SOME vs EVERY
const hasInactiveEmp = employees.some(emp => !emp.active); // true (some inactive)
const allActive = employees.every(emp => emp.active);       // false (not all active)
const allHaveSalary = employees.every(emp => emp.salary > 0); // true

// FLAT and FLATMAP
const nested = [[1, 2], [3, 4], [5, [6, 7]]];
console.log(nested.flat());   // [1, 2, 3, 4, 5, [6, 7]]
console.log(nested.flat(2));  // [1, 2, 3, 4, 5, 6, 7]

const sentences = ["Hello World", "How Are You"];
const allWords = sentences.flatMap(s => s.split(" "));
console.log(allWords); // ["Hello", "World", "How", "Are", "You"]
// flatMap = map + flat(1) - useful for when map returns arrays

// SORT (tricky! - default converts to string)
const nums = [10, 1, 21, 2];
console.log(nums.sort());            // [1, 10, 2, 21] WRONG! (string sort)
console.log(nums.sort((a, b) => a - b)); // [1, 2, 10, 21] CORRECT (number sort)

// Sort objects by property
const sortedByName = [...employees].sort((a, b) => 
    a.name.localeCompare(b.name)
);

const sortedBySalaryDesc = [...employees].sort((a, b) => 
    b.salary - a.salary // descending
);

/*
COMMON INTERVIEW MISTAKE: sort() modifies original array!
Always use spread first: [...arr].sort()
*/

// REDUCE to implement other methods (shows deep understanding!)
// Implement map using reduce
function myMap(arr, callback) {
    return arr.reduce((result, item, index) => {
        result.push(callback(item, index, arr));
        return result;
    }, []);
}

// Implement filter using reduce
function myFilter(arr, callback) {
    return arr.reduce((result, item, index) => {
        if (callback(item, index, arr)) {
            result.push(item);
        }
        return result;
    }, []);
}

console.log(myMap([1, 2, 3], x => x * 2));      // [2, 4, 6]
console.log(myFilter([1, 2, 3, 4], x => x % 2 === 0)); // [2, 4]
```

---

## 10. Error Handling - Interview Explanation

```javascript
/*
INTERVIEWER: "How do you handle errors in JavaScript?"

PERFECT ANSWER:
"JavaScript has several mechanisms for error handling:

1. try-catch-finally for synchronous code
2. .catch() for Promise rejections
3. try-catch with async/await
4. Custom error classes for specific error types
5. Global error handlers for uncaught errors

The most important principle: errors should be handled as close
to where they occur as possible, but also allow critical errors
to propagate up for global handling."
*/

// ============================================
// BASIC ERROR HANDLING
// ============================================

// Synchronous error handling
function divide(a, b) {
    if (typeof a !== 'number' || typeof b !== 'number') {
        throw new TypeError("Both arguments must be numbers");
    }
    if (b === 0) {
        throw new Error("Cannot divide by zero");
    }
    return a / b;
}

try {
    console.log(divide(10, 2));  // 5
    console.log(divide(10, 0));  // throws!
} catch (error) {
    console.error(`Caught: ${error.name}: ${error.message}`);
    // Check error type
    if (error instanceof TypeError) {
        console.log("Type error - fix input validation");
    } else if (error instanceof RangeError) {
        console.log("Range error - value out of bounds");
    } else {
        console.log("General error:", error.message);
    }
} finally {
    console.log("This ALWAYS runs - good for cleanup");
}

// ============================================
// CUSTOM ERROR CLASSES (Shows expertise!)
// ============================================

class AppError extends Error {
    constructor(message, statusCode, errorCode) {
        super(message);
        this.name = this.constructor.name; // Error class name
        this.statusCode = statusCode;
        this.errorCode = errorCode;
        this.isOperational = true; // Known, expected error
    }
}

class ValidationError extends AppError {
    constructor(message, field) {
        super(message, 400, "VALIDATION_ERROR");
        this.field = field;
    }
}

class NotFoundError extends AppError {
    constructor(resource) {
        super(`${resource} not found`, 404, "NOT_FOUND");
    }
}

class AuthError extends AppError {
    constructor(message = "Unauthorized") {
        super(message, 401, "UNAUTHORIZED");
    }
}

// Using custom errors
function findUser(id) {
    const user = { id: 1, name: "John" };
    if (id !== 1) throw new NotFoundError("User");
    return user;
}

function validateEmail(email) {
    if (!email.includes("@")) {
        throw new ValidationError("Invalid email format", "email");
    }
}

try {
    validateEmail("not-an-email");
} catch (error) {
    if (error instanceof ValidationError) {
        console.log(`Validation failed on field '${error.field}': ${error.message}`);
        console.log(`Status: ${error.statusCode}`); // 400
    }
}

// ============================================
// ASYNC ERROR HANDLING
// ============================================

// WRONG WAY (missing error handling!)
async function wrongWay() {
    const data = await fetch('/api/data'); // Could throw!
    return data.json();
}

// CORRECT WAY 1: try-catch in async function
async function correctWay1() {
    try {
        const response = await fetch('/api/data');
        
        if (!response.ok) {
            throw new AppError(`HTTP Error: ${response.status}`, response.status);
        }
        
        return await response.json();
    } catch (error) {
        console.error("Error:", error.message);
        throw error; // Let caller handle or re-throw
    }
}

// CORRECT WAY 2: Catch on the call
correctWay1()
    .then(data => console.log(data))
    .catch(err => console.error("Handled:", err.message));

// ELEGANT: Error wrapper pattern
async function safeAsync(promise) {
    try {
        const data = await promise;
        return [data, null]; // [success, error]
    } catch (error) {
        return [null, error]; // [success, error]
    }
}

// Clean usage
async function getData() {
    const [data, error] = await safeAsync(fetch('/api/users'));
    
    if (error) {
        console.error("Failed to fetch:", error.message);
        return null;
    }
    
    return data;
}
```

---

# PART 2: NODE.JS CONCEPTS - INTERVIEW EXPLANATIONS

---

## 1. Node.js Architecture - Interview Explanation

```javascript
/*
INTERVIEWER: "What is Node.js and how does it work?"

PERFECT ANSWER:
"Node.js is a JavaScript runtime environment built on Chrome's 
V8 JavaScript engine. It allows JavaScript to run on the server side.

Key characteristics:
1. Single-threaded - but non-blocking via Event Loop
2. Non-blocking I/O - using callbacks, promises, async/await
3. Event-driven architecture
4. Great for I/O-heavy applications (APIs, real-time apps)
5. Not ideal for CPU-intensive tasks (use Worker Threads for those)

Architecture components:
- V8 Engine: Compiles JS to machine code
- libuv: Provides Event Loop, Thread Pool, async I/O
- Node.js APIs: fs, http, path, os, etc.
- npm: Package ecosystem
"
*/

// ============================================
// NON-BLOCKING I/O DEMONSTRATION
// ============================================

const fs = require('fs');
const https = require('https');

console.log("1. Script starts");

// Non-blocking file read
fs.readFile('package.json', 'utf8', (err, data) => {
    // This runs later, after file is read
    if (err) return console.error(err);
    console.log("3. File read complete");
});

// While file is being read, this executes immediately!
console.log("2. Script continues (not blocked!)");

/*
Output:
1. Script starts
2. Script continues (not blocked!)
3. File read complete

Contrast with BLOCKING (synchronous):
const data = fs.readFileSync('file.txt');  // BLOCKS everything!
console.log("This waits until file is read...");
*/

// ============================================
// NODE.JS vs BROWSER JS
// ============================================

/*
INTERVIEWER: "How is Node.js different from browser JavaScript?"

KEY DIFFERENCES:
┌─────────────────┬──────────────────┬───────────────────┐
│ Feature         │ Browser JS       │ Node.js           │
├─────────────────┼──────────────────┼───────────────────┤
│ Global object   │ window           │ global            │
│ DOM             │ Yes              │ No                │
│ File System     │ No               │ Yes (fs module)   │
│ Modules         │ ES modules       │ CommonJS + ES     │
│ Environment     │ Client-side      │ Server-side       │
│ console.log     │ Shown in browser │ Shown in terminal │
│ process         │ No               │ Yes               │
│ __dirname       │ No               │ Yes               │
└─────────────────┴──────────────────┴───────────────────┘
*/

// Node.js specific globals
console.log(__dirname);   // Current directory path
console.log(__filename);  // Current file path
console.log(process.env); // Environment variables
console.log(process.pid); // Process ID
console.log(process.version); // Node.js version

// Node.js process object
process.on('exit', (code) => {
    console.log(`Process exiting with code: ${code}`);
});

process.on('uncaughtException', (err) => {
    console.error('Uncaught Exception:', err.message);
    process.exit(1);
});

// ============================================
// WHEN TO USE NODE.JS (Interview question!)
// ============================================

/*
GOOD USE CASES for Node.js:
✓ REST APIs and microservices
✓ Real-time applications (chat, gaming, live updates)
✓ I/O intensive operations
✓ Streaming applications
✓ Serverless functions
✓ Command-line tools
✓ Proxy servers

NOT IDEAL for:
✗ Heavy CPU computation (image processing, ML)
✗ CPU-intensive number crunching
(Use Worker Threads or other languages for these)
*/
```

---

## 2. Modules - Interview Explanation

```javascript
/*
INTERVIEWER: "Explain CommonJS vs ES Modules"

PERFECT ANSWER:
"Node.js supports two module systems:

CommonJS (CJS) - Traditional Node.js:
- require() to import
- module.exports or exports to export
- Synchronous loading
- Dynamic imports allowed
- .js extension by default

ES Modules (ESM) - Modern JavaScript:
- import/export syntax
- Static imports (analyzed at compile time)
- Asynchronous loading
- Tree-shakeable (better for bundlers)
- Need .mjs extension OR 'type: module' in package.json

I prefer ES Modules for new projects because:
1. They're the JavaScript standard
2. Better tree-shaking
3. Static analysis possible
4. Both browsers and Node.js support them
"
*/

// ============================================
// COMMONJS - How it works internally
// ============================================

// Node.js wraps each file in a function:
/*
(function(exports, require, module, __filename, __dirname) {
    // Your code here
    
    const x = 10;
    module.exports = { x };
});
*/

// EXPORTING (math.js)
const add = (a, b) => a + b;
const subtract = (a, b) => a - b;
const PI = 3.14159;

// Method 1: module.exports (recommended)
module.exports = { add, subtract, PI };

// Method 2: exports shorthand (same as module.exports)
exports.multiply = (a, b) => a * b;
// Note: don't replace exports = {} (breaks reference!)

// Method 3: Named exports
module.exports.divide = (a, b) => {
    if (b === 0) throw new Error("Division by zero");
    return a / b;
};

// IMPORTING
const math = require('./math');             // Whole object
const { add, PI } = require('./math');     // Destructured
const add2 = require('./math').add;        // Direct access

// ============================================
// ES MODULES
// ============================================

// EXPORTING (utils.mjs or with "type":"module")
export const add3 = (a, b) => a + b;
export const PI2 = 3.14159;

export default class Calculator {
    add(a, b) { return a + b; }
    subtract(a, b) { return a - b; }
}

// Export all at once
const helpers = {
    formatDate: (date) => date.toLocaleDateString(),
    capitalize: (str) => str.charAt(0).toUpperCase() + str.slice(1)
};
export { helpers };

// IMPORTING
import Calculator from './utils.mjs';          // Default
import { add3, PI2 } from './utils.mjs';       // Named
import * as Utils from './utils.mjs';          // All as namespace
import Calculator2, { add3 as addNumbers } from './utils.mjs'; // Mixed + rename

// DYNAMIC IMPORTS (works in both CJS and ESM)
async function loadModule() {
    // Load only when needed (code splitting!)
    const { add } = await import('./math.mjs');
    return add(1, 2);
}

// Use case: Load heavy library only when needed
async function processImage(file) {
    if (file.type === 'image/png') {
        const { processPNG } = await import('./heavy-png-lib.mjs');
        return processPNG(file);
    }
}

// ============================================
// MODULE CACHING (Important for interviews!)
// ============================================

/*
INTERVIEWER: "What happens when you require the same module twice?"

ANSWER: "Node.js caches modules after first load.
Subsequent require() calls return the CACHED version.
This is why singletons work with modules in Node.js."
*/

// counter.js
let count = 0;
module.exports = {
    increment() { count++; },
    getCount() { return count; }
};

// app.js
const counter1 = require('./counter');
const counter2 = require('./counter');

counter1.increment();
counter1.increment();

console.log(counter1.getCount()); // 2
console.log(counter2.getCount()); // 2 (SAME module! cached!)
console.log(counter1 === counter2); // true (same reference)
```

---

## 3. File System - Interview Explanation

```javascript
/*
INTERVIEWER: "Explain sync vs async file operations in Node.js"

PERFECT ANSWER:
"Node.js provides both synchronous and asynchronous versions
of file system operations.

Synchronous (fs.readFileSync):
- Blocks the Event Loop while executing
- Simpler code (no callbacks needed)
- Should ONLY be used at startup/initialization
- Never use in request handlers!

Asynchronous (fs.readFile):
- Non-blocking, Event Loop continues
- Uses callbacks, promises, or async/await
- Should ALWAYS be used in production request handling

Modern approach: Use fs.promises (Promise-based) with async/await"
*/

const fs = require('fs');
const fsPromises = fs.promises;
const path = require('path');

// ============================================
// SYNC vs ASYNC - Why it matters
// ============================================

// WRONG in server context:
const express = require('express');
const app = express();

// ❌ NEVER do this in a request handler!
app.get('/data', (req, res) => {
    const data = fs.readFileSync('data.json'); // BLOCKS ALL OTHER REQUESTS!
    res.json(JSON.parse(data));
});

// ✅ ALWAYS do this:
app.get('/data-async', async (req, res) => {
    try {
        const data = await fsPromises.readFile('data.json', 'utf8');
        res.json(JSON.parse(data));
    } catch (err) {
        res.status(500).json({ error: err.message });
    }
});

// ============================================
// PRACTICAL FILE OPERATIONS
// ============================================

class FileService {
    // Read file
    static async readFile(filePath) {
        try {
            const data = await fsPromises.readFile(filePath, 'utf8');
            return JSON.parse(data);
        } catch (err) {
            if (err.code === 'ENOENT') {
                throw new Error(`File not found: ${filePath}`);
            }
            throw err;
        }
    }
    
    // Write file (atomic - uses temp file)
    static async writeFile(filePath, data) {
        const tempPath = `${filePath}.tmp`;
        try {
            await fsPromises.writeFile(tempPath, JSON.stringify(data, null, 2));
            await fsPromises.rename(tempPath, filePath); // Atomic move
        } catch (err) {
            // Clean up temp file if error
            try { await fsPromises.unlink(tempPath); } catch {}
            throw err;
        }
    }
    
    // Append to log file
    static async appendLog(filePath, message) {
        const logEntry = `${new Date().toISOString()} - ${message}\n`;
        await fsPromises.appendFile(filePath, logEntry);
    }
    
    // Ensure directory exists
    static async ensureDir(dirPath) {
        await fsPromises.mkdir(dirPath, { recursive: true });
    }
    
    // List files with filter
    static async listFiles(dirPath, extension = null) {
        const files = await fsPromises.readdir(dirPath);
        
        if (extension) {
            return files.filter(file => path.extname(file) === extension);
        }
        return files;
    }
    
    // Watch for file changes
    static watchFile(filePath, callback) {
        const watcher = fs.watch(filePath, (eventType, filename) => {
            console.log(`File ${filename} ${eventType}`);
            callback(eventType, filename);
        });
        
        return () => watcher.close(); // Return unwatch function
    }
}

// Usage
async function main() {
    // Write data
    await FileService.writeFile('./data.json', {
        users: [{ id: 1, name: "John" }],
        timestamp: Date.now()
    });
    
    // Read data
    const data = await FileService.readFile('./data.json');
    console.log(data);
    
    // Log operations
    await FileService.appendLog('./app.log', 'Application started');
    
    // List JS files
    const jsFiles = await FileService.listFiles('.', '.js');
    console.log('JS files:', jsFiles);
}

// ============================================
// STREAMS FOR LARGE FILES (Important!)
// ============================================

/*
INTERVIEWER: "How would you handle a very large file in Node.js?"

ANSWER: "Use streams instead of reading the entire file at once.
Streams process data in chunks, keeping memory usage low
regardless of file size."
*/

async function processLargeFile(inputPath, outputPath) {
    return new Promise((resolve, reject) => {
        const readStream = fs.createReadStream(inputPath, { 
            encoding: 'utf8',
            highWaterMark: 64 * 1024 // 64KB chunks
        });
        
        const writeStream = fs.createWriteStream(outputPath);
        
        let lineCount = 0;
        let buffer = '';
        
        readStream.on('data', (chunk) => {
            buffer += chunk;
            const lines = buffer.split('\n');
            buffer = lines.pop(); // Keep incomplete line in buffer
            lineCount += lines.length;
        });
        
        readStream.on('end', () => {
            if (buffer) lineCount++; // Count last line
            console.log(`Processed ${lineCount} lines`);
            resolve(lineCount);
        });
        
        // Pipe to output
        readStream.pipe(writeStream);
        
        readStream.on('error', reject);
        writeStream.on('error', reject);
        writeStream.on('finish', resolve);
    });
}
```

---

## 4. Express.js - Interview Explanation

```javascript
/*
INTERVIEWER: "What is Express.js and what are its key concepts?"

PERFECT ANSWER:
"Express is a minimal and flexible Node.js web framework.

Key concepts:
1. Middleware - functions that process requests in a pipeline
2. Routing - defining endpoints (URL + HTTP method)
3. Request/Response objects - enhanced versions of Node's http
4. Template engines - for server-side rendering
5. Error handling - centralized error management

Express follows the middleware pipeline pattern:
Request → Middleware 1 → Middleware 2 → Route Handler → Response
                      → Error Handler (if error occurs)
"
*/

const express = require('express');
const app = express();

// ============================================
// MIDDLEWARE - Deep Explanation
// ============================================

/*
Middleware is the core concept of Express.
Each middleware is a function with (req, res, next) parameters.

Middleware can:
1. Execute any code
2. Modify req and res objects
3. End the request-response cycle
4. Call the NEXT middleware

If middleware doesn't call next() or send response → REQUEST HANGS!
*/

// MIDDLEWARE TYPES WITH EXPLANATION:

// 1. Application-level middleware
app.use((req, res, next) => {
    console.log(`[${new Date().toISOString()}] ${req.method} ${req.url}`);
    next(); // Must call next!
});

// 2. Route-specific middleware
function validateId(req, res, next) {
    const { id } = req.params;
    if (!id || isNaN(Number(id))) {
        return res.status(400).json({ error: "Invalid ID" });
        // Don't call next - stop here
    }
    req.validatedId = Number(id);
    next(); // Pass to next middleware
}

// 3. Error-handling middleware (MUST have 4 params)
function errorHandler(err, req, res, next) {
    console.error(err.stack);
    res.status(err.statusCode || 500).json({
        error: err.message || 'Internal Server Error'
    });
}

// ============================================
// ROUTING - Complete Example
// ============================================

const router = express.Router();

// Middleware for all routes in this router
router.use((req, res, next) => {
    console.log('Router-level middleware');
    next();
});

// CRUD operations for users
router.get('/', async (req, res, next) => {
    try {
        const { page = 1, limit = 10, search } = req.query;
        
        // Simulated database query
        const users = [
            { id: 1, name: "Alice", email: "alice@test.com" },
            { id: 2, name: "Bob", email: "bob@test.com" }
        ];
        
        res.json({
            success: true,
            data: users,
            pagination: { page, limit, total: users.length }
        });
    } catch (err) {
        next(err); // Pass to error handler
    }
});

router.get('/:id', validateId, async (req, res, next) => {
    try {
        const user = { id: req.validatedId, name: "John" };
        
        if (!user) {
            return res.status(404).json({ error: "User not found" });
        }
        
        res.json({ success: true, data: user });
    } catch (err) {
        next(err);
    }
});

router.post('/', express.json(), async (req, res, next) => {
    try {
        const { name, email, password } = req.body;
        
        // Validation
        const errors = [];
        if (!name) errors.push("Name is required");
        if (!email) errors.push("Email is required");
        if (!password || password.length < 8) errors.push("Password min 8 chars");
        
        if (errors.length > 0) {
            return res.status(400).json({ error: errors.join(", ") });
        }
        
        const newUser = { id: Date.now(), name, email };
        res.status(201).json({ success: true, data: newUser });
    } catch (err) {
        next(err);
    }
});

router.put('/:id', validateId, express.json(), async (req, res, next) => {
    try {
        const updated = { id: req.validatedId, ...req.body };
        res.json({ success: true, data: updated });
    } catch (err) {
        next(err);
    }
});

router.delete('/:id', validateId, async (req, res, next) => {
    try {
        res.json({ success: true, message: `User ${req.validatedId} deleted` });
    } catch (err) {
        next(err);
    }
});

// Mount router
app.use('/api/users', router);

// Error handler MUST be last
app.use(errorHandler);

app.listen(3000, () => console.log('Server on port 3000'));

// ============================================
// MIDDLEWARE EXECUTION ORDER (Critical concept!)
// ============================================

/*
INTERVIEWER: "What is the order of middleware execution?"

ANSWER: "Middleware executes in the ORDER it's defined.
This is critical for:
1. Body parsers must come before routes that need req.body
2. Authentication must come before protected routes
3. Error handlers must come LAST
4. Logging should come FIRST

Example flow:
Request → cors() → helmet() → logger → bodyParser → auth → route → errorHandler
"
*/

// Correct order
app.use(require('cors')());         // 1. CORS first
app.use(require('helmet')());       // 2. Security headers
app.use(express.json());            // 3. Parse body
app.use(express.urlencoded({ extended: true }));
app.use(logger);                    // 4. Log request
app.use('/api', authMiddleware);    // 5. Authenticate
app.use('/api/users', userRouter);  // 6. Routes
app.use(notFoundHandler);          // 7. 404 handler
app.use(errorHandler);             // 8. Error handler (MUST be last!)
```

---

## 5. Authentication & JWT - Interview Explanation

```javascript
/*
INTERVIEWER: "Explain JWT authentication"

PERFECT ANSWER:
"JWT (JSON Web Token) is a compact, self-contained token format
used for authentication and authorization.

Structure: header.payload.signature
- Header: algorithm type (HS256)
- Payload: claims (user data, expiry)
- Signature: ensures token wasn't tampered with

Authentication Flow:
1. User logs in with credentials
2. Server verifies credentials
3. Server creates JWT with user data
4. Client stores JWT (localStorage or httpOnly cookie)
5. Client sends JWT in Authorization header
6. Server verifies JWT on each request
7. Server extracts user data from JWT (no DB lookup needed!)

Advantages over sessions:
- Stateless - no server storage needed
- Scalable - works across multiple servers
- Self-contained - user info in token

Disadvantages:
- Cannot invalidate before expiry (use short expiry + refresh tokens)
- Larger than session cookies
- Secret key must be kept secure
"
*/

const jwt = require('jsonwebtoken');
const bcrypt = require('bcryptjs');

// ============================================
// JWT IMPLEMENTATION EXPLAINED
// ============================================

const JWT_SECRET = process.env.JWT_SECRET; // NEVER hardcode!
const JWT_REFRESH_SECRET = process.env.JWT_REFRESH_SECRET;

class AuthService {
    // Hash password (NEVER store plain text!)
    static async hashPassword(password) {
        /*
        bcrypt.hash(password, saltRounds):
        - saltRounds: 10-12 is recommended
        - Higher = more secure but slower
        - Each additional round doubles computation time
        */
        const saltRounds = 12;
        return await bcrypt.hash(password, saltRounds);
    }
    
    static async verifyPassword(password, hash) {
        return await bcrypt.compare(password, hash);
    }
    
    // Generate short-lived access token
    static generateAccessToken(user) {
        return jwt.sign(
            // Payload (don't put sensitive data here - it's base64 encoded, not encrypted!)
            { 
                id: user.id, 
                email: user.email, 
                role: user.role 
            },
            JWT_SECRET,
            { 
                expiresIn: '15m',      // Short lived
                algorithm: 'HS256',    // Algorithm
                issuer: 'my-app'       // Who issued it
            }
        );
    }
    
    // Generate long-lived refresh token
    static generateRefreshToken(userId) {
        return jwt.sign(
            { id: userId },
            JWT_REFRESH_SECRET,
            { expiresIn: '7d' }
        );
    }
    
    // Verify token
    static verifyToken(token, secret = JWT_SECRET) {
        try {
            return jwt.verify(token, secret);
        } catch (error) {
            if (error.name === 'TokenExpiredError') {
                throw new Error('TOKEN_EXPIRED');
            }
            if (error.name === 'JsonWebTokenError') {
                throw new Error('INVALID_TOKEN');
            }
            throw error;
        }
    }
    
    // Decode without verification (to get expiry info)
    static decodeToken(token) {
        return jwt.decode(token); // Don't trust this for auth!
    }
}

// ============================================
// AUTH MIDDLEWARE - Explained
// ============================================

const authenticate = async (req, res, next) => {
    try {
        // 1. Get token from header
        const authHeader = req.headers.authorization;
        
        if (!authHeader) {
            return res.status(401).json({ 
                error: "No authorization header",
                code: "NO_TOKEN"
            });
        }
        
        // 2. Check format "Bearer <token>"
        const parts = authHeader.split(' ');
        if (parts.length !== 2 || parts[0] !== 'Bearer') {
            return res.status(401).json({ 
                error: "Invalid token format. Use 'Bearer <token>'",
                code: "INVALID_FORMAT"
            });
        }
        
        const token = parts[1];
        
        // 3. Verify token
        const decoded = AuthService.verifyToken(token);
        
        // 4. Attach user to request
        req.user = decoded;
        
        next();
    } catch (error) {
        if (error.message === 'TOKEN_EXPIRED') {
            return res.status(401).json({ 
                error: "Token expired, please refresh",
                code: "TOKEN_EXPIRED"
            });
        }
        res.status(401).json({ error: "Invalid token", code: "INVALID_TOKEN" });
    }
};

// Role-based authorization
const authorize = (...allowedRoles) => {
    return (req, res, next) => {
        if (!req.user) {
            return res.status(401).json({ error: "Not authenticated" });
        }
        
        if (!allowedRoles.includes(req.user.role)) {
            return res.status(403).json({ 
                error: `Access denied. Required roles: ${allowedRoles.join(', ')}`,
                code: "INSUFFICIENT_PERMISSIONS"
            });
        }
        
        next();
    };
};

// Usage
app.get('/admin', authenticate, authorize('admin'), (req, res) => {
    res.json({ message: "Admin only content" });
});

app.get('/dashboard', authenticate, authorize('admin', 'user'), (req, res) => {
    res.json({ message: `Welcome ${req.user.email}` });
});

// ============================================
// REFRESH TOKEN PATTERN (Shows expertise!)
// ============================================

/*
INTERVIEWER: "How do you handle token expiration?"

ANSWER: "Use the refresh token pattern:
1. Issue short-lived access token (15min) + long-lived refresh token (7d)
2. Store refresh token securely (httpOnly cookie or DB)
3. When access token expires, use refresh token to get new access token
4. If refresh token expired, user must log in again
This balances security and user experience."
*/

const refreshTokens = new Set(); // In production: store in Redis/DB

app.post('/auth/refresh', (req, res) => {
    const { refreshToken } = req.body;
    
    if (!refreshToken) {
        return res.status(401).json({ error: "Refresh token required" });
    }
    
    // Check if token is in our allowed list
    if (!refreshTokens.has(refreshToken)) {
        return res.status(403).json({ error: "Invalid refresh token" });
    }
    
    try {
        const decoded = AuthService.verifyToken(refreshToken, JWT_REFRESH_SECRET);
        
        // Generate new access token
        const newAccessToken = AuthService.generateAccessToken({
            id: decoded.id,
            email: decoded.email,
            role: decoded.role
        });
        
        res.json({ accessToken: newAccessToken });
    } catch (error) {
        refreshTokens.delete(refreshToken); // Remove invalid token
        res.status(403).json({ error: "Invalid or expired refresh token" });
    }
});

app.post('/auth/logout', authenticate, (req, res) => {
    const { refreshToken } = req.body;
    refreshTokens.delete(refreshToken); // Invalidate refresh token
    res.json({ message: "Logged out successfully" });
});
```

---

## 6. Node.js Event Loop Specifics

```javascript
/*
INTERVIEWER: "What is the Node.js Event Loop? How is it different from browser?"

PERFECT ANSWER:
"Node.js Event Loop has specific PHASES unlike the browser:

PHASES (in order):
1. timers - setTimeout, setInterval callbacks
2. pending callbacks - I/O callbacks deferred to next loop
3. idle, prepare - internal use only
4. poll - retrieve I/O events, execute I/O callbacks
5. check - setImmediate callbacks
6. close callbacks - close events (socket.close, etc.)

process.nextTick() runs AFTER current operation, 
BEFORE moving to next phase - highest priority!

PRIORITY ORDER:
process.nextTick > Promises (microtasks) > setImmediate > setTimeout(0)
"
*/

// ============================================
// NODE.JS EVENT LOOP PHASES DEMONSTRATION
// ============================================

const fs = require('fs');

console.log('1. Start - synchronous');

// Timers phase
setTimeout(() => console.log('6. setTimeout 0ms'), 0);
setTimeout(() => console.log('7. setTimeout 100ms'), 100);

// setImmediate - check phase (after I/O)
setImmediate(() => console.log('5. setImmediate'));

// I/O callback (poll phase)
fs.readFile(__filename, () => {
    console.log('4. I/O callback - file read');
    
    // Inside I/O callback, setImmediate runs BEFORE setTimeout!
    setTimeout(() => console.log('8. setTimeout inside I/O'), 0);
    setImmediate(() => console.log('4a. setImmediate inside I/O'));
});

// nextTick - runs before EVERYTHING else after current operation
process.nextTick(() => console.log('2. nextTick 1'));
process.nextTick(() => console.log('3. nextTick 2'));

// Promises (microtasks)
Promise.resolve().then(() => console.log('2.5. Promise'));

console.log('1b. End - synchronous');

/*
Output:
1. Start - synchronous
1b. End - synchronous
2. nextTick 1          ← nextTick before Promises!
3. nextTick 2
2.5. Promise           ← Promise microtask
6. setTimeout 0ms      ← Timers phase
5. setImmediate        ← Check phase (before I/O completes)
4. I/O callback        ← Poll phase
4a. setImmediate inside I/O  ← setImmediate runs first inside I/O
8. setTimeout inside I/O
7. setTimeout 100ms
*/

// ============================================
// PRACTICAL: process.nextTick Use Cases
// ============================================

/*
Use process.nextTick when:
1. You want to ensure callback runs before any I/O
2. Allow event listeners to be set up before emitting events
3. Error handling that should run before I/O
*/

// GOOD use of nextTick: Emit event after constructor
const EventEmitter = require('events');

class AsyncEventEmitter extends EventEmitter {
    constructor() {
        super();
        
        // Using nextTick allows caller to set up listeners!
        process.nextTick(() => {
            this.emit('ready', 'Initialized!');
        });
    }
}

const emitter = new AsyncEventEmitter();

// This listener is set up BEFORE nextTick runs!
emitter.on('ready', (msg) => {
    console.log('Ready:', msg); // Works!
});

// WITHOUT nextTick, the emit would happen during constructor
// BEFORE the listener could be set up!
```

---

## 7. Performance & Security Best Practices

```javascript
/*
INTERVIEWER: "How do you optimize a Node.js application?"

PERFECT ANSWER:
"Performance optimization strategies:

1. Use async/await everywhere (never block event loop)
2. Implement caching (Redis for API responses, in-memory for computed data)
3. Use connection pooling for databases
4. Implement rate limiting
5. Use compression for responses
6. Use streams for large data
7. Cluster module to use all CPU cores
8. Worker Threads for CPU-intensive tasks
9. Implement proper indexing in databases
10. Monitor with tools like PM2, New Relic

Security best practices:
1. Input validation and sanitization
2. Use Helmet.js (security headers)
3. Rate limiting
4. HTTPS only
5. Environment variables for secrets
6. Principle of least privilege
7. Keep dependencies updated
"
*/

const express = require('express');
const helmet = require('helmet');
const rateLimit = require('express-rate-limit');
const mongoSanitize = require('express-mongo-sanitize');
const xss = require('xss-clean');
const compression = require('compression');
const cors = require('cors');

const app = express();

// ============================================
// SECURITY MIDDLEWARE STACK
// ============================================

// 1. CORS - Control who can access your API
app.use(cors({
    origin: process.env.ALLOWED_ORIGINS?.split(',') || ['http://localhost:3000'],
    methods: ['GET', 'POST', 'PUT', 'DELETE'],
    allowedHeaders: ['Content-Type', 'Authorization'],
    credentials: true,
    maxAge: 86400 // Cache preflight for 24 hours
}));

// 2. Helmet - Set security HTTP headers
app.use(helmet({
    contentSecurityPolicy: {
        directives: {
            defaultSrc: ["'self'"],
            styleSrc: ["'self'", "'unsafe-inline'"]
        }
    },
    hsts: {
        maxAge: 31536000, // 1 year
        includeSubDomains: true,
        preload: true
    }
}));

// 3. Rate Limiting
const limiter = rateLimit({
    windowMs: 15 * 60 * 1000, // 15 minutes
    max: 100,                  // Max 100 requests per window
    standardHeaders: true,
    legacyHeaders: false,
    message: {
        status: 'error',
        message: 'Too many requests, please try again in 15 minutes'
    }
});

const authLimiter = rateLimit({
    windowMs: 60 * 60 * 1000, // 1 hour
    max: 5,                    // Only 5 login attempts per hour
    message: { error: 'Too many login attempts, account may be locked' }
});

app.use('/api/', limiter);
app.use('/api/auth/login', authLimiter);

// 4. Body Parser with limits
app.use(express.json({ limit: '10kb' })); // Limit request body size
app.use(express.urlencoded({ extended: true, limit: '10kb' }));

// 5. Data Sanitization
app.use(mongoSanitize()); // Prevent NoSQL injection
app.use(xss());           // Prevent XSS attacks

// 6. Compression
app.use(compression({
    level: 6,
    filter: (req, res) => {
        // Don't compress for very small responses
        if (req.headers['x-no-compression']) return false;
        return compression.filter(req, res);
    }
}));

// ============================================
// PERFORMANCE: CACHING
// ============================================

const NodeCache = require('node-cache');
const cache = new NodeCache({ stdTTL: 300, checkperiod: 60 }); // 5 min default

function cacheMiddleware(duration = 300) {
    return (req, res, next) => {
        // Only cache GET requests
        if (req.method !== 'GET') return next();
        
        const key = `cache:${req.originalUrl}`;
        const cached = cache.get(key);
        
        if (cached) {
            res.setHeader('X-Cache', 'HIT');
            return res.json(cached);
        }
        
        res.setHeader('X-Cache', 'MISS');
        
        // Override res.json to cache response
        const originalJson = res.json.bind(res);
        res.json = (data) => {
            cache.set(key, data, duration);
            originalJson(data);
        };
        
        next();
    };
}

// Usage
app.get('/api/products', cacheMiddleware(600), async (req, res) => {
    // Expensive database query - only runs on cache miss!
    const products = await Product.find().lean();
    res.json(products);
});
```

---

## 8. Testing - Interview Explanation

```javascript
/*
INTERVIEWER: "How do you approach testing in Node.js?"

PERFECT ANSWER:
"I follow the testing pyramid:
1. Unit tests - test individual functions/classes (70%)
   - Fast, isolated, mock dependencies
   
2. Integration tests - test multiple components together (20%)
   - Test API endpoints, database operations
   
3. E2E tests - test full user flows (10%)
   - Slowest, most realistic

I use Jest for unit and integration tests because:
- Built-in mocking
- Snapshot testing
- Coverage reports
- Great async support

For HTTP testing I use Supertest.
For database tests I use separate test database, 
clean up after each test."
*/

// ============================================
// UNIT TESTING - Explained
// ============================================

// service/userService.js
class UserService {
    constructor(userRepository, emailService) {
        this.userRepository = userRepository;
        this.emailService = emailService;
    }
    
    async createUser(userData) {
        // Validate
        if (!userData.email) throw new Error("Email required");
        if (!userData.name) throw new Error("Name required");
        
        // Check duplicate
        const existing = await this.userRepository.findByEmail(userData.email);
        if (existing) throw new Error("Email already in use");
        
        // Create
        const user = await this.userRepository.create(userData);
        
        // Send welcome email
        await this.emailService.sendWelcome(user.email, user.name);
        
        return user;
    }
}

// __tests__/userService.test.js
const UserService = require('../service/userService');

describe('UserService', () => {
    let userService;
    let mockUserRepo;
    let mockEmailService;
    
    beforeEach(() => {
        // Create mock implementations
        mockUserRepo = {
            findByEmail: jest.fn(),
            create: jest.fn()
        };
        
        mockEmailService = {
            sendWelcome: jest.fn().mockResolvedValue(true)
        };
        
        userService = new UserService(mockUserRepo, mockEmailService);
    });
    
    afterEach(() => {
        jest.clearAllMocks();
    });
    
    describe('createUser()', () => {
        const validUser = { name: "John", email: "john@test.com" };
        
        it('should create user successfully', async () => {
            // Arrange
            mockUserRepo.findByEmail.mockResolvedValue(null);
            mockUserRepo.create.mockResolvedValue({ id: 1, ...validUser });
            
            // Act
            const result = await userService.createUser(validUser);
            
            // Assert
            expect(result.id).toBe(1);
            expect(result.email).toBe(validUser.email);
            expect(mockUserRepo.findByEmail).toHaveBeenCalledWith(validUser.email);
            expect(mockUserRepo.create).toHaveBeenCalledWith(validUser);
            expect(mockEmailService.sendWelcome).toHaveBeenCalledWith(
                validUser.email, 
                validUser.name
            );
        });
        
        it('should throw if email already exists', async () => {
            // Arrange
            mockUserRepo.findByEmail.mockResolvedValue({ id: 99, ...validUser });
            
            // Act & Assert
            await expect(userService.createUser(validUser))
                .rejects
                .toThrow("Email already in use");
            
            // Ensure create was NOT called
            expect(mockUserRepo.create).not.toHaveBeenCalled();
            expect(mockEmailService.sendWelcome).not.toHaveBeenCalled();
        });
        
        it('should throw if email missing', async () => {
            await expect(userService.createUser({ name: "John" }))
                .rejects
                .toThrow("Email required");
        });
        
        it('should throw if name missing', async () => {
            await expect(userService.createUser({ email: "test@test.com" }))
                .rejects
                .toThrow("Name required");
        });
    });
});

// ============================================
// INTEGRATION TESTING - API Routes
// ============================================

const request = require('supertest');
const mongoose = require('mongoose');
const app = require('../app');

describe('User API - Integration Tests', () => {
    let authToken;
    
    beforeAll(async () => {
        // Connect to test database
        await mongoose.connect(process.env.MONGODB_TEST_URI);
    });
    
    afterAll(async () => {
        await mongoose.connection.dropDatabase();
        await mongoose.connection.close();
    });
    
    afterEach(async () => {
        // Clean up after each test
        await mongoose.connection.collection('users').deleteMany({});
    });
    
    describe('POST /api/auth/register', () => {
        it('should register successfully with valid data', async () => {
            const response = await request(app)
                .post('/api/auth/register')
                .send({
                    name: 'Test User',
                    email: 'test@example.com',
                    password: 'SecurePass123!'
                })
                .expect('Content-Type', /json/)
                .expect(201);
            
            expect(response.body.success).toBe(true);
            expect(response.body.token).toBeDefined();
            expect(response.body.user.email).toBe('test@example.com');
            expect(response.body.user.password).toBeUndefined(); // Never expose!
        });
        
        it('should return 400 for duplicate email', async () => {
            const userData = {
                name: 'Test User',
                email: 'duplicate@example.com',
                password: 'SecurePass123!'
            };
            
            // First registration
            await request(app).post('/api/auth/register').send(userData);
            
            // Second registration with same email
            const response = await request(app)
                .post('/api/auth/register')
                .send(userData)
                .expect(400);
            
            expect(response.body.error).toContain('email');
        });
    });
    
    describe('GET /api/users (Protected)', () => {
        beforeEach(async () => {
            // Login to get token
            await request(app).post('/api/auth/register').send({
                name: 'Admin',
                email: 'admin@test.com',
                password: 'AdminPass123!',
                role: 'admin'
            });
            
            const loginRes = await request(app).post('/api/auth/login').send({
                email: 'admin@test.com',
                password: 'AdminPass123!'
            });
            
            authToken = loginRes.body.token;
        });
        
        it('should return users with valid token', async () => {
            const response = await request(app)
                .get('/api/users')
                .set('Authorization', `Bearer ${authToken}`)
                .expect(200);
            
            expect(response.body.data).toBeInstanceOf(Array);
        });
        
        it('should return 401 without token', async () => {
            await request(app)
                .get('/api/users')
                .expect(401);
        });
    });
});
```

---

## Final: Top 20 Interview Questions & Answers

```javascript
/*
================================================================
TOP 20 JAVASCRIPT/NODE.JS INTERVIEW Q&A
================================================================
*/

// Q1: What is the output?
var a = 1;
function test() {
    console.log(a); // ?
    var a = 2;
    console.log(a); // ?
}
test();
// Answer: undefined, 2 (var hoisting inside function)

// Q2: What is the output?
console.log(1 + "2" + "3"); // "123" (1 becomes string)
console.log(1 + 2 + "3");   // "33" (1+2=3, then "3"+"3"="33")
console.log("3" - 2);       // 1 (string to number)

// Q3: What is the output?
const obj = {};
obj.a = 1;
Object.freeze(obj);
obj.a = 2; // Silently fails!
obj.b = 3; // Silently fails!
console.log(obj); // { a: 1 }

// Q4: Implement debounce
function debounce(fn, delay) {
    let timer;
    return function(...args) {
        clearTimeout(timer);
        timer = setTimeout(() => fn.apply(this, args), delay);
    };
}

// Q5: Implement curry
function curry(fn) {
    return function curried(...args) {
        if (args.length >= fn.length) {
            return fn(...args);
        }
        return (...more) => curried(...args, ...more);
    };
}

// Q6: Deep clone without library
function deepClone(obj) {
    if (obj === null || typeof obj !== 'object') return obj;
    if (obj instanceof Date) return new Date(obj);
    if (Array.isArray(obj)) return obj.map(item => deepClone(item));
    return Object.fromEntries(
        Object.entries(obj).map(([k, v]) => [k, deepClone(v)])
    );
}

// Q7: Flatten nested array
function flatten(arr) {
    return arr.reduce((flat, item) => {
        return Array.isArray(item) 
            ? flat.concat(flatten(item)) 
            : flat.concat(item);
    }, []);
}
// Or: arr.flat(Infinity)

// Q8: Remove duplicates from array
const removeDuplicates = arr => [...new Set(arr)];
// Or:
const removeDups = arr => arr.filter((item, index) => arr.indexOf(item) === index);

// Q9: What is the output?
for (var i = 0; i < 3; i++) {
    setTimeout(() => console.log(i), 0);
}
// Answer: 3, 3, 3

// Fix:
for (let i = 0; i < 3; i++) {
    setTimeout(() => console.log(i), 0);
}
// Answer: 0, 1, 2

// Q10: Difference between Promise.all and Promise.allSettled?
// Promise.all: fails fast - rejects if ANY promise rejects
// Promise.allSettled: always resolves - gives status of each promise

// Q11: What is async/await?
// Syntactic sugar over Promises. async function always returns Promise.
// await pauses execution of async function until Promise resolves.

// Q12: What is event delegation?
// Instead of adding listeners to each child,
// add ONE listener to parent and use event.target to identify source
document.getElementById('list').addEventListener('click', (e) => {
    if (e.target.tagName === 'LI') {
        console.log('Clicked:', e.target.textContent);
    }
});

// Q13: Implement a function to check palindrome
function isPalindrome(str) {
    const cleaned = str.toLowerCase().replace(/[^a-z0-9]/g, '');
    return cleaned === cleaned.split('').reverse().join('');
}

// Q14: What is the difference between null and undefined?
// null: intentional absence of value (developer sets it)
// undefined: variable declared but never assigned (JS default)
// typeof null === 'object' (known bug)
// typeof undefined === 'undefined'
// null == undefined → true
// null === undefined → false

// Q15: What are WeakMap and WeakSet?
// Regular Map/Set: prevent garbage collection (memory leaks possible)
// WeakMap/WeakSet: keys/values are weakly referenced
// - Objects can be garbage collected even if they're in WeakMap/WeakSet
// - No iteration, no size property
// - Use case: Storing metadata about DOM nodes

const privateData = new WeakMap();
class MyClass {
    constructor(data) {
        privateData.set(this, { secret: data }); // Private!
    }
    getSecret() {
        return privateData.get(this).secret;
    }
}

// Q16: Explain Symbol in JavaScript
// Symbol: unique, immutable identifier
const sym1 = Symbol('description');
const sym2 = Symbol('description');
console.log(sym1 === sym2); // false - always unique!

// Use case: Avoid property name collisions
const ID = Symbol('id');
const user = { [ID]: 123, name: "John" };
console.log(user[ID]); // 123

// Q17: What is the Proxy object?
const handler = {
    get(target, prop) {
        return prop in target ? target[prop] : `Property '${prop}' not found`;
    },
    set(target, prop, value) {
        if (typeof value !== 'string') throw new TypeError('Only strings!');
        target[prop] = value;
        return true;
    }
};

const proxy = new Proxy({}, handler);
proxy.name = "John";
console.log(proxy.name);    // "John"
console.log(proxy.missing); // "Property 'missing' not found"
// proxy.age = 25; // TypeError!

// Q18: Node.js vs Express.js
// Node.js: Runtime environment (lower level HTTP handling)
// Express.js: Framework built on Node.js (routing, middleware, easier API dev)

// Q19: What is middleware in Express?
// Functions with (req, res, next) that execute in sequence
// Can modify request/response, end request cycle, call next middleware

// Q20: How to handle async errors in Express?
const asyncHandler = fn => (req, res, next) => {
    Promise.resolve(fn(req, res, next)).catch(next);
};

// Usage
app.get('/users', asyncHandler(async (req, res) => {
    const users = await User.find(); // If this throws, caught by errorHandler!
    res.json(users);
}));

// Error handler catches it
app.use((err, req, res, next) => {
    res.status(500).json({ error: err.message });
});
```

---

## Summary Cheat Sheet

```javascript
/*
================================================================
INTERVIEW CHEAT SHEET - Key Points to Remember
================================================================

JAVASCRIPT FUNDAMENTALS:
✓ var = function scope, let/const = block scope
✓ Hoisting: var → undefined, let/const → TDZ, functions → fully hoisted
✓ Closure = function + remembered lexical scope
✓ 'this' depends on HOW function is called (not where defined)
✓ Arrow functions have NO own 'this', 'arguments', or prototype
✓ Prototype chain: object → prototype → Object.prototype → null

ASYNC JAVASCRIPT:
✓ Event Loop: Sync → nextTick → Promises → setTimeout
✓ Promise states: pending → fulfilled/rejected
✓ Promise.all = all succeed or fail fast
✓ Promise.allSettled = all run, get each status
✓ Promise.race = first to settle wins
✓ async/await = syntactic sugar over Promises
✓ Error handling: try/catch with async/await

ES6+ FEATURES:
✓ Destructuring: const {a, b} = obj; const [x, y] = arr;
✓ Spread: ...obj copies, ...arr copies (shallow!)
✓ Rest: function(first, ...rest) collects remaining
✓ Template literals: `${expression}`
✓ Optional chaining: obj?.prop?.nested
✓ Nullish coalescing: value ?? 'default'

NODE.JS:
✓ Single-threaded but non-blocking via Event Loop
✓ libuv handles async I/O with thread pool
✓ CommonJS: require/module.exports
✓ ES Modules: import/export
✓ Never block Event Loop (no sync I/O in request handlers!)
✓ Use streams for large files
✓ Use cluster for multi-core
✓ Use worker_threads for CPU-intensive work

SECURITY:
✓ Never store plain text passwords (use bcrypt)
✓ Never hardcode secrets (use environment variables)
✓ Validate and sanitize all inputs
✓ Use HTTPS
✓ Implement rate limiting
✓ Use Helmet.js for security headers

PERFORMANCE:
✓ Cache frequently accessed data (Redis)
✓ Use database indexes
✓ Implement pagination
✓ Use compression
✓ Run in cluster mode
✓ Monitor with PM2 or similar
================================================================
*/
```

---

*This complete guide covers every major concept with interview-focused explanations, practical examples, and the exact language to use when answering interview questions. Practice explaining each concept in your own words!*
