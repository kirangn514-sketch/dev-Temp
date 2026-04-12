# 🔐 Security Interview Questions: Node.js & React
### From Beginner to Expert

---

## TABLE OF CONTENTS

1. [General Web Security Basics](#general-web-security-basics)
2. [Node.js Security – Beginner](#nodejs-security--beginner)
3. [Node.js Security – Intermediate](#nodejs-security--intermediate)
4. [Node.js Security – Advanced](#nodejs-security--advanced)
5. [React Security – Beginner](#react-security--beginner)
6. [React Security – Intermediate](#react-security--intermediate)
7. [React Security – Advanced](#react-security--advanced)
8. [Full-Stack Security Scenarios](#full-stack-security-scenarios)

---

## General Web Security Basics

---

### Q1. What is the OWASP Top 10?

**Answer:**
The OWASP (Open Web Application Security Project) Top 10 is a standard awareness document listing the most critical web application security risks. The 2021 list includes:

1. Broken Access Control
2. Cryptographic Failures
3. Injection (SQL, NoSQL, Command)
4. Insecure Design
5. Security Misconfiguration
6. Vulnerable & Outdated Components
7. Identification & Authentication Failures
8. Software & Data Integrity Failures
9. Security Logging & Monitoring Failures
10. Server-Side Request Forgery (SSRF)

Every Node.js/React developer should be familiar with this list as most interview security questions are derived from it.

---

### Q2. What is the difference between Authentication and Authorization?

**Answer:**

| Concept | Definition | Example |
|---|---|---|
| **Authentication** | Verifying *who* you are | Logging in with username/password |
| **Authorization** | Verifying *what* you can do | Checking if user has admin role |

- Authentication comes first.
- Authorization depends on authentication.
- Common bug: skipping authorization checks after successful authentication.

```javascript
// Authentication - who are you?
app.post('/login', async (req, res) => {
  const user = await User.findOne({ email: req.body.email });
  const valid = await bcrypt.compare(req.body.password, user.password);
  if (valid) res.json({ token: generateToken(user) });
});

// Authorization - what can you do?
app.delete('/admin/users/:id', verifyToken, requireRole('admin'), async (req, res) => {
  // Only admins reach here
});
```

---

### Q3. What is HTTPS and why is it important?

**Answer:**
HTTPS (HyperText Transfer Protocol Secure) encrypts data between the browser and server using TLS/SSL.

**Why it matters:**
- Prevents man-in-the-middle (MITM) attacks
- Encrypts sensitive data (passwords, tokens, personal info)
- Required for modern browser features (Service Workers, Geolocation)
- Boosts SEO rankings

**In Node.js:**
```javascript
const https = require('https');
const fs = require('fs');

const options = {
  key: fs.readFileSync('private.key'),
  cert: fs.readFileSync('certificate.crt'),
};

https.createServer(options, app).listen(443);
```

In production, always use HTTPS. Redirect HTTP → HTTPS.

---

### Q4. What is a Cookie vs. LocalStorage vs. SessionStorage for storing tokens?

**Answer:**

| Storage | XSS Safe | CSRF Safe | Persists | Accessible via JS |
|---|---|---|---|---|
| `httpOnly` Cookie | ✅ Yes | ❌ No (needs CSRF token) | Yes (configurable) | ❌ No |
| LocalStorage | ❌ No | ✅ Yes | Yes (until cleared) | ✅ Yes |
| SessionStorage | ❌ No | ✅ Yes | Tab only | ✅ Yes |

**Best practice:** Store JWT tokens in `httpOnly`, `Secure`, `SameSite=Strict` cookies to protect against XSS.

```javascript
res.cookie('token', jwtToken, {
  httpOnly: true,   // Not accessible via JS
  secure: true,     // HTTPS only
  sameSite: 'Strict', // CSRF protection
  maxAge: 3600000   // 1 hour
});
```

---

## Node.js Security – Beginner

---

### Q5. What is SQL/NoSQL Injection and how do you prevent it in Node.js?

**Answer:**
Injection attacks occur when untrusted user input is executed as a query or command.

**SQL Injection Example (vulnerable):**
```javascript
// ❌ DANGEROUS - never do this
const query = `SELECT * FROM users WHERE email = '${req.body.email}'`;
db.query(query);
// Attacker input: ' OR '1'='1 -- (bypasses authentication)
```

**Prevention with parameterized queries:**
```javascript
// ✅ SAFE - parameterized query
const query = 'SELECT * FROM users WHERE email = ?';
db.query(query, [req.body.email]);
```

**NoSQL Injection (MongoDB) - vulnerable:**
```javascript
// ❌ DANGEROUS
User.findOne({ email: req.body.email, password: req.body.password });
// Attacker sends: { "password": { "$gt": "" } }
```

**Prevention with Mongoose validation:**
```javascript
// ✅ SAFE - sanitize with mongoose-sanitize or express-mongo-sanitize
const mongoSanitize = require('express-mongo-sanitize');
app.use(mongoSanitize()); // Strips $ and . from user inputs
```

---

### Q6. What is Helmet.js and what does it do?

**Answer:**
Helmet.js is a collection of Express middleware that sets various HTTP security headers to protect against common web vulnerabilities.

```bash
npm install helmet
```

```javascript
const helmet = require('helmet');
app.use(helmet());
```

**Headers set by Helmet:**

| Header | Protection |
|---|---|
| `Content-Security-Policy` | XSS, data injection |
| `X-Frame-Options` | Clickjacking |
| `X-Content-Type-Options` | MIME sniffing |
| `Strict-Transport-Security` | Forces HTTPS |
| `X-XSS-Protection` | XSS (legacy browsers) |
| `Referrer-Policy` | Info leakage |

```javascript
// Custom configuration
app.use(helmet({
  contentSecurityPolicy: {
    directives: {
      defaultSrc: ["'self'"],
      scriptSrc: ["'self'", "trusted-cdn.com"],
    },
  },
  hsts: {
    maxAge: 31536000,       // 1 year
    includeSubDomains: true,
    preload: true,
  },
}));
```

---

### Q7. How do you hash passwords securely in Node.js?

**Answer:**
Never store plain-text passwords. Use bcrypt, Argon2, or scrypt for hashing.

**Why not MD5/SHA?** — They're fast (bad for passwords), and vulnerable to rainbow table attacks.

**bcrypt implementation:**
```javascript
const bcrypt = require('bcrypt');

// Hashing a password
const SALT_ROUNDS = 12; // Higher = slower but more secure
const hashedPassword = await bcrypt.hash(plainPassword, SALT_ROUNDS);

// Verifying
const isMatch = await bcrypt.compare(plainPassword, hashedPassword);

// Full example
app.post('/register', async (req, res) => {
  const { email, password } = req.body;

  // Validate input
  if (!password || password.length < 8) {
    return res.status(400).json({ error: 'Password too short' });
  }

  const hashed = await bcrypt.hash(password, 12);
  await User.create({ email, password: hashed });
  res.status(201).json({ message: 'User created' });
});
```

**Argon2 (recommended for new projects):**
```javascript
const argon2 = require('argon2');
const hash = await argon2.hash(password);
const valid = await argon2.verify(hash, password);
```

---

### Q8. What is Rate Limiting and how do you implement it?

**Answer:**
Rate limiting restricts how many requests a user/IP can make in a time period, protecting against brute force attacks, DDoS, and API abuse.

```bash
npm install express-rate-limit
```

```javascript
const rateLimit = require('express-rate-limit');

// General limiter
const generalLimiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100,                  // Max 100 requests per window
  message: { error: 'Too many requests, please try again later.' },
  standardHeaders: true,
  legacyHeaders: false,
});

// Stricter limiter for auth routes
const authLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 5,  // Only 5 login attempts
  message: { error: 'Too many login attempts.' },
});

app.use(generalLimiter);
app.use('/api/auth/login', authLimiter);
```

---

### Q9. What is input validation and sanitization? How are they different?

**Answer:**

| Concept | Purpose | Example |
|---|---|---|
| **Validation** | Check if input meets expected format | Is email a valid email? Is age a number? |
| **Sanitization** | Clean/transform input to remove dangerous content | Strip HTML tags, trim whitespace |

**Validation with express-validator:**
```javascript
const { body, validationResult } = require('express-validator');

app.post('/register',
  body('email').isEmail().normalizeEmail(),
  body('password').isLength({ min: 8 }).matches(/^(?=.*[A-Z])(?=.*[0-9])/),
  body('age').isInt({ min: 18, max: 120 }),
  (req, res) => {
    const errors = validationResult(req);
    if (!errors.isEmpty()) {
      return res.status(400).json({ errors: errors.array() });
    }
    // Safe to proceed
  }
);
```

**Sanitization:**
```javascript
const xss = require('xss');

const safeInput = xss(req.body.userInput); // Strips <script> tags etc.
const trimmed = req.body.name.trim();      // Remove whitespace
```

---

### Q10. How do you use environment variables securely in Node.js?

**Answer:**
Sensitive data (API keys, DB passwords, secrets) must never be hardcoded in source code.

```bash
npm install dotenv
```

**.env file:**
```
PORT=3000
DB_URI=mongodb://localhost:27017/myapp
JWT_SECRET=mySuperSecretKeyHere
STRIPE_KEY=sk_live_xxxxx
```

**Usage:**
```javascript
require('dotenv').config();

const dbUri = process.env.DB_URI;
const jwtSecret = process.env.JWT_SECRET;
```

**Security rules:**
- Add `.env` to `.gitignore` — NEVER commit it
- Use different `.env` files for dev/staging/production
- Validate required env vars at startup

```javascript
// Validate required env vars at startup
const required = ['JWT_SECRET', 'DB_URI', 'STRIPE_KEY'];
required.forEach(key => {
  if (!process.env[key]) {
    console.error(`Missing required env var: ${key}`);
    process.exit(1);
  }
});
```

---

## Node.js Security – Intermediate

---

### Q11. What is JWT and what are its security considerations?

**Answer:**
JWT (JSON Web Token) is a compact, URL-safe means of representing claims between two parties. Structure: `header.payload.signature`

**Implementation:**
```javascript
const jwt = require('jsonwebtoken');

// Signing
const token = jwt.sign(
  { userId: user._id, role: user.role },
  process.env.JWT_SECRET,
  { expiresIn: '15m', algorithm: 'HS256' }
);

// Verifying
const decoded = jwt.verify(token, process.env.JWT_SECRET);
```

**Security considerations:**

1. **Use strong secret** (min 256 bits): `crypto.randomBytes(32).toString('hex')`
2. **Short expiry for access tokens** (15 min), longer for refresh tokens (7 days)
3. **Store in httpOnly cookie**, not localStorage
4. **Don't store sensitive data** in payload (it's base64-encoded, not encrypted)
5. **Implement token revocation** using blacklists or refresh token rotation
6. **Specify algorithm explicitly** to prevent algorithm switching attacks

```javascript
// ❌ Vulnerable - no algorithm check
jwt.verify(token, secret);

// ✅ Safe - explicitly specify algorithm
jwt.verify(token, secret, { algorithms: ['HS256'] });
```

---

### Q12. What is CSRF and how do you prevent it in Node.js?

**Answer:**
CSRF (Cross-Site Request Forgery) tricks authenticated users into unknowingly submitting malicious requests to a server where they are authenticated.

**Attack scenario:**
1. User is logged into `bank.com`
2. User visits `evil.com`
3. `evil.com` sends a form POST to `bank.com/transfer`
4. Browser includes `bank.com` cookies automatically → transfer happens

**Prevention strategies:**

**1. SameSite cookies:**
```javascript
res.cookie('token', jwtToken, {
  sameSite: 'Strict', // Cookie not sent with cross-site requests
});
```

**2. CSRF tokens with csurf:**
```javascript
const csrf = require('csurf');
const csrfProtection = csrf({ cookie: true });

app.get('/form', csrfProtection, (req, res) => {
  res.render('form', { csrfToken: req.csrfToken() });
});

app.post('/submit', csrfProtection, (req, res) => {
  // CSRF token validated automatically
});
```

**3. Double Submit Cookie pattern** (for SPAs):
```javascript
// Server sends CSRF token in both cookie and response body
// Client must send it back in request header
// Server checks both match
app.use((req, res, next) => {
  const cookieToken = req.cookies['csrf-token'];
  const headerToken = req.headers['x-csrf-token'];
  if (cookieToken !== headerToken) return res.status(403).send('CSRF mismatch');
  next();
});
```

---

### Q13. How do you handle file uploads securely in Node.js?

**Answer:**
File upload vulnerabilities can allow attackers to upload malicious files, execute server-side code, or perform path traversal attacks.

```javascript
const multer = require('multer');
const path = require('path');
const crypto = require('crypto');

const storage = multer.diskStorage({
  destination: './uploads/',
  filename: (req, file, cb) => {
    // Generate random filename to prevent path traversal
    const randomName = crypto.randomBytes(16).toString('hex');
    const ext = path.extname(file.originalname).toLowerCase();
    cb(null, `${randomName}${ext}`);
  },
});

const upload = multer({
  storage,
  limits: {
    fileSize: 5 * 1024 * 1024, // 5MB max
    files: 1,
  },
  fileFilter: (req, file, cb) => {
    // Whitelist allowed types
    const allowedTypes = /jpeg|jpg|png|gif|pdf/;
    const isValidExt = allowedTypes.test(path.extname(file.originalname).toLowerCase());
    const isValidMime = allowedTypes.test(file.mimetype);

    if (isValidExt && isValidMime) {
      cb(null, true);
    } else {
      cb(new Error('Only images and PDFs are allowed'));
    }
  },
});
```

**Additional protections:**
- Serve uploads from a separate domain (not same origin as app)
- Scan files with antivirus (e.g., ClamAV)
- Store files outside the web root
- Never execute uploaded files

---

### Q14. What is the difference between XSS and CSRF?

**Answer:**

| Attack | Full Name | How it Works | Target | Defense |
|---|---|---|---|---|
| **XSS** | Cross-Site Scripting | Injects malicious scripts into web pages | Other users of the site | CSP, input sanitization |
| **CSRF** | Cross-Site Request Forgery | Forces authenticated user to make unwanted requests | Server-side actions | SameSite cookies, CSRF tokens |

**XSS Types:**
- **Stored XSS**: Malicious script saved in database, executes when loaded
- **Reflected XSS**: Script in URL, executes immediately
- **DOM-based XSS**: Manipulates DOM via client-side JS

**Node.js XSS prevention:**
```javascript
// Sanitize output
const xss = require('xss');
const safe = xss(userInput); // Escapes HTML entities

// Set Content Security Policy
app.use(helmet.contentSecurityPolicy({
  directives: {
    scriptSrc: ["'self'"], // Only allow scripts from same origin
  },
}));
```

---

### Q15. What is CORS and how do you configure it securely?

**Answer:**
CORS (Cross-Origin Resource Sharing) is a browser security mechanism that restricts HTTP requests from a different origin (domain/protocol/port) than the one that served the page.

```bash
npm install cors
```

**Insecure (allows everyone):**
```javascript
// ❌ Never do this in production
app.use(cors()); // Allows ALL origins
```

**Secure configuration:**
```javascript
const cors = require('cors');

const allowedOrigins = [
  'https://myapp.com',
  'https://www.myapp.com',
  process.env.NODE_ENV === 'development' ? 'http://localhost:3000' : null,
].filter(Boolean);

app.use(cors({
  origin: (origin, callback) => {
    if (!origin || allowedOrigins.includes(origin)) {
      callback(null, true);
    } else {
      callback(new Error('Not allowed by CORS'));
    }
  },
  credentials: true,          // Allow cookies
  methods: ['GET', 'POST', 'PUT', 'DELETE'],
  allowedHeaders: ['Content-Type', 'Authorization'],
}));
```

---

### Q16. How do you prevent command injection in Node.js?

**Answer:**
Command injection occurs when user input is passed to shell commands.

```javascript
// ❌ DANGEROUS
const { exec } = require('child_process');
exec(`convert ${req.body.filename} output.jpg`);
// Attacker input: "file.jpg; rm -rf /"

// ✅ SAFE - use execFile with args as array
const { execFile } = require('child_process');
execFile('convert', [req.body.filename, 'output.jpg'], (err, stdout) => {
  // Arguments are not interpreted by shell
});

// ✅ ALSO SAFE - validate/sanitize filename first
const path = require('path');
const filename = path.basename(req.body.filename); // Remove path traversal
const allowed = /^[a-zA-Z0-9_-]+\.(jpg|png)$/;
if (!allowed.test(filename)) return res.status(400).send('Invalid filename');
```

---

## Node.js Security – Advanced

---

### Q17. What is Prototype Pollution and how do you prevent it?

**Answer:**
Prototype Pollution is a JavaScript vulnerability where an attacker manipulates the `Object.prototype`, affecting all objects in the application.

**Vulnerable code:**
```javascript
function merge(target, source) {
  for (let key in source) {
    target[key] = source[key]; // ❌ Pollutes prototype if key is "__proto__"
  }
}

// Attacker sends: { "__proto__": { "isAdmin": true } }
merge({}, JSON.parse(req.body));
// Now ALL objects have isAdmin = true!
```

**Prevention:**
```javascript
// 1. Use Object.create(null) to create objects without prototype
const safeObj = Object.create(null);

// 2. Check for dangerous keys
function safeMerge(target, source) {
  for (let key of Object.keys(source)) {
    if (key === '__proto__' || key === 'constructor' || key === 'prototype') {
      continue; // Skip dangerous keys
    }
    target[key] = source[key];
  }
}

// 3. Use lodash merge (has built-in protection in newer versions)
const _ = require('lodash');
_.merge({}, userInput);

// 4. Use JSON schema validation
// 5. Use Object.freeze(Object.prototype) in critical sections
```

---

### Q18. How do you implement Refresh Token rotation securely?

**Answer:**
Refresh token rotation is a security technique where every use of a refresh token generates a new refresh token and invalidates the old one. This detects token theft.

```javascript
// Token service
const generateTokens = (userId) => {
  const accessToken = jwt.sign({ userId }, process.env.JWT_SECRET, { expiresIn: '15m' });
  const refreshToken = crypto.randomBytes(64).toString('hex');
  return { accessToken, refreshToken };
};

// Store refresh tokens in DB with hash
app.post('/auth/refresh', async (req, res) => {
  const { refreshToken } = req.cookies;
  if (!refreshToken) return res.status(401).json({ error: 'No refresh token' });

  // Find and validate in DB
  const storedToken = await RefreshToken.findOne({ token: refreshToken });
  if (!storedToken || storedToken.revoked) {
    // Possible token theft - revoke all tokens for this user
    if (storedToken) {
      await RefreshToken.updateMany({ userId: storedToken.userId }, { revoked: true });
    }
    return res.status(401).json({ error: 'Invalid refresh token' });
  }

  // Rotate: invalidate old, issue new
  await RefreshToken.updateOne({ _id: storedToken._id }, { revoked: true });
  const { accessToken, refreshToken: newRefreshToken } = generateTokens(storedToken.userId);
  await RefreshToken.create({ token: newRefreshToken, userId: storedToken.userId });

  res.cookie('refreshToken', newRefreshToken, { httpOnly: true, secure: true });
  res.json({ accessToken });
});
```

---

### Q19. What is a Security Audit and how do you perform one on a Node.js project?

**Answer:**
A security audit identifies vulnerabilities in your dependencies and code.

**1. npm audit:**
```bash
npm audit                # Lists known vulnerabilities
npm audit fix            # Auto-fix non-breaking issues
npm audit fix --force    # Fix all (may break things)
npm audit --audit-level=high  # Fail only on high severity
```

**2. Snyk:**
```bash
npm install -g snyk
snyk auth
snyk test            # Test for vulnerabilities
snyk monitor         # Monitor project continuously
```

**3. Static Analysis (ESLint security plugin):**
```bash
npm install --save-dev eslint-plugin-security
```

```json
// .eslintrc
{
  "plugins": ["security"],
  "extends": ["plugin:security/recommended"]
}
```

**4. Dependency pinning:**
```json
// Use exact versions to prevent supply chain attacks
{
  "dependencies": {
    "express": "4.18.2"  // Not "^4.18.2"
  }
}
```

**5. SAST (Static Application Security Testing):**
- SonarQube, CodeQL, Semgrep for deep code analysis

---

### Q20. How do you prevent Server-Side Request Forgery (SSRF)?

**Answer:**
SSRF occurs when an attacker tricks the server into making requests to internal services.

**Vulnerable:**
```javascript
// ❌ DANGEROUS
app.post('/fetch', async (req, res) => {
  const response = await axios.get(req.body.url);
  // Attacker: url = "http://169.254.169.254/latest/meta-data/" (AWS metadata)
  // Or: url = "http://internal-db:5432"
  res.json(response.data);
});
```

**Prevention:**
```javascript
const dns = require('dns').promises;
const ipRangeCheck = require('ip-range-check');

const BLOCKED_RANGES = [
  '10.0.0.0/8',     // Private
  '172.16.0.0/12',  // Private
  '192.168.0.0/16', // Private
  '127.0.0.0/8',    // Loopback
  '169.254.0.0/16', // Link-local (AWS metadata)
];

async function isSafeUrl(url) {
  const parsed = new URL(url);

  // Whitelist allowed protocols
  if (!['http:', 'https:'].includes(parsed.protocol)) return false;

  // Resolve to IP and check against blocked ranges
  const addresses = await dns.lookup(parsed.hostname, { all: true });
  for (const { address } of addresses) {
    if (ipRangeCheck(address, BLOCKED_RANGES)) return false;
  }
  return true;
}

app.post('/fetch', async (req, res) => {
  if (!(await isSafeUrl(req.body.url))) {
    return res.status(400).json({ error: 'URL not allowed' });
  }
  const response = await axios.get(req.body.url);
  res.json(response.data);
});
```

---

### Q21. What is Content Security Policy (CSP) and how do you configure it?

**Answer:**
CSP is an HTTP header that tells browsers which sources of content are trusted, effectively preventing XSS attacks.

```javascript
app.use(helmet.contentSecurityPolicy({
  directives: {
    defaultSrc: ["'self'"],                          // Default: only same origin
    scriptSrc: ["'self'", "https://cdnjs.cloudflare.com"],  // Scripts sources
    styleSrc: ["'self'", "'unsafe-inline'", "https://fonts.googleapis.com"],
    fontSrc: ["'self'", "https://fonts.gstatic.com"],
    imgSrc: ["'self'", "data:", "https:"],
    connectSrc: ["'self'", "https://api.myservice.com"],
    frameAncestors: ["'none'"],                      // Prevents clickjacking
    formAction: ["'self'"],                          // Forms only submit to same origin
    upgradeInsecureRequests: [],                     // Auto-upgrade HTTP to HTTPS
    reportUri: '/csp-violation-report',              // Report violations
  },
  reportOnly: false, // Set true to test without enforcing
}));

// Log CSP violations
app.post('/csp-violation-report', express.json({ type: 'application/csp-report' }), (req, res) => {
  console.error('CSP Violation:', req.body);
  res.status(204).end();
});
```

---

## React Security – Beginner

---

### Q22. What is XSS in React and how does React help prevent it?

**Answer:**
React escapes all dynamic values by default before rendering them, preventing most XSS attacks.

```jsx
// React automatically escapes this
const userInput = "<script>alert('xss')</script>";
return <div>{userInput}</div>; // Renders as text, not HTML ✅

// ❌ dangerouslySetInnerHTML bypasses protection
return <div dangerouslySetInnerHTML={{ __html: userInput }} />; // DANGEROUS
```

**When dangerouslySetInnerHTML is needed (e.g., rich text), sanitize first:**
```jsx
import DOMPurify from 'dompurify';

function SafeHTML({ content }) {
  const clean = DOMPurify.sanitize(content, {
    ALLOWED_TAGS: ['b', 'i', 'em', 'strong', 'p', 'br'],
    ALLOWED_ATTR: [], // No attributes at all
  });

  return <div dangerouslySetInnerHTML={{ __html: clean }} />;
}
```

---

### Q23. How do you handle sensitive data in React state?

**Answer:**
React state exists in memory and can potentially be exposed through browser devtools, error logs, or memory dumps.

**Best practices:**
```jsx
// ❌ Don't store full credit card numbers, SSNs in state
const [state, setState] = useState({ ccNumber: '4111111111111111' });

// ✅ Store only what's necessary
const [state, setState] = useState({ ccLast4: '1111', ccToken: 'tok_xxx' });

// ❌ Don't log sensitive data
console.log('User data:', user); // May include password, tokens

// ✅ Log only what you need
console.log('User logged in:', user.id);

// Clear sensitive state on unmount
useEffect(() => {
  return () => {
    setPassword('');  // Clear on unmount
    setSensitiveData(null);
  };
}, []);
```

---

### Q24. What are the security risks of using third-party npm packages in React?

**Answer:**
Third-party packages can contain malicious code, vulnerabilities, or be compromised (supply chain attack).

**Risks:**
- Malicious packages with typosquatting names (e.g., `lodahs` instead of `lodash`)
- Compromised packages (maintainer account hacked)
- Vulnerable dependencies (outdated with known CVEs)

**Mitigation:**
```bash
# Check package before installing
npm info package-name

# Audit regularly
npm audit

# Check package downloads/repo reputation on npmjs.com

# Lock versions
npm ci  # Uses package-lock.json exactly (use in CI/CD)

# Scan with Snyk
snyk test
```

```json
// Use exact versions in package.json
{
  "dependencies": {
    "axios": "1.4.0"  // Not "^1.4.0"
  }
}
```

---

### Q25. How do you securely make API calls from React?

**Answer:**
```jsx
// ❌ Never hardcode API keys in React source code
// They will be visible in the browser bundle!
const API_KEY = 'sk_live_xxx'; // BAD - exposed to all users

// ✅ Use environment variables (still exposed in bundle for public apps)
// Only safe for PUBLIC keys
const API_KEY = process.env.REACT_APP_PUBLIC_KEY;

// ✅ Best: proxy sensitive API calls through your backend
// React → Your Node.js server → Third-party API
const response = await fetch('/api/payment', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ amount: 1000 }),
  credentials: 'include', // Send cookies
});

// ✅ Always use HTTPS endpoints
// ✅ Validate and handle errors
if (!response.ok) {
  throw new Error('API call failed');
}
```

---

## React Security – Intermediate

---

### Q26. How do you implement secure routing (protected routes) in React?

**Answer:**
Protected routes ensure that only authenticated/authorized users can access certain pages.

```jsx
import { Navigate, useLocation } from 'react-router-dom';
import { useAuth } from './hooks/useAuth';

// Protected Route component
function ProtectedRoute({ children, requiredRole }) {
  const { user, isLoading } = useAuth();
  const location = useLocation();

  if (isLoading) return <LoadingSpinner />;

  // Not authenticated
  if (!user) {
    return <Navigate to="/login" state={{ from: location }} replace />;
  }

  // Not authorized (role-based)
  if (requiredRole && user.role !== requiredRole) {
    return <Navigate to="/unauthorized" replace />;
  }

  return children;
}

// Usage
function App() {
  return (
    <Routes>
      <Route path="/login" element={<Login />} />
      <Route path="/dashboard" element={
        <ProtectedRoute>
          <Dashboard />
        </ProtectedRoute>
      } />
      <Route path="/admin" element={
        <ProtectedRoute requiredRole="admin">
          <AdminPanel />
        </ProtectedRoute>
      } />
    </Routes>
  );
}
```

---

### Q27. What is the security implication of using eval() or similar in React?

**Answer:**
`eval()`, `new Function()`, and similar constructs execute arbitrary strings as code — this is extremely dangerous.

```jsx
// ❌ NEVER use eval
eval(userInput); // Executes arbitrary code

// ❌ new Function is equally dangerous
const fn = new Function('return ' + userInput)();

// ❌ setTimeout/setInterval with strings
setTimeout(userInput, 1000);

// ✅ Instead, use safe alternatives
// For math expressions: use math.js
import { evaluate } from 'mathjs';
const result = evaluate(userInput); // Safe math evaluation

// For dynamic components: use a component map
const components = { Button, Input, Card };
const Component = components[req.componentName]; // Never eval
```

**React-specific concern:**
```jsx
// ❌ Avoid href="javascript:" links
<a href="javascript:void(0)">Click</a>

// ✅ Use onClick handler
<button onClick={handleClick}>Click</button>

// ❌ Avoid passing user input to href without validation
<a href={user.website}>Website</a>

// ✅ Validate URL protocol
function SafeLink({ href, children }) {
  const isSafe = /^https?:\/\//.test(href);
  if (!isSafe) return <span>{children}</span>;
  return <a href={href} rel="noopener noreferrer" target="_blank">{children}</a>;
}
```

---

### Q28. What is `rel="noopener noreferrer"` and why is it important?

**Answer:**
When opening links in a new tab (`target="_blank"`), the opened page can access the opener's `window` object via `window.opener`, potentially redirecting your page to a malicious URL (reverse tabnapping).

```jsx
// ❌ Vulnerable
<a href="https://external.com" target="_blank">Visit</a>
// external.com can: window.opener.location = 'http://phishing.com'

// ✅ Safe
<a
  href="https://external.com"
  target="_blank"
  rel="noopener noreferrer"  // Prevents opener access + hides referrer
>
  Visit
</a>
```

- `noopener`: Prevents `window.opener` access
- `noreferrer`: Also hides the Referrer header (privacy)

---

### Q29. How do you prevent sensitive information from appearing in React error messages?

**Answer:**
Development error details should never reach production users.

```jsx
// Error Boundary for production
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError() {
    return { hasError: true };
  }

  componentDidCatch(error, info) {
    // Log to monitoring service, not console
    logErrorToService(error, info); // e.g., Sentry
    // Don't expose error details to users
  }

  render() {
    if (this.state.hasError) {
      return (
        <div>
          <h2>Something went wrong.</h2>
          <p>Please try again later.</p>
          {/* Never render: error.message, stack traces */}
        </div>
      );
    }
    return this.props.children;
  }
}

// Environment-based error display
const ErrorMessage = ({ error }) => {
  if (process.env.NODE_ENV === 'development') {
    return <pre>{error.stack}</pre>;
  }
  return <p>An error occurred. Please try again.</p>;
};
```

---

### Q30. How do you secure forms in React against common attacks?

**Answer:**
```jsx
import { useState } from 'react';
import DOMPurify from 'dompurify';

function SecureForm() {
  const [formData, setFormData] = useState({ name: '', email: '', message: '' });
  const [errors, setErrors] = useState({});

  const validate = (data) => {
    const errors = {};
    if (!data.name || data.name.length > 100) errors.name = 'Invalid name';
    if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(data.email)) errors.email = 'Invalid email';
    if (!data.message || data.message.length > 1000) errors.message = 'Message too long';
    return errors;
  };

  const handleSubmit = async (e) => {
    e.preventDefault();

    // Client-side validation (always validate server-side too!)
    const validationErrors = validate(formData);
    if (Object.keys(validationErrors).length > 0) {
      setErrors(validationErrors);
      return;
    }

    // Sanitize before sending
    const sanitized = {
      name: DOMPurify.sanitize(formData.name),
      email: formData.email.toLowerCase().trim(),
      message: DOMPurify.sanitize(formData.message),
    };

    await fetch('/api/contact', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'X-CSRF-Token': getCsrfToken(), // Include CSRF token
      },
      body: JSON.stringify(sanitized),
      credentials: 'include',
    });
  };

  return (
    <form onSubmit={handleSubmit} noValidate>
      <input
        type="text"
        value={formData.name}
        onChange={e => setFormData(prev => ({ ...prev, name: e.target.value }))}
        maxLength={100}        // HTML-level limit
        autoComplete="name"
      />
      {errors.name && <span role="alert">{errors.name}</span>}
      {/* ... rest of form */}
    </form>
  );
}
```

---

## React Security – Advanced

---

### Q31. How do you implement secure authentication flow in a React SPA?

**Answer:**
A complete, secure auth flow for a React SPA:

```jsx
// 1. Auth Context
const AuthContext = createContext(null);

export function AuthProvider({ children }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  // Verify session on mount (token in httpOnly cookie)
  useEffect(() => {
    fetch('/api/auth/me', { credentials: 'include' })
      .then(r => r.ok ? r.json() : null)
      .then(data => setUser(data?.user ?? null))
      .finally(() => setLoading(false));
  }, []);

  const login = async (email, password) => {
    const res = await fetch('/api/auth/login', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ email, password }),
      credentials: 'include', // Receive httpOnly cookie
    });
    if (!res.ok) throw new Error('Login failed');
    const { user } = await res.json();
    setUser(user);
  };

  const logout = async () => {
    await fetch('/api/auth/logout', {
      method: 'POST',
      credentials: 'include',
    });
    setUser(null);
  };

  return (
    <AuthContext.Provider value={{ user, login, logout, loading }}>
      {children}
    </AuthContext.Provider>
  );
}

// 2. Axios interceptor for token refresh
axios.interceptors.response.use(
  response => response,
  async error => {
    if (error.response?.status === 401 && !error.config._retry) {
      error.config._retry = true;
      await axios.post('/api/auth/refresh', {}, { withCredentials: true });
      return axios(error.config); // Retry original request
    }
    return Promise.reject(error);
  }
);
```

---

### Q32. What is Clickjacking and how do you prevent it in a React app?

**Answer:**
Clickjacking tricks users into clicking on invisible iframe elements that perform unintended actions.

**Prevention is primarily server-side (HTTP headers):**

```javascript
// Node.js/Express (backend)
app.use(helmet.frameguard({ action: 'deny' }));
// Sets: X-Frame-Options: DENY
// Also use CSP:
// frame-ancestors 'none'
```

**React-side detection (defense in depth):**
```jsx
// Bust frames (prevents your app from being iframed)
useEffect(() => {
  if (window.top !== window.self) {
    // We're inside an iframe!
    window.top.location = window.self.location; // Break out
  }
}, []);

// Or in index.html
// <style>html { display: none; }</style>
// <script>
//   if (self === top) {
//     document.documentElement.style.display = 'block';
//   } else {
//     top.location = self.location;
//   }
// </script>
```

---

### Q33. How do you handle OAuth2 / Social Login securely in React?

**Answer:**
OAuth2 PKCE (Proof Key for Code Exchange) flow is recommended for SPAs since they can't keep secrets.

```jsx
// 1. Generate code verifier and challenge
function generateCodeVerifier() {
  const array = new Uint8Array(32);
  crypto.getRandomValues(array);
  return btoa(String.fromCharCode(...array))
    .replace(/\+/g, '-').replace(/\//g, '_').replace(/=/g, '');
}

async function generateCodeChallenge(verifier) {
  const encoder = new TextEncoder();
  const data = encoder.encode(verifier);
  const digest = await crypto.subtle.digest('SHA-256', data);
  return btoa(String.fromCharCode(...new Uint8Array(digest)))
    .replace(/\+/g, '-').replace(/\//g, '_').replace(/=/g, '');
}

// 2. Initiate OAuth flow
async function loginWithGoogle() {
  const codeVerifier = generateCodeVerifier();
  const codeChallenge = await generateCodeChallenge(codeVerifier);
  const state = crypto.randomUUID(); // CSRF protection

  // Store in sessionStorage (cleared when tab closes)
  sessionStorage.setItem('oauth_state', state);
  sessionStorage.setItem('code_verifier', codeVerifier);

  const params = new URLSearchParams({
    client_id: process.env.REACT_APP_GOOGLE_CLIENT_ID,
    redirect_uri: `${window.location.origin}/auth/callback`,
    response_type: 'code',
    scope: 'openid email profile',
    state,
    code_challenge: codeChallenge,
    code_challenge_method: 'S256',
  });

  window.location.href = `https://accounts.google.com/o/oauth2/v2/auth?${params}`;
}

// 3. Handle callback
function AuthCallback() {
  useEffect(() => {
    const params = new URLSearchParams(window.location.search);
    const code = params.get('code');
    const state = params.get('state');
    const storedState = sessionStorage.getItem('oauth_state');
    const codeVerifier = sessionStorage.getItem('code_verifier');

    // Validate state to prevent CSRF
    if (state !== storedState) {
      navigate('/login?error=invalid_state');
      return;
    }

    // Exchange code for tokens (done on backend)
    fetch('/api/auth/oauth/callback', {
      method: 'POST',
      body: JSON.stringify({ code, codeVerifier }),
      credentials: 'include',
    }).then(() => navigate('/dashboard'));

    // Cleanup
    sessionStorage.removeItem('oauth_state');
    sessionStorage.removeItem('code_verifier');
  }, []);
}
```

---

### Q34. How do you prevent sensitive data exposure in React DevTools and Redux state?

**Answer:**
```jsx
// 1. Redux - never store passwords, full tokens in state
const authSlice = createSlice({
  name: 'auth',
  initialState: {
    user: null,
    isAuthenticated: false,
    // ❌ Don't store: password, fullToken, ssn, cvv
    // ✅ Store: userId, username, role, isAuthenticated
  },
});

// 2. Hide sensitive data in production
if (process.env.NODE_ENV === 'production') {
  // Disable React DevTools
  if (typeof window.__REACT_DEVTOOLS_GLOBAL_HOOK__ === 'object') {
    for (let [key, value] of Object.entries(window.__REACT_DEVTOOLS_GLOBAL_HOOK__)) {
      window.__REACT_DEVTOOLS_GLOBAL_HOOK__[key] =
        typeof value === 'function' ? () => {} : null;
    }
  }
}

// 3. Redux DevTools - disable in production
const store = configureStore({
  reducer: rootReducer,
  devTools: process.env.NODE_ENV !== 'production', // Disable in prod
});

// 4. Custom serializer to mask sensitive fields in logs
const sanitizeState = (state) => ({
  ...state,
  payment: state.payment ? { ...state.payment, cvv: '***', cardNumber: '****' } : null,
});
```

---

## Full-Stack Security Scenarios

---

### Q35. Design a secure login system covering both React frontend and Node.js backend.

**Answer (Architecture Overview):**

**Flow:**
```
React Login Form
  → POST /api/auth/login (HTTPS + CSRF token)
  → Rate limited (5 attempts / 15 min per IP)
  → Input validation (email format, password length)
  → bcrypt.compare(password, storedHash)
  → On success: generate JWT access (15min) + refresh token (7 days)
  → Set tokens in httpOnly, Secure, SameSite=Strict cookies
  → Return user object (no tokens in body)
```

**Backend:**
```javascript
app.post('/api/auth/login',
  authLimiter,
  body('email').isEmail().normalizeEmail(),
  body('password').isLength({ min: 1, max: 128 }),
  async (req, res) => {
    const { email, password } = req.body;

    // Timing-safe lookup (prevent username enumeration)
    const user = await User.findOne({ email });
    const hash = user?.password || '$2b$12$InvalidPlaceholderToPreventTimingAttack';
    const isValid = await bcrypt.compare(password, hash);

    if (!user || !isValid) {
      await logFailedAttempt(email, req.ip);
      return res.status(401).json({ error: 'Invalid credentials' });
    }

    const accessToken = jwt.sign({ userId: user._id }, process.env.JWT_SECRET, { expiresIn: '15m' });
    const refreshToken = crypto.randomBytes(64).toString('hex');
    await RefreshToken.create({ token: refreshToken, userId: user._id });

    const cookieOpts = { httpOnly: true, secure: true, sameSite: 'Strict' };
    res.cookie('accessToken', accessToken, { ...cookieOpts, maxAge: 900000 });
    res.cookie('refreshToken', refreshToken, { ...cookieOpts, maxAge: 604800000 });

    res.json({ user: { id: user._id, email: user.email, role: user.role } });
  }
);
```

**React Frontend:**
```jsx
async function handleLogin(email, password) {
  try {
    const res = await fetch('/api/auth/login', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ email, password }),
      credentials: 'include',
    });

    if (!res.ok) {
      const err = await res.json();
      setError(err.error || 'Login failed');
      return;
    }

    const { user } = await res.json();
    dispatch(setUser(user));
    navigate('/dashboard');
  } catch {
    setError('Network error. Please try again.');
  }
}
```

---

### Q36. What is a Security Headers checklist for a production Node.js + React app?

**Answer:**

```javascript
// Complete security headers setup
app.use(helmet({
  // 1. HSTS - Force HTTPS for 1 year
  hsts: { maxAge: 31536000, includeSubDomains: true, preload: true },

  // 2. CSP - Prevent XSS
  contentSecurityPolicy: {
    directives: {
      defaultSrc: ["'self'"],
      scriptSrc: ["'self'"],
      styleSrc: ["'self'", "'unsafe-inline'"],
      imgSrc: ["'self'", "data:", "https:"],
      connectSrc: ["'self'"],
      frameAncestors: ["'none'"],        // Prevent clickjacking
      upgradeInsecureRequests: [],
    },
  },

  // 3. Prevent MIME sniffing
  noSniff: true,

  // 4. Prevent clickjacking (in addition to CSP)
  frameguard: { action: 'deny' },

  // 5. Hide Express info
  hidePoweredBy: true,

  // 6. Referrer Policy
  referrerPolicy: { policy: 'strict-origin-when-cross-origin' },

  // 7. Permissions Policy
  permittedCrossDomainPolicies: false,
}));

// Additional manual headers
app.use((req, res, next) => {
  res.setHeader('Permissions-Policy', 'geolocation=(), microphone=(), camera=()');
  res.setHeader('Cache-Control', 'no-store'); // Prevent sensitive data caching
  next();
});
```

---

### Q37. How do you handle security logging and monitoring?

**Answer:**
```javascript
const winston = require('winston');

// Structured security logger
const securityLogger = winston.createLogger({
  level: 'info',
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.json()
  ),
  transports: [
    new winston.transports.File({ filename: 'security.log' }),
  ],
});

// Log security events
function logSecurityEvent(type, data) {
  securityLogger.warn({
    type,
    timestamp: new Date().toISOString(),
    ip: data.ip,
    userId: data.userId,
    // Never log: passwords, tokens, credit cards
    ...data,
  });
}

// Use in middleware
app.post('/login', async (req, res) => {
  const result = await attemptLogin(req.body.email, req.body.password);

  if (!result.success) {
    logSecurityEvent('LOGIN_FAILED', {
      ip: req.ip,
      email: req.body.email,
      reason: result.reason,
    });
    // Trigger alert after N failures
    return res.status(401).json({ error: 'Invalid credentials' });
  }

  logSecurityEvent('LOGIN_SUCCESS', { ip: req.ip, userId: result.user.id });
});

// Monitor for suspicious patterns
// - Multiple failed logins from same IP
// - Logins from unusual geolocations
// - Token reuse after rotation
// - Requests with malformed payloads
```

---

## Quick Reference Summary

| Vulnerability | Prevention in Node.js | Prevention in React |
|---|---|---|
| **XSS** | Helmet CSP, input sanitization | Auto-escaping, DOMPurify |
| **SQL/NoSQL Injection** | Parameterized queries, mongoose-sanitize | N/A (backend concern) |
| **CSRF** | SameSite cookies, CSRF tokens | Include CSRF header in requests |
| **Clickjacking** | X-Frame-Options, CSP frame-ancestors | frame-buster script |
| **Broken Auth** | bcrypt, rate limiting, JWT best practices | Protected routes, httpOnly cookies |
| **Sensitive Exposure** | Avoid verbose errors, HTTPS | No secrets in code, no tokens in state |
| **Insecure Deps** | npm audit, Snyk, lockfiles | Same + bundle analysis |
| **Prototype Pollution** | Use Object.keys(), avoid deep merges | N/A |
| **SSRF** | URL allowlist, IP range blocking | Proxy through backend |
| **Command Injection** | execFile with args array | N/A |

---

*This document covers security interview questions from beginner to expert level for Node.js and React development. Always remember: security is a continuous process, not a one-time implementation.*
