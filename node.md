# Node.js Interview Topics: Basic to Advanced

---

## TABLE OF CONTENTS

1. [What is Node.js?](#1-what-is-nodejs)
2. [Node.js Architecture & Event Loop](#2-nodejs-architecture--event-loop)
3. [Modules System](#3-modules-system)
4. [NPM (Node Package Manager)](#4-npm-node-package-manager)
5. [File System (fs) Module](#5-file-system-fs-module)
6. [Streams and Buffers](#6-streams-and-buffers)
7. [Events and EventEmitter](#7-events-and-eventemitter)
8. [HTTP Module & Creating Servers](#8-http-module--creating-servers)
9. [Express.js Framework](#9-expressjs-framework)
10. [Middleware](#10-middleware)
11. [RESTful APIs](#11-restful-apis)
12. [Asynchronous Programming](#12-asynchronous-programming)
13. [Error Handling](#13-error-handling)
14. [Database Integration](#14-database-integration)
15. [Authentication & Authorization](#15-authentication--authorization)
16. [Environment Variables & Configuration](#16-environment-variables--configuration)
17. [Process and Child Processes](#17-process-and-child-processes)
18. [Cluster Module](#18-cluster-module)
19. [Worker Threads](#19-worker-threads)
20. [Security Best Practices](#20-security-best-practices)
21. [Performance Optimization](#21-performance-optimization)
22. [Testing in Node.js](#22-testing-in-nodejs)
23. [Debugging Node.js](#23-debugging-nodejs)
24. [WebSockets & Real-time Communication](#24-websockets--real-time-communication)
25. [Microservices & Architecture Patterns](#25-microservices--architecture-patterns)
26. [Docker & Deployment](#26-docker--deployment)
27. [Advanced Topics](#27-advanced-topics)

---

## 1. What is Node.js?

### Definition
Node.js is an **open-source, cross-platform, JavaScript runtime environment** built on Chrome's V8 JavaScript engine. It allows JavaScript to run on the server side (outside the browser).

### Key Characteristics
- **Non-blocking I/O**: Operations don't block the execution thread.
- **Single-threaded**: Uses a single thread with an event loop.
- **Asynchronous**: Handles concurrent connections efficiently.
- **Fast**: Powered by V8 engine which compiles JS to native machine code.

### Why Node.js?
- Same language (JavaScript) on both frontend and backend.
- Huge ecosystem via npm.
- Excellent for I/O-heavy applications (APIs, real-time apps, microservices).
- Not ideal for CPU-intensive tasks (video encoding, ML).

### Interview Q&A
**Q: What is Node.js, and how is it different from a browser?**
> Node.js runs JavaScript on the server. Unlike the browser, Node.js has no DOM but provides access to the file system, network, OS-level APIs, and more.

**Q: Is Node.js multi-threaded or single-threaded?**
> Node.js is single-threaded for JavaScript execution but uses libuv under the hood, which uses a thread pool for I/O operations.

---

## 2. Node.js Architecture & Event Loop

### V8 Engine
Chrome's V8 compiles JavaScript directly to native machine code for fast execution.

### libuv
A C library that provides the event loop and thread pool (default 4 threads) for handling async I/O.

### The Event Loop
The event loop is the heart of Node.js. It enables non-blocking I/O by offloading operations to the OS or thread pool and then executing callbacks when they complete.

**Phases of the Event Loop (in order):**

```
   ┌───────────────────────────┐
   │           timers          │  → setTimeout, setInterval callbacks
   │   pending callbacks       │  → I/O errors from previous iteration
   │       idle, prepare       │  → internal use
   │           poll            │  → fetch new I/O events; execute I/O callbacks
   │           check           │  → setImmediate callbacks
   │      close callbacks      │  → socket.on('close', ...)
   └───────────────────────────┘
```

**Microtasks** (run between each phase):
- `process.nextTick()` — highest priority
- `Promise.then()` / `queueMicrotask()`

### Example: Event Loop in Action

```js
console.log("Start");

setTimeout(() => console.log("setTimeout"), 0);

setImmediate(() => console.log("setImmediate"));

process.nextTick(() => console.log("nextTick"));

Promise.resolve().then(() => console.log("Promise"));

console.log("End");

// Output:
// Start
// End
// nextTick
// Promise
// setImmediate
// setTimeout
```

### Interview Q&A
**Q: What is the event loop in Node.js?**
> The event loop is a mechanism that allows Node.js to perform non-blocking operations by offloading tasks to the system kernel or thread pool and then executing their callbacks once complete.

**Q: Difference between `process.nextTick()` and `setImmediate()`?**
> `process.nextTick()` fires before any I/O events (even before Promises). `setImmediate()` fires in the check phase, after I/O events.

---

## 3. Modules System

### CommonJS (CJS) — Default in Node.js

```js
// math.js
function add(a, b) { return a + b; }
module.exports = { add };

// app.js
const { add } = require('./math');
console.log(add(2, 3)); // 5
```

### ES Modules (ESM) — Modern standard

```js
// math.mjs or with "type":"module" in package.json
export function add(a, b) { return a + b; }

// app.mjs
import { add } from './math.mjs';
console.log(add(2, 3));
```

### Built-in Modules (no install needed)
- `fs` — File System
- `http` / `https` — HTTP server/client
- `path` — File path utilities
- `os` — Operating system info
- `events` — EventEmitter
- `stream` — Streams
- `crypto` — Cryptography
- `child_process` — Spawn child processes
- `cluster` — Multi-process clustering
- `url` — URL parsing

### Module Resolution Order
1. Core modules (`fs`, `http`, etc.)
2. `node_modules` folder (up the directory tree)
3. Local files (`./`, `../`)

### Interview Q&A
**Q: What is `module.exports` vs `exports`?**
> Both point to the same object initially, but if you reassign `module.exports = something`, the `exports` shorthand breaks. Always use `module.exports` for exporting a single value.

**Q: What is the difference between CJS and ESM?**
> CJS uses `require()` and is synchronous. ESM uses `import/export` and is asynchronous and statically analyzable (enables tree-shaking).

---

## 4. NPM (Node Package Manager)

### Key Concepts
- **package.json**: Manifest file for the project (name, version, scripts, dependencies).
- **node_modules**: Folder where packages are installed.
- **package-lock.json**: Locks exact versions for reproducible installs.

### Common Commands

```bash
npm init -y                   # Initialize a project
npm install express           # Install a package (adds to dependencies)
npm install jest --save-dev   # Dev dependency only
npm uninstall express         # Remove a package
npm update                    # Update packages
npm run <script>              # Run a script from package.json
npm list                      # List installed packages
npm audit                     # Security vulnerability check
npm publish                   # Publish package to npm registry
```

### scripts in package.json

```json
{
  "scripts": {
    "start": "node index.js",
    "dev": "nodemon index.js",
    "test": "jest"
  }
}
```

### Semantic Versioning (SemVer)
`MAJOR.MINOR.PATCH` → e.g., `2.4.1`
- `^2.4.1` — Accept minor and patch updates (2.x.x)
- `~2.4.1` — Accept patch updates only (2.4.x)
- `2.4.1` — Exact version only

### Interview Q&A
**Q: What is the difference between `dependencies` and `devDependencies`?**
> `dependencies` are needed at runtime (e.g., express). `devDependencies` are needed only during development (e.g., jest, nodemon).

**Q: What is `npx`?**
> `npx` runs a package binary without globally installing it. e.g., `npx create-react-app my-app`.

---

## 5. File System (fs) Module

### Reading Files

```js
const fs = require('fs');

// Asynchronous (non-blocking) - preferred
fs.readFile('./file.txt', 'utf8', (err, data) => {
  if (err) throw err;
  console.log(data);
});

// Synchronous (blocking) - avoid in production servers
const data = fs.readFileSync('./file.txt', 'utf8');
console.log(data);

// Promise-based (modern)
const fsPromises = require('fs').promises;
const data = await fsPromises.readFile('./file.txt', 'utf8');
```

### Writing Files

```js
// Overwrite
fs.writeFile('./output.txt', 'Hello World', (err) => {
  if (err) throw err;
});

// Append
fs.appendFile('./output.txt', '\nNew line', (err) => {
  if (err) throw err;
});
```

### Other Operations

```js
fs.unlink('./file.txt', cb);             // Delete file
fs.rename('./old.txt', './new.txt', cb); // Rename/move
fs.mkdir('./newDir', { recursive: true }, cb); // Create directory
fs.readdir('./dir', cb);                 // List directory
fs.stat('./file.txt', cb);              // Get file metadata
fs.watch('./file.txt', cb);             // Watch for changes
```

### Interview Q&A
**Q: When would you use `fs.readFileSync` over `fs.readFile`?**
> Only during startup or CLI tools where blocking is acceptable. In servers, always use the async version to keep the event loop free.

---

## 6. Streams and Buffers

### What is a Buffer?
A Buffer is a chunk of memory allocated outside the V8 heap. It represents binary data (like file content or network packets).

```js
const buf = Buffer.from('Hello');
console.log(buf);           // <Buffer 48 65 6c 6c 6f>
console.log(buf.toString()); // Hello
```

### What are Streams?
Streams process data piece by piece (chunks) instead of loading everything into memory. Ideal for large files or network data.

**Types of Streams:**
- `Readable` — Source of data (e.g., `fs.createReadStream`)
- `Writable` — Destination (e.g., `fs.createWriteStream`)
- `Duplex` — Both readable and writable (e.g., TCP sockets)
- `Transform` — Duplex that transforms data (e.g., `zlib.createGzip`)

### Stream Example

```js
const fs = require('fs');

// Without streams (loads entire file into memory)
const data = fs.readFileSync('./bigfile.txt');
res.end(data);

// With streams (memory efficient)
const readStream = fs.createReadStream('./bigfile.txt');
readStream.pipe(res); // pipes data directly to HTTP response
```

### Transform Stream Example

```js
const { Transform } = require('stream');

const upperCase = new Transform({
  transform(chunk, encoding, callback) {
    this.push(chunk.toString().toUpperCase());
    callback();
  }
});

process.stdin.pipe(upperCase).pipe(process.stdout);
```

### Interview Q&A
**Q: What is the advantage of streams over reading entire files?**
> Streams process data in chunks, so memory usage stays constant regardless of file size. This prevents memory overflow for large files.

**Q: What is backpressure in streams?**
> Backpressure occurs when the writable stream can't consume data as fast as the readable stream produces it. `.pipe()` handles backpressure automatically by pausing the readable stream.

---

## 7. Events and EventEmitter

### EventEmitter
Node.js is event-driven. The `EventEmitter` class allows you to emit and listen to named events.

```js
const EventEmitter = require('events');

class MyEmitter extends EventEmitter {}

const emitter = new MyEmitter();

// Register listener
emitter.on('greet', (name) => {
  console.log(`Hello, ${name}!`);
});

// Emit event
emitter.emit('greet', 'Alice'); // Hello, Alice!

// One-time listener
emitter.once('connect', () => {
  console.log('Connected once');
});

// Remove listener
const handler = () => console.log('handler');
emitter.on('event', handler);
emitter.off('event', handler); // or removeListener
```

### Common EventEmitter Methods
- `on(event, listener)` — Register listener
- `once(event, listener)` — One-time listener
- `emit(event, ...args)` — Trigger event
- `off(event, listener)` / `removeListener` — Remove listener
- `removeAllListeners(event)` — Remove all listeners for an event
- `listeners(event)` — Get all listeners

### Interview Q&A
**Q: What is the Observer pattern and how does Node.js use it?**
> The Observer pattern defines a one-to-many relationship where when one object changes state, all dependent objects are notified. Node.js implements it via EventEmitter.

**Q: What is the default maximum number of listeners?**
> 10. You can change it with `emitter.setMaxListeners(n)`. Exceeding it without changing produces a memory leak warning.

---

## 8. HTTP Module & Creating Servers

### Basic HTTP Server

```js
const http = require('http');

const server = http.createServer((req, res) => {
  // req = IncomingMessage, res = ServerResponse
  res.writeHead(200, { 'Content-Type': 'application/json' });
  res.end(JSON.stringify({ message: 'Hello World' }));
});

server.listen(3000, () => {
  console.log('Server running on port 3000');
});
```

### Routing with HTTP Module

```js
const server = http.createServer((req, res) => {
  const { method, url } = req;

  if (method === 'GET' && url === '/') {
    res.writeHead(200);
    res.end('Home Page');
  } else if (method === 'POST' && url === '/users') {
    let body = '';
    req.on('data', chunk => body += chunk);
    req.on('end', () => {
      const user = JSON.parse(body);
      res.writeHead(201);
      res.end(JSON.stringify(user));
    });
  } else {
    res.writeHead(404);
    res.end('Not Found');
  }
});
```

### HTTPS Server

```js
const https = require('https');
const fs = require('fs');

const options = {
  key: fs.readFileSync('./key.pem'),
  cert: fs.readFileSync('./cert.pem')
};

https.createServer(options, (req, res) => {
  res.end('Secure Server');
}).listen(443);
```

---

## 9. Express.js Framework

### Why Express?
Express is a minimal, fast, and unopinionated web framework for Node.js. It simplifies routing, middleware, and HTTP handling.

### Basic Setup

```js
const express = require('express');
const app = express();

app.use(express.json()); // Parse JSON bodies

app.get('/', (req, res) => {
  res.send('Hello World');
});

app.listen(3000, () => console.log('Server on port 3000'));
```

### Routing

```js
// Basic routes
app.get('/users', getUsers);
app.post('/users', createUser);
app.put('/users/:id', updateUser);
app.delete('/users/:id', deleteUser);

// Route parameters
app.get('/users/:id', (req, res) => {
  console.log(req.params.id);
});

// Query strings
app.get('/search', (req, res) => {
  console.log(req.query.q); // /search?q=hello
});

// Router module
const router = express.Router();
router.get('/', getUsers);
router.post('/', createUser);
app.use('/api/users', router);
```

### Response Methods

```js
res.send('text');
res.json({ key: 'value' });
res.status(404).json({ error: 'Not found' });
res.redirect('/new-path');
res.sendFile('/path/to/file');
res.download('/path/to/file');
```

### Interview Q&A
**Q: What is Express.js and why use it over the built-in `http` module?**
> Express provides a cleaner API for routing, middleware chaining, request/response helpers, and error handling — significantly reducing boilerplate compared to the raw `http` module.

---

## 10. Middleware

### What is Middleware?
Middleware functions have access to `req`, `res`, and `next`. They execute in order and can modify the request/response or terminate the cycle.

```js
// Middleware signature
(req, res, next) => {
  // do something
  next(); // pass to next middleware
}
```

### Types of Middleware

```js
// 1. Application-level
app.use((req, res, next) => {
  console.log(`${req.method} ${req.url}`);
  next();
});

// 2. Router-level
router.use(authMiddleware);

// 3. Error-handling (4 parameters)
app.use((err, req, res, next) => {
  res.status(500).json({ error: err.message });
});

// 4. Built-in
app.use(express.json());
app.use(express.urlencoded({ extended: true }));
app.use(express.static('public'));

// 5. Third-party
const cors = require('cors');
const helmet = require('helmet');
const morgan = require('morgan');

app.use(cors());
app.use(helmet());
app.use(morgan('dev'));
```

### Custom Auth Middleware Example

```js
const authMiddleware = (req, res, next) => {
  const token = req.headers.authorization?.split(' ')[1];
  if (!token) return res.status(401).json({ error: 'Unauthorized' });

  try {
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    req.user = decoded;
    next();
  } catch (err) {
    res.status(403).json({ error: 'Invalid token' });
  }
};

app.get('/protected', authMiddleware, (req, res) => {
  res.json({ user: req.user });
});
```

### Interview Q&A
**Q: What happens if you don't call `next()` in middleware?**
> The request-response cycle hangs. The client never receives a response and eventually times out.

---

## 11. RESTful APIs

### REST Principles
- **Stateless**: Each request contains all necessary information.
- **Uniform Interface**: Standard HTTP methods (GET, POST, PUT, PATCH, DELETE).
- **Resource-based**: URLs represent resources (nouns, not verbs).
- **Layered System**: Client doesn't know if directly talking to the server.

### HTTP Methods & Status Codes

| Method | Use | Success Code |
|--------|-----|-------------|
| GET | Retrieve resource | 200 |
| POST | Create resource | 201 |
| PUT | Replace resource | 200 |
| PATCH | Partial update | 200 |
| DELETE | Delete resource | 204 |

### Complete REST API Example

```js
const express = require('express');
const router = express.Router();

let users = [{ id: 1, name: 'Alice' }];

// GET all
router.get('/', (req, res) => {
  res.json(users);
});

// GET one
router.get('/:id', (req, res) => {
  const user = users.find(u => u.id === +req.params.id);
  if (!user) return res.status(404).json({ error: 'User not found' });
  res.json(user);
});

// POST
router.post('/', (req, res) => {
  const user = { id: Date.now(), ...req.body };
  users.push(user);
  res.status(201).json(user);
});

// PUT
router.put('/:id', (req, res) => {
  const index = users.findIndex(u => u.id === +req.params.id);
  if (index === -1) return res.status(404).json({ error: 'Not found' });
  users[index] = { id: +req.params.id, ...req.body };
  res.json(users[index]);
});

// DELETE
router.delete('/:id', (req, res) => {
  users = users.filter(u => u.id !== +req.params.id);
  res.status(204).send();
});

module.exports = router;
```

---

## 12. Asynchronous Programming

### Callbacks (Old way)

```js
fs.readFile('./file.txt', 'utf8', (err, data) => {
  if (err) return console.error(err);
  console.log(data);
});
```

**Callback Hell (Pyramid of Doom):**
```js
getUser(id, (err, user) => {
  getPosts(user.id, (err, posts) => {
    getComments(posts[0].id, (err, comments) => {
      // deeply nested, hard to read
    });
  });
});
```

### Promises

```js
const readFile = (path) => new Promise((resolve, reject) => {
  fs.readFile(path, 'utf8', (err, data) => {
    if (err) reject(err);
    else resolve(data);
  });
});

readFile('./file.txt')
  .then(data => console.log(data))
  .catch(err => console.error(err))
  .finally(() => console.log('done'));

// Chaining
fetchUser()
  .then(user => fetchPosts(user.id))
  .then(posts => fetchComments(posts[0].id))
  .then(comments => console.log(comments))
  .catch(err => console.error(err));

// Parallel
Promise.all([fetchUser(), fetchPosts(), fetchComments()])
  .then(([user, posts, comments]) => { /* all resolved */ });

// First to settle
Promise.race([p1, p2]).then(result => console.log(result));

// All settle regardless of errors
Promise.allSettled([p1, p2]).then(results => results.forEach(r => console.log(r)));
```

### Async/Await (Modern)

```js
async function getUserData(id) {
  try {
    const user = await fetchUser(id);       // waits for Promise
    const posts = await fetchPosts(user.id);
    return posts;
  } catch (err) {
    console.error('Error:', err.message);
    throw err;
  }
}

// Parallel with async/await
async function getAll() {
  const [users, posts] = await Promise.all([fetchUsers(), fetchPosts()]);
  return { users, posts };
}
```

### Interview Q&A
**Q: What is the difference between `Promise.all` and `Promise.allSettled`?**
> `Promise.all` rejects immediately if any promise rejects. `Promise.allSettled` waits for all to complete (resolved or rejected) and returns all results.

**Q: Does `async/await` make Node.js synchronous?**
> No. `await` pauses execution inside the async function but does NOT block the event loop. Other code continues running.

---

## 13. Error Handling

### Types of Errors
- **Operational Errors**: Expected (file not found, DB connection failure). Handle gracefully.
- **Programmer Errors**: Bugs (undefined variable, wrong types). Fix the code.

### Sync Error Handling

```js
try {
  const data = JSON.parse('invalid json');
} catch (err) {
  console.error('Parse error:', err.message);
}
```

### Async Error Handling

```js
// With callbacks
fs.readFile('./file.txt', (err, data) => {
  if (err) return handleError(err);
});

// With Promises
fetchData()
  .then(data => process(data))
  .catch(err => handleError(err));

// With async/await
async function main() {
  try {
    const data = await fetchData();
  } catch (err) {
    handleError(err);
  }
}
```

### Custom Error Classes

```js
class AppError extends Error {
  constructor(message, statusCode) {
    super(message);
    this.statusCode = statusCode;
    this.isOperational = true;
    Error.captureStackTrace(this, this.constructor);
  }
}

// Usage
throw new AppError('User not found', 404);
```

### Express Global Error Handler

```js
// Must be last middleware, 4 params
app.use((err, req, res, next) => {
  const statusCode = err.statusCode || 500;
  res.status(statusCode).json({
    status: 'error',
    message: err.message,
    ...(process.env.NODE_ENV === 'development' && { stack: err.stack })
  });
});

// Catch unhandled promise rejections
process.on('unhandledRejection', (reason) => {
  console.error('Unhandled rejection:', reason);
  process.exit(1);
});

// Catch uncaught exceptions
process.on('uncaughtException', (err) => {
  console.error('Uncaught exception:', err);
  process.exit(1);
});
```

---

## 14. Database Integration

### MongoDB with Mongoose

```js
const mongoose = require('mongoose');

// Connect
await mongoose.connect(process.env.MONGO_URI);

// Define Schema
const userSchema = new mongoose.Schema({
  name: { type: String, required: true },
  email: { type: String, required: true, unique: true },
  age: { type: Number, min: 0 }
}, { timestamps: true });

const User = mongoose.model('User', userSchema);

// CRUD Operations
const user = await User.create({ name: 'Alice', email: 'alice@example.com' });
const users = await User.find({ age: { $gte: 18 } });
const one = await User.findById(id);
await User.findByIdAndUpdate(id, { name: 'Bob' }, { new: true });
await User.findByIdAndDelete(id);
```

### PostgreSQL with pg / Sequelize

```js
// Using pg (raw queries)
const { Pool } = require('pg');
const pool = new Pool({ connectionString: process.env.DATABASE_URL });

const result = await pool.query('SELECT * FROM users WHERE id = $1', [id]);
console.log(result.rows[0]);

// Using Sequelize ORM
const { Sequelize, DataTypes } = require('sequelize');
const sequelize = new Sequelize(process.env.DATABASE_URL);

const User = sequelize.define('User', {
  name: DataTypes.STRING,
  email: { type: DataTypes.STRING, unique: true }
});

await sequelize.sync();
await User.create({ name: 'Alice', email: 'alice@example.com' });
```

### Redis (Caching)

```js
const redis = require('redis');
const client = redis.createClient();

await client.connect();

// Set with TTL (seconds)
await client.setEx('user:1', 3600, JSON.stringify(userData));

// Get
const cached = await client.get('user:1');
if (cached) return JSON.parse(cached);
```

---

## 15. Authentication & Authorization

### JWT (JSON Web Token)

```js
const jwt = require('jsonwebtoken');

// Sign (create token)
const token = jwt.sign(
  { userId: user._id, role: user.role },
  process.env.JWT_SECRET,
  { expiresIn: '7d' }
);

// Verify
const decoded = jwt.verify(token, process.env.JWT_SECRET);
```

### bcrypt (Password Hashing)

```js
const bcrypt = require('bcryptjs');

// Hash password
const salt = await bcrypt.genSalt(10);
const hashedPassword = await bcrypt.hash(plainPassword, salt);

// Compare
const isMatch = await bcrypt.compare(plainPassword, hashedPassword);
```

### Complete Auth Flow

```js
// Register
app.post('/register', async (req, res) => {
  const { email, password } = req.body;
  const hashedPw = await bcrypt.hash(password, 10);
  const user = await User.create({ email, password: hashedPw });
  res.status(201).json({ message: 'User created' });
});

// Login
app.post('/login', async (req, res) => {
  const { email, password } = req.body;
  const user = await User.findOne({ email });
  if (!user || !(await bcrypt.compare(password, user.password))) {
    return res.status(401).json({ error: 'Invalid credentials' });
  }
  const token = jwt.sign({ userId: user._id }, process.env.JWT_SECRET, { expiresIn: '1d' });
  res.json({ token });
});
```

---

## 16. Environment Variables & Configuration

### Using dotenv

```bash
npm install dotenv
```

```
# .env
NODE_ENV=development
PORT=3000
DB_URI=mongodb://localhost:27017/mydb
JWT_SECRET=mysecretkey
```

```js
require('dotenv').config();

const port = process.env.PORT || 3000;
const dbUri = process.env.DB_URI;
```

### Config Module Pattern

```js
// config/index.js
module.exports = {
  port: process.env.PORT || 3000,
  db: {
    uri: process.env.DB_URI,
    options: { useNewUrlParser: true }
  },
  jwt: {
    secret: process.env.JWT_SECRET,
    expiresIn: '7d'
  }
};
```

### Best Practices
- Never commit `.env` to version control (add to `.gitignore`).
- Provide a `.env.example` with dummy values.
- Validate required env vars at startup.

---

## 17. Process and Child Processes

### `process` Object

```js
process.env          // Environment variables
process.argv         // Command-line arguments
process.pid          // Process ID
process.cwd()        // Current working directory
process.exit(0)      // Exit with code (0 = success, 1 = error)
process.stdout.write('Hello\n')
process.stdin.on('data', data => console.log(data.toString()))

// Graceful shutdown
process.on('SIGTERM', () => {
  server.close(() => {
    mongoose.connection.close();
    process.exit(0);
  });
});
```

### Child Processes

```js
const { exec, spawn, fork, execFile } = require('child_process');

// exec — runs shell command, buffers output
exec('ls -la', (err, stdout, stderr) => {
  console.log(stdout);
});

// spawn — streams output, no shell, preferred for large data
const ls = spawn('ls', ['-la']);
ls.stdout.on('data', data => console.log(data.toString()));

// fork — spawns a new Node.js process, enables IPC
const child = fork('./worker.js');
child.send({ task: 'heavyComputation' });
child.on('message', result => console.log(result));

// In worker.js
process.on('message', (msg) => {
  const result = doHeavyWork(msg.task);
  process.send(result);
});
```

### Interview Q&A
**Q: When would you use `spawn` over `exec`?**
> Use `spawn` when the command produces large output (it streams data). Use `exec` for quick commands with small output (it buffers everything).

---

## 18. Cluster Module

### Why Clustering?
Node.js is single-threaded. Clustering creates multiple worker processes to use all CPU cores.

```js
const cluster = require('cluster');
const os = require('os');
const express = require('express');

if (cluster.isPrimary) {
  const numCPUs = os.cpus().length;
  console.log(`Master ${process.pid} running, forking ${numCPUs} workers`);

  for (let i = 0; i < numCPUs; i++) {
    cluster.fork();
  }

  cluster.on('exit', (worker, code) => {
    console.log(`Worker ${worker.process.pid} died, forking a new one`);
    cluster.fork(); // Auto-restart
  });
} else {
  const app = express();
  app.get('/', (req, res) => res.send(`Handled by worker ${process.pid}`));
  app.listen(3000);
  console.log(`Worker ${process.pid} started`);
}
```

### PM2 (Production Process Manager)

```bash
npm install -g pm2

pm2 start index.js -i max    # Cluster mode, all CPUs
pm2 start index.js --watch   # Auto-restart on changes
pm2 list                     # List processes
pm2 logs                     # View logs
pm2 restart all              # Restart processes
pm2 save && pm2 startup      # Auto-start on reboot
```

---

## 19. Worker Threads

### Why Worker Threads?
For **CPU-intensive** tasks (image processing, encryption, data parsing). Unlike child processes, worker threads share memory.

```js
// main.js
const { Worker } = require('worker_threads');

function runWorker(workerData) {
  return new Promise((resolve, reject) => {
    const worker = new Worker('./worker.js', { workerData });
    worker.on('message', resolve);
    worker.on('error', reject);
    worker.on('exit', (code) => {
      if (code !== 0) reject(new Error(`Worker stopped with code ${code}`));
    });
  });
}

const result = await runWorker({ data: largeArray });
console.log(result);

// worker.js
const { workerData, parentPort } = require('worker_threads');

const result = heavyCPUTask(workerData.data);
parentPort.postMessage(result);
```

### Shared Memory with SharedArrayBuffer

```js
const { Worker, isMainThread, parentPort, workerData } = require('worker_threads');

if (isMainThread) {
  const sharedBuffer = new SharedArrayBuffer(4);
  const arr = new Int32Array(sharedBuffer);
  
  const worker = new Worker(__filename, { workerData: sharedBuffer });
  worker.on('message', () => console.log(arr[0])); // 42
} else {
  const arr = new Int32Array(workerData);
  arr[0] = 42;
  parentPort.postMessage('done');
}
```

### Interview Q&A
**Q: Worker Threads vs Child Processes — when to use which?**
> Use Worker Threads for CPU-intensive JS code (they share memory, lower overhead). Use Child Processes for running external programs or completely isolated environments.

---

## 20. Security Best Practices

### Input Validation

```js
const Joi = require('joi');

const schema = Joi.object({
  email: Joi.string().email().required(),
  password: Joi.string().min(8).required()
});

const { error, value } = schema.validate(req.body);
if (error) return res.status(400).json({ error: error.details[0].message });
```

### Common Vulnerabilities & Fixes

**SQL/NoSQL Injection:**
```js
// Vulnerable
User.find({ name: req.body.name }); // attacker could pass {$gt: ""} to get all users

// Safe - validate/sanitize first
const { error } = nameSchema.validate(req.body.name);
```

**XSS Prevention:**
```js
const helmet = require('helmet');
app.use(helmet()); // Sets secure HTTP headers
```

**CSRF Protection:**
```js
const csurf = require('csurf');
app.use(csurf());
```

**Rate Limiting:**
```js
const rateLimit = require('express-rate-limit');

const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // limit each IP to 100 requests
  message: 'Too many requests, please try again later'
});

app.use('/api', limiter);
```

**CORS Configuration:**
```js
const cors = require('cors');

app.use(cors({
  origin: ['https://myapp.com'],
  methods: ['GET', 'POST', 'PUT', 'DELETE'],
  credentials: true
}));
```

**Environment Secrets:**
- Never log sensitive data.
- Use strong secrets for JWT (`crypto.randomBytes(64).toString('hex')`).
- Store secrets in environment variables, never in code.

---

## 21. Performance Optimization

### Caching

```js
// Memory cache (in-process)
const cache = new Map();

app.get('/users', async (req, res) => {
  if (cache.has('users')) {
    return res.json(cache.get('users'));
  }
  const users = await User.find();
  cache.set('users', users);
  setTimeout(() => cache.delete('users'), 60000); // TTL 1 min
  res.json(users);
});

// Redis cache (distributed)
const cached = await redisClient.get('users');
if (cached) return res.json(JSON.parse(cached));
const users = await User.find();
await redisClient.setEx('users', 60, JSON.stringify(users));
```

### Database Optimization
- Use indexes: `userSchema.index({ email: 1 })`
- Project only needed fields: `User.find({}, 'name email')`
- Paginate results: `User.find().skip(page * limit).limit(limit)`
- Use lean queries in Mongoose: `User.find().lean()` (plain JS objects, no Mongoose overhead)

### Connection Pooling

```js
// pg pool
const pool = new Pool({ max: 20, idleTimeoutMillis: 30000 });
```

### Gzip Compression

```js
const compression = require('compression');
app.use(compression());
```

### Event Loop Monitoring

```js
// Detect event loop lag
let last = Date.now();
setInterval(() => {
  const delta = Date.now() - last;
  if (delta > 100) console.warn(`Event loop lag: ${delta}ms`);
  last = Date.now();
}, 50);
```

---

## 22. Testing in Node.js

### Types of Tests
- **Unit Tests**: Test individual functions in isolation.
- **Integration Tests**: Test multiple modules together (e.g., route + DB).
- **E2E Tests**: Test full application from a user's perspective.

### Jest (Unit Testing)

```js
// math.js
function add(a, b) { return a + b; }
module.exports = { add };

// math.test.js
const { add } = require('./math');

describe('add()', () => {
  it('should add two numbers', () => {
    expect(add(2, 3)).toBe(5);
  });

  it('should handle negative numbers', () => {
    expect(add(-1, 1)).toBe(0);
  });
});
```

### Supertest (API Integration Testing)

```js
const request = require('supertest');
const app = require('./app');

describe('GET /users', () => {
  it('should return list of users', async () => {
    const res = await request(app).get('/users');
    expect(res.status).toBe(200);
    expect(res.body).toBeInstanceOf(Array);
  });
});
```

### Mocking

```js
// Mock a module
jest.mock('./userService', () => ({
  findUser: jest.fn().mockResolvedValue({ id: 1, name: 'Alice' })
}));

// Mock specific method
const userService = require('./userService');
userService.findUser.mockResolvedValueOnce(null); // Override for one call
```

### Code Coverage

```bash
jest --coverage
```

---

## 23. Debugging Node.js

### Built-in Debugger

```bash
node inspect app.js
```

### Chrome DevTools

```bash
node --inspect app.js           # Start with debugger
node --inspect-brk app.js       # Pause at first line
```
Open `chrome://inspect` in Chrome browser.

### VS Code Debugger (launch.json)

```json
{
  "type": "node",
  "request": "launch",
  "name": "Debug App",
  "program": "${workspaceFolder}/index.js",
  "env": { "NODE_ENV": "development" }
}
```

### Common Debugging Techniques

```js
// console methods
console.log(obj);
console.error(err);
console.table(array);
console.time('label');
// ... code ...
console.timeEnd('label'); // Elapsed time

// Util inspect (deep objects)
const util = require('util');
console.log(util.inspect(deepObj, { depth: null, colors: true }));
```

### Debug Module

```js
const debug = require('debug')('app:server');

debug('Server started on port %d', 3000); // Only logs if DEBUG=app:server
```

```bash
DEBUG=app:* node index.js
```

---

## 24. WebSockets & Real-time Communication

### Socket.IO

```bash
npm install socket.io
```

```js
// server.js
const http = require('http');
const { Server } = require('socket.io');
const express = require('express');

const app = express();
const server = http.createServer(app);
const io = new Server(server, {
  cors: { origin: '*' }
});

io.on('connection', (socket) => {
  console.log(`User connected: ${socket.id}`);

  socket.on('message', (data) => {
    io.emit('message', data); // Broadcast to all
    // socket.broadcast.emit('message', data); // Broadcast to others
    // socket.to('room').emit('message', data); // Emit to room
  });

  socket.on('join-room', (room) => {
    socket.join(room);
  });

  socket.on('disconnect', () => {
    console.log('User disconnected');
  });
});

server.listen(3000);
```

```js
// client.js
const io = require('socket.io-client');
const socket = io('http://localhost:3000');

socket.emit('message', 'Hello!');
socket.on('message', (data) => console.log(data));
```

---

## 25. Microservices & Architecture Patterns

### Microservices Concepts
- Each service is small, independently deployable, and responsible for one domain.
- Services communicate via HTTP/REST, gRPC, or message queues (RabbitMQ, Kafka).

### API Gateway Pattern

```
Client → API Gateway → [User Service]
                     → [Order Service]
                     → [Payment Service]
```

```js
// Simple API Gateway with http-proxy-middleware
const { createProxyMiddleware } = require('http-proxy-middleware');

app.use('/users', createProxyMiddleware({ target: 'http://user-service:3001' }));
app.use('/orders', createProxyMiddleware({ target: 'http://order-service:3002' }));
```

### Message Queue (Bull + Redis)

```js
const Bull = require('bull');
const emailQueue = new Bull('email', { redis: { host: 'localhost', port: 6379 } });

// Producer
await emailQueue.add({ to: 'user@example.com', subject: 'Welcome' });

// Consumer (Worker)
emailQueue.process(async (job) => {
  await sendEmail(job.data);
});
```

### Design Patterns in Node.js

**Repository Pattern:**
```js
class UserRepository {
  async findById(id) { return User.findById(id); }
  async create(data) { return User.create(data); }
  async update(id, data) { return User.findByIdAndUpdate(id, data, { new: true }); }
  async delete(id) { return User.findByIdAndDelete(id); }
}
```

**Service Layer Pattern:**
```js
class UserService {
  constructor(userRepository) {
    this.userRepository = userRepository;
  }

  async getUserById(id) {
    const user = await this.userRepository.findById(id);
    if (!user) throw new AppError('User not found', 404);
    return user;
  }
}
```

---

## 26. Docker & Deployment

### Dockerfile for Node.js

```dockerfile
FROM node:20-alpine

WORKDIR /app

COPY package*.json ./
RUN npm ci --only=production

COPY . .

EXPOSE 3000

USER node

CMD ["node", "index.js"]
```

### docker-compose.yml

```yaml
version: '3.8'
services:
  app:
    build: .
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
      - MONGO_URI=mongodb://mongo:27017/mydb
    depends_on:
      - mongo
  mongo:
    image: mongo:6
    volumes:
      - mongo-data:/data/db
volumes:
  mongo-data:
```

### Production Deployment Checklist
- Set `NODE_ENV=production`.
- Use process manager (PM2, or container orchestration).
- Implement health check endpoints.
- Enable logging with structured logs (Winston, Pino).
- Set up monitoring (Prometheus, Grafana, Datadog).
- Enable gzip compression.
- Use HTTPS/TLS.
- Implement graceful shutdown.

---

## 27. Advanced Topics

### Graceful Shutdown

```js
const server = app.listen(3000);

process.on('SIGTERM', () => {
  console.log('Received SIGTERM, shutting down gracefully');
  server.close(async () => {
    await mongoose.connection.close();
    await redisClient.quit();
    process.exit(0);
  });

  // Force exit if not done in 10s
  setTimeout(() => process.exit(1), 10000);
});
```

### Dependency Injection

```js
// Without DI — tightly coupled
class UserService {
  constructor() {
    this.db = new Database(); // hard-coded dependency
  }
}

// With DI — loosely coupled, testable
class UserService {
  constructor(db) {
    this.db = db; // injected from outside
  }
}

const db = new Database();
const userService = new UserService(db);
```

### Rate Limiting Patterns

```js
// Sliding window rate limiter with Redis
const requests = await redisClient.incr(`rate:${ip}`);
if (requests === 1) {
  await redisClient.expire(`rate:${ip}`, 60); // 60s window
}
if (requests > 100) {
  return res.status(429).json({ error: 'Too many requests' });
}
```

### Pagination Patterns

```js
// Offset-based pagination
app.get('/users', async (req, res) => {
  const page = parseInt(req.query.page) || 1;
  const limit = parseInt(req.query.limit) || 10;
  const skip = (page - 1) * limit;

  const [users, total] = await Promise.all([
    User.find().skip(skip).limit(limit).lean(),
    User.countDocuments()
  ]);

  res.json({
    data: users,
    meta: { page, limit, total, pages: Math.ceil(total / limit) }
  });
});

// Cursor-based pagination (more efficient for large datasets)
app.get('/users', async (req, res) => {
  const cursor = req.query.cursor; // last seen _id
  const limit = 10;

  const query = cursor ? { _id: { $gt: cursor } } : {};
  const users = await User.find(query).limit(limit).lean();
  const nextCursor = users.length === limit ? users[users.length - 1]._id : null;

  res.json({ data: users, nextCursor });
});
```

### Memory Leak Detection

```js
// Monitor heap usage
setInterval(() => {
  const { heapUsed, heapTotal } = process.memoryUsage();
  console.log(`Heap: ${Math.round(heapUsed / 1024 / 1024)}MB / ${Math.round(heapTotal / 1024 / 1024)}MB`);
}, 5000);
```

Common memory leak sources:
- Listeners not removed (`emitter.removeAllListeners()`)
- Global variables accumulating data
- Closures holding references
- Uncleared timers/intervals

### SOLID Principles in Node.js
- **S** — Single Responsibility: Each module/class does one thing.
- **O** — Open/Closed: Open for extension, closed for modification (use middleware pattern).
- **L** — Liskov Substitution: Subclasses should replace parent class seamlessly.
- **I** — Interface Segregation: Don't depend on methods you don't use.
- **D** — Dependency Inversion: Depend on abstractions, not concrete implementations (use DI).

---

## Quick Reference: Common Interview Questions

| Topic | Key Question |
|-------|-------------|
| Event Loop | What are the phases of the event loop? |
| Async | Difference between `Promise.all` and `Promise.race`? |
| Streams | What is backpressure? |
| Cluster | How does Node.js utilize multiple CPU cores? |
| Worker Threads | When to use worker threads vs child processes? |
| Security | How do you prevent SQL injection in Node.js? |
| Performance | How do you prevent memory leaks in Node.js? |
| Middleware | What happens if you forget to call `next()`? |
| Modules | Difference between CommonJS and ES Modules? |
| Error Handling | How do you handle async errors in Express? |

---

*Happy interviewing! 🚀*
