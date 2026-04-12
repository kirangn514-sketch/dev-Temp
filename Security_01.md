# Security in Node.js & React Development
### JWT · Cipher Suites · Encryption · HTTPS · TLS · SQL Injection · OWASP Top 10 Mitigation

---

## TABLE OF CONTENTS

1. [HTTPS & TLS/SSL](#1-https--tlsssl)
2. [Cipher Suites & Encryption Algorithms](#2-cipher-suites--encryption-algorithms)
3. [Hashing Algorithms](#3-hashing-algorithms)
4. [JWT — JSON Web Tokens](#4-jwt--json-web-tokens)
5. [Authentication & Authorization Patterns](#5-authentication--authorization-patterns)
6. [SQL Injection & NoSQL Injection](#6-sql-injection--nosql-injection)
7. [OWASP Top 10 — 2021 Edition with Mitigations](#7-owasp-top-10--2021-edition-with-mitigations)
8. [Node.js Security Best Practices](#8-nodejs-security-best-practices)
9. [React Security Best Practices](#9-react-security-best-practices)
10. [Security Headers & CORS](#10-security-headers--cors)
11. [Secrets & Environment Management](#11-secrets--environment-management)
12. [Security Checklist — Production Ready](#12-security-checklist--production-ready)

---

## 1. HTTPS & TLS/SSL

---

### What is HTTPS?

**HTTPS (HyperText Transfer Protocol Secure)** is HTTP layered on top of **TLS (Transport Layer Security)**. It provides:

- **Confidentiality** — Data is encrypted; eavesdroppers see only ciphertext
- **Integrity** — Data cannot be tampered in transit (MAC/HMAC)
- **Authentication** — Server identity verified via digital certificate (PKI)

```
HTTP  → Data sent in plain text    → Insecure
HTTPS → Data encrypted via TLS     → Secure
```

---

### What is TLS?

**TLS (Transport Layer Security)** is the cryptographic protocol that secures communication over a network. It replaced the older SSL (Secure Sockets Layer). Despite being deprecated, "SSL" is still commonly used to refer to TLS.

**TLS Versions:**

| Version | Status | Notes |
|---|---|---|
| SSL 2.0 | Deprecated | Broken — do not use |
| SSL 3.0 | Deprecated | POODLE attack — do not use |
| TLS 1.0 | Deprecated (2020) | Vulnerable — disabled by most browsers |
| TLS 1.1 | Deprecated (2020) | Disabled by most browsers |
| TLS 1.2 | Supported | Widely used; secure with proper cipher config |
| TLS 1.3 | Recommended | Fastest, most secure; reduced handshake round trips |

---

### TLS Handshake (How HTTPS Connection is Established)

**TLS 1.2 Handshake (2 round trips):**

```
Client                                    Server
  |                                          |
  |--- ClientHello ---------------------->   |  (TLS version, cipher suites, random)
  |<-- ServerHello ----------------------    |  (chosen cipher, random)
  |<-- Certificate ----------------------    |  (server's public key + cert)
  |<-- ServerHelloDone ------------------    |
  |                                          |
  |--- ClientKeyExchange --------------->    |  (pre-master secret, encrypted with server pub key)
  |--- ChangeCipherSpec  --------------->    |
  |--- Finished ----------------------->     |
  |<-- ChangeCipherSpec -----------------    |
  |<-- Finished -----------------------      |
  |                                          |
  |=== Encrypted Application Data ========= |
```

**TLS 1.3 Handshake (1 round trip — faster):**

```
Client                                    Server
  |                                          |
  |--- ClientHello (+ key share) ------->    |  (sends key share upfront)
  |<-- ServerHello + Certificate --------    |  (immediately derives keys)
  |<-- Finished -------------------------    |
  |--- Finished ----------------------->     |
  |                                          |
  |=== Encrypted Application Data ========= |
  (also supports 0-RTT for resumption)
```

**TLS 1.3 improvements:**
- Removed weak cipher suites (RC4, DES, 3DES, MD5, SHA-1, RSA key exchange)
- Only forward-secret key exchange (ECDHE/DHE)
- Reduced handshake from 2 round trips to 1
- 0-RTT session resumption (faster reconnects)

---

### Setting Up HTTPS in Node.js

```javascript
// Using Node.js built-in https module
const https = require('https');
const fs = require('fs');

const options = {
  key:  fs.readFileSync('./certs/private-key.pem'),
  cert: fs.readFileSync('./certs/certificate.pem'),
  ca:   fs.readFileSync('./certs/ca-bundle.pem'),   // intermediate certs

  // TLS configuration — enforce TLS 1.2+
  minVersion: 'TLSv1.2',
  maxVersion: 'TLSv1.3',

  // Only allow secure cipher suites
  ciphers: [
    'TLS_AES_256_GCM_SHA384',          // TLS 1.3
    'TLS_CHACHA20_POLY1305_SHA256',    // TLS 1.3
    'TLS_AES_128_GCM_SHA256',          // TLS 1.3
    'ECDHE-RSA-AES256-GCM-SHA384',    // TLS 1.2
    'ECDHE-RSA-AES128-GCM-SHA256',    // TLS 1.2
    'ECDHE-RSA-CHACHA20-POLY1305',    // TLS 1.2
  ].join(':'),

  honorCipherOrder: true,   // server chooses cipher, not client
};

const app = require('./app');  // your Express app
https.createServer(options, app).listen(443, () => {
  console.log('HTTPS server running on port 443');
});

// Redirect HTTP → HTTPS
const http = require('http');
http.createServer((req, res) => {
  res.writeHead(301, { Location: `https://${req.headers.host}${req.url}` });
  res.end();
}).listen(80);
```

```javascript
// Using Express with HTTPS
const express = require('express');
const app = express();

// Force HTTPS in production
app.use((req, res, next) => {
  if (process.env.NODE_ENV === 'production' && !req.secure) {
    return res.redirect(301, `https://${req.headers.host}${req.url}`);
  }
  next();
});
```

```javascript
// Using Let's Encrypt with Certbot (free SSL)
// After certbot generates certs, they are usually at:
// /etc/letsencrypt/live/<domain>/privkey.pem
// /etc/letsencrypt/live/<domain>/fullchain.pem

const options = {
  key:  fs.readFileSync('/etc/letsencrypt/live/yourdomain.com/privkey.pem'),
  cert: fs.readFileSync('/etc/letsencrypt/live/yourdomain.com/fullchain.pem'),
};
```

---

### Certificate Pinning

Certificate pinning ensures your app only accepts a specific certificate or public key, preventing man-in-the-middle attacks with fraudulent certificates.

```javascript
// In Node.js — verify certificate fingerprint
const https = require('https');
const tls = require('tls');

const EXPECTED_FINGERPRINT = 'AA:BB:CC:DD:EE:FF:...'; // SHA-256 fingerprint

const req = https.request({
  hostname: 'api.example.com',
  port: 443,
  checkServerIdentity: (host, cert) => {
    const fingerprint = cert.fingerprint256;
    if (fingerprint !== EXPECTED_FINGERPRINT) {
      return new Error('Certificate fingerprint mismatch — possible MITM!');
    }
  }
}, (res) => {
  // handle response
});
```

---

## 2. CIPHER SUITES & ENCRYPTION ALGORITHMS

---

### What is a Cipher Suite?

A cipher suite is a **named combination of cryptographic algorithms** used during a TLS session. It specifies:

```
TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
 |      |      |       |    |    |    |
 |      |      |       |    |    |    └── Hash algorithm (MAC: SHA-384)
 |      |      |       |    |    └────── Mode (GCM = Galois/Counter Mode)
 |      |      |       |    └───────────── Key size (256 bits)
 |      |      |       └────────────────── Bulk cipher (AES)
 |      |      └────────────────────────── Authentication (RSA)
 |      └───────────────────────────────── Key exchange (ECDHE)
 └──────────────────────────────────────── Protocol (TLS)
```

**Components of a Cipher Suite:**

| Component | Purpose | Examples |
|---|---|---|
| Key Exchange | Securely exchange symmetric key | ECDHE, DHE, RSA |
| Authentication | Verify server identity | RSA, ECDSA, DSA |
| Bulk Cipher | Encrypt the actual data | AES-256-GCM, ChaCha20, 3DES |
| MAC/Hash | Ensure data integrity | SHA-256, SHA-384, POLY1305 |

---

### Key Exchange Algorithms

**ECDHE (Elliptic Curve Diffie-Hellman Ephemeral) — Recommended**
- Provides **Perfect Forward Secrecy (PFS)**
- Uses elliptic curves (e.g., P-256, X25519) — smaller keys, same security
- "Ephemeral" = new key pair per session; compromising one session does NOT compromise past sessions

**DHE (Diffie-Hellman Ephemeral)**
- Provides PFS but slower than ECDHE
- Requires large key sizes (2048+ bits) for security

**RSA Key Exchange (Legacy — avoid)**
- Does NOT provide PFS
- If server's private key is compromised, ALL past sessions can be decrypted
- Removed in TLS 1.3

```
Perfect Forward Secrecy (PFS):
Session 1 → ephemeral key K1 → encrypted with K1
Session 2 → ephemeral key K2 → encrypted with K2
Session 3 → ephemeral key K3 → encrypted with K3

Even if the server's long-term private key is stolen:
→ Past sessions CANNOT be decrypted (each had a unique ephemeral key)

Without PFS (RSA key exchange):
→ Record ALL traffic now, decrypt LATER when private key is stolen
```

---

### Symmetric Encryption Algorithms

Used for **bulk data encryption** (fast, same key for encrypt/decrypt).

**AES (Advanced Encryption Standard) — Most Widely Used**

```javascript
const crypto = require('crypto');

// AES-256-GCM — Recommended (Authenticated Encryption)
function encrypt(plaintext, key) {
  const iv = crypto.randomBytes(16);        // random initialization vector
  const cipher = crypto.createCipheriv('aes-256-gcm', key, iv);

  let encrypted = cipher.update(plaintext, 'utf8', 'hex');
  encrypted += cipher.final('hex');
  const authTag = cipher.getAuthTag();      // authentication tag (integrity check)

  return {
    encrypted,
    iv: iv.toString('hex'),
    authTag: authTag.toString('hex')
  };
}

function decrypt(encryptedData, key) {
  const iv = Buffer.from(encryptedData.iv, 'hex');
  const authTag = Buffer.from(encryptedData.authTag, 'hex');

  const decipher = crypto.createDecipheriv('aes-256-gcm', key, iv);
  decipher.setAuthTag(authTag);             // verify integrity on decrypt

  let decrypted = decipher.update(encryptedData.encrypted, 'hex', 'utf8');
  decrypted += decipher.final('utf8');
  return decrypted;
}

// Generate a 256-bit (32-byte) key
const key = crypto.randomBytes(32);

const data = encrypt('Sensitive data here', key);
const original = decrypt(data, key);

// AES modes comparison:
// AES-256-GCM  → Authenticated encryption (confidentiality + integrity) ✅ USE THIS
// AES-256-CBC  → Older; needs separate MAC for integrity; padding oracle attacks
// AES-256-CTR  → Stream mode; fast; no padding; needs separate MAC
// AES-256-ECB  → NEVER USE — identical blocks produce identical ciphertext (leaks patterns)
```

**ChaCha20-Poly1305 — Modern Alternative**
- Designed by D. J. Bernstein
- Faster than AES on devices without hardware AES acceleration (mobile/IoT)
- Built-in authentication (like GCM)
- Used in TLS 1.3

```javascript
// ChaCha20-Poly1305
const cipher = crypto.createCipheriv('chacha20-poly1305', key, nonce, {
  authTagLength: 16
});
```

**AES Key Sizes:**
- AES-128 → 128-bit key → Secure (fast)
- AES-192 → 192-bit key → More secure
- AES-256 → 256-bit key → Most secure (recommended for sensitive data)

---

### Asymmetric Encryption Algorithms

Uses a **public key to encrypt** and **private key to decrypt** (or vice versa for signatures). Slower than symmetric — used for key exchange and signatures.

**RSA (Rivest–Shamir–Adleman)**

```javascript
const { generateKeyPairSync, publicEncrypt, privateDecrypt } = require('crypto');

// Generate RSA key pair (2048-bit minimum; 4096-bit for high security)
const { publicKey, privateKey } = generateKeyPairSync('rsa', {
  modulusLength: 2048,
  publicKeyEncoding:  { type: 'pkcs1', format: 'pem' },
  privateKeyEncoding: { type: 'pkcs1', format: 'pem' },
});

// Encrypt with public key
const encrypted = publicEncrypt(
  { key: publicKey, padding: crypto.constants.RSA_PKCS1_OAEP_PADDING },
  Buffer.from('Secret message')
);

// Decrypt with private key
const decrypted = privateDecrypt(
  { key: privateKey, padding: crypto.constants.RSA_PKCS1_OAEP_PADDING },
  encrypted
);
console.log(decrypted.toString()); // "Secret message"

// RSA padding schemes:
// PKCS1v1.5    → Legacy; vulnerable to Bleichenbacher attack — AVOID
// OAEP         → Recommended for encryption
// PSS          → Recommended for signatures
```

**ECDSA (Elliptic Curve Digital Signature Algorithm)**
- Smaller keys, same security as RSA (256-bit ECDSA ≈ 3072-bit RSA)
- Faster signature generation and verification

```javascript
const { generateKeyPairSync, createSign, createVerify } = require('crypto');

// Generate EC key pair
const { privateKey, publicKey } = generateKeyPairSync('ec', {
  namedCurve: 'P-256',   // NIST P-256 (secp256r1) or 'secp256k1', 'Ed25519'
});

// Sign data
const sign = createSign('SHA256');
sign.update('data to sign');
const signature = sign.sign(privateKey, 'hex');

// Verify signature
const verify = createVerify('SHA256');
verify.update('data to sign');
const isValid = verify.verify(publicKey, signature, 'hex');
console.log(isValid); // true
```

---

### Key Derivation Functions (KDF)

Used to derive cryptographic keys from passwords or other keys.

```javascript
const crypto = require('crypto');

// PBKDF2 — Password-Based Key Derivation Function 2
crypto.pbkdf2('password', 'salt', 100000, 32, 'sha256', (err, key) => {
  console.log(key.toString('hex')); // 256-bit derived key
});

// scrypt — Memory-hard; more resistant to GPU/ASIC attacks (recommended)
const key = crypto.scryptSync('password', 'salt', 32, {
  N: 16384,   // CPU/memory cost (power of 2)
  r: 8,       // block size
  p: 1,       // parallelization
});

// Argon2 — Winner of Password Hashing Competition (use via argon2 npm package)
const argon2 = require('argon2');
const hash = await argon2.hash('password', {
  type: argon2.argon2id,     // argon2id recommended
  memoryCost: 65536,          // 64 MB
  timeCost: 3,                // iterations
  parallelism: 4,
});
const valid = await argon2.verify(hash, 'password'); // true
```

---

## 3. HASHING ALGORITHMS

---

### What is Hashing?

Hashing is a **one-way function** that converts input of any size to a fixed-size output (digest). Unlike encryption, it cannot be reversed.

**Properties of a Cryptographic Hash:**
- **Deterministic** — Same input always produces same output
- **One-way** — Cannot reverse hash to get original input
- **Avalanche effect** — Small change in input → completely different output
- **Collision resistant** — Infeasible to find two inputs with same hash

```javascript
const crypto = require('crypto');

// SHA-256 (256-bit output) — secure for most uses
const hash = crypto.createHash('sha256').update('data').digest('hex');

// SHA-512 (512-bit output) — higher security
const hash512 = crypto.createHash('sha512').update('data').digest('hex');

// HMAC — Hash-based Message Authentication Code
// Requires a secret key; used for JWT signatures and API authentication
const hmac = crypto.createHmac('sha256', 'secret-key')
  .update('message')
  .digest('hex');
```

**Algorithm Security Status:**

| Algorithm | Output | Status | Use |
|---|---|---|---|
| MD5 | 128 bits | Broken — collisions found | File checksums only (not security) |
| SHA-1 | 160 bits | Broken — collisions found | Avoid for security |
| SHA-256 | 256 bits | Secure | General purpose hashing |
| SHA-384 | 384 bits | Secure | Higher security needs |
| SHA-512 | 512 bits | Secure | High security |
| SHA-3 (Keccak) | Variable | Secure | Modern alternative to SHA-2 |
| bcrypt | 60 chars | Secure (slow by design) | **Password hashing** |
| Argon2 | Variable | Most secure | **Password hashing** (recommended) |

### Password Hashing (Critical — Never use SHA for passwords)

```javascript
// bcrypt — industry standard for password hashing
const bcrypt = require('bcrypt');

// Hash password (saltRounds = cost factor; higher = slower = more secure)
const saltRounds = 12; // ~250ms on modern hardware; recommended: 10-14
const hashedPassword = await bcrypt.hash('userPassword123', saltRounds);
// Output: "$2b$12$..." (includes salt + cost factor embedded in output)

// Verify password
const isMatch = await bcrypt.compare('userPassword123', hashedPassword); // true
const isWrong = await bcrypt.compare('wrongPassword', hashedPassword);   // false

// Never store plain passwords
// Never use MD5/SHA for passwords — too fast; rainbow tables/brute force possible
// bcrypt automatically salts — no need to manage salt separately

// Express route example
app.post('/register', async (req, res) => {
  const { email, password } = req.body;

  // Validate password strength
  if (password.length < 8) {
    return res.status(400).json({ error: 'Password too short' });
  }

  const hashedPassword = await bcrypt.hash(password, 12);
  await db.users.create({ email, password: hashedPassword });
  res.status(201).json({ message: 'User created' });
});

app.post('/login', async (req, res) => {
  const { email, password } = req.body;
  const user = await db.users.findOne({ email });

  if (!user || !(await bcrypt.compare(password, user.password))) {
    // Same error for both — don't reveal which was wrong (username enumeration)
    return res.status(401).json({ error: 'Invalid credentials' });
  }

  // Issue token...
});
```

---

## 4. JWT — JSON WEB TOKENS

---

### What is JWT?

A **JWT (JSON Web Token)** is a compact, URL-safe token for securely transmitting information between parties. It is commonly used for **authentication and authorization**.

### JWT Structure

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0IiwibmFtZSI6IktpcmFuIiwiaWF0IjoxNzAwMDAwMDAwfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
|___________________________|         |___________________________________________|   |__________________________________________|
         Header (base64)                        Payload (base64)                               Signature
```

**Header:**
```json
{
  "alg": "HS256",    // algorithm used to sign
  "typ": "JWT"       // token type
}
```

**Payload (Claims):**
```json
{
  "sub":   "user_123",          // subject (user ID)
  "name":  "Kiran",             // custom claim
  "email": "kiran@example.com",
  "role":  "admin",
  "iat":   1700000000,          // issued at (Unix timestamp)
  "exp":   1700003600,          // expiry (1 hour later)
  "nbf":   1700000000,          // not before
  "iss":   "api.myapp.com",     // issuer
  "aud":   "myapp.com"          // audience
}
```

**Signature:**
```
HMACSHA256(
  base64url(header) + "." + base64url(payload),
  secret
)
```

> **Important:** JWT payload is base64-encoded, NOT encrypted. Anyone can decode it. Never put sensitive data (passwords, PII) in JWT payload unless using JWE (encrypted JWT).

---

### JWT in Node.js — Full Implementation

```javascript
const jwt = require('jsonwebtoken');

const ACCESS_TOKEN_SECRET  = process.env.JWT_ACCESS_SECRET;   // strong random string (256-bit+)
const REFRESH_TOKEN_SECRET = process.env.JWT_REFRESH_SECRET;  // different secret

// --- GENERATE TOKENS ---

function generateAccessToken(user) {
  return jwt.sign(
    {
      sub:   user.id,
      email: user.email,
      role:  user.role,
    },
    ACCESS_TOKEN_SECRET,
    {
      expiresIn:  '15m',          // short-lived access token
      issuer:     'api.myapp.com',
      audience:   'myapp.com',
      algorithm:  'HS256',        // HMAC with SHA-256
    }
  );
}

function generateRefreshToken(user) {
  return jwt.sign(
    { sub: user.id },
    REFRESH_TOKEN_SECRET,
    { expiresIn: '7d' }           // long-lived refresh token
  );
}

// --- VERIFY TOKEN (middleware) ---

function authenticateToken(req, res, next) {
  const authHeader = req.headers['authorization'];
  const token = authHeader && authHeader.split(' ')[1]; // "Bearer <token>"

  if (!token) {
    return res.status(401).json({ error: 'Access token required' });
  }

  jwt.verify(token, ACCESS_TOKEN_SECRET, {
    issuer:   'api.myapp.com',
    audience: 'myapp.com',
  }, (err, decoded) => {
    if (err) {
      if (err.name === 'TokenExpiredError') {
        return res.status(401).json({ error: 'Token expired', code: 'TOKEN_EXPIRED' });
      }
      if (err.name === 'JsonWebTokenError') {
        return res.status(403).json({ error: 'Invalid token' });
      }
      return res.status(403).json({ error: 'Token verification failed' });
    }

    req.user = decoded;  // attach decoded payload to request
    next();
  });
}

// --- LOGIN ROUTE ---

app.post('/auth/login', async (req, res) => {
  const { email, password } = req.body;
  const user = await User.findOne({ email });

  if (!user || !(await bcrypt.compare(password, user.password))) {
    return res.status(401).json({ error: 'Invalid credentials' });
  }

  const accessToken  = generateAccessToken(user);
  const refreshToken = generateRefreshToken(user);

  // Store refresh token in DB (for revocation)
  await RefreshToken.create({ token: refreshToken, userId: user.id });

  // Send refresh token in httpOnly cookie (NOT accessible by JavaScript)
  res.cookie('refreshToken', refreshToken, {
    httpOnly:  true,          // not accessible via JS — prevents XSS theft
    secure:    true,          // HTTPS only
    sameSite:  'strict',      // CSRF protection
    maxAge:    7 * 24 * 60 * 60 * 1000,  // 7 days
  });

  // Send access token in response body (store in memory, NOT localStorage)
  res.json({ accessToken });
});

// --- TOKEN REFRESH ROUTE ---

app.post('/auth/refresh', async (req, res) => {
  const refreshToken = req.cookies.refreshToken;
  if (!refreshToken) return res.status(401).json({ error: 'Refresh token required' });

  // Check if token is in DB (not revoked)
  const stored = await RefreshToken.findOne({ token: refreshToken });
  if (!stored) return res.status(403).json({ error: 'Invalid refresh token' });

  jwt.verify(refreshToken, REFRESH_TOKEN_SECRET, async (err, decoded) => {
    if (err) return res.status(403).json({ error: 'Invalid refresh token' });

    const user = await User.findById(decoded.sub);
    const newAccessToken = generateAccessToken(user);

    // Optionally rotate refresh token (better security)
    await RefreshToken.deleteOne({ token: refreshToken });
    const newRefreshToken = generateRefreshToken(user);
    await RefreshToken.create({ token: newRefreshToken, userId: user.id });

    res.cookie('refreshToken', newRefreshToken, { httpOnly: true, secure: true, sameSite: 'strict' });
    res.json({ accessToken: newAccessToken });
  });
});

// --- LOGOUT ---

app.post('/auth/logout', async (req, res) => {
  const refreshToken = req.cookies.refreshToken;
  await RefreshToken.deleteOne({ token: refreshToken }); // revoke from DB
  res.clearCookie('refreshToken');
  res.json({ message: 'Logged out' });
});

// --- PROTECTED ROUTE ---

app.get('/api/profile', authenticateToken, (req, res) => {
  res.json({ user: req.user });
});
```

### JWT Algorithm Choices

```javascript
// HS256 — HMAC-SHA256 (symmetric — same secret for sign and verify)
// Use when: same server signs and verifies
jwt.sign(payload, 'shared-secret', { algorithm: 'HS256' });

// RS256 — RSA-SHA256 (asymmetric — private key signs, public key verifies)
// Use when: multiple services verify tokens; public key can be shared safely
const privateKey = fs.readFileSync('private.pem');
const publicKey  = fs.readFileSync('public.pem');
jwt.sign(payload, privateKey, { algorithm: 'RS256' });
jwt.verify(token, publicKey, { algorithms: ['RS256'] });

// ES256 — ECDSA-SHA256 (asymmetric — smaller keys than RSA)
// Use when: same as RS256 but need smaller token size

// Algorithm security comparison:
// HS256: Fast, simple — secret must stay private
// RS256: Public key shareable — better for microservices
// ES256: Like RS256 but smaller/faster — modern choice
// AVOID: 'none' algorithm — NEVER accept tokens with alg: 'none'

// Always specify allowed algorithms on verify
jwt.verify(token, secret, { algorithms: ['HS256'] }); // don't allow 'none'
```

### JWT Security Best Practices

```
DO:
✅ Use short expiry for access tokens (15 min)
✅ Store access token in memory (JS variable), NOT localStorage
✅ Store refresh token in httpOnly, Secure, SameSite=Strict cookie
✅ Always verify algorithm — prevent 'alg: none' attacks
✅ Validate all claims (iss, aud, exp, nbf)
✅ Use strong secrets (256-bit random for HS256)
✅ Implement token rotation for refresh tokens
✅ Maintain a token revocation list (DB) for refresh tokens
✅ Use RS256/ES256 for distributed systems (microservices)

DON'T:
❌ Store sensitive data in payload (it's only base64-encoded)
❌ Store tokens in localStorage (XSS vulnerability)
❌ Use long-lived access tokens
❌ Use weak secrets ("password", "secret")
❌ Accept tokens with 'none' algorithm
❌ Skip verification of exp, iss, aud claims
```

---

## 5. AUTHENTICATION & AUTHORIZATION PATTERNS

---

### Role-Based Access Control (RBAC)

```javascript
// Middleware for role-based authorization
function requireRole(...roles) {
  return (req, res, next) => {
    if (!req.user) {
      return res.status(401).json({ error: 'Authentication required' });
    }

    if (!roles.includes(req.user.role)) {
      return res.status(403).json({ error: 'Insufficient permissions' });
    }

    next();
  };
}

// Routes
app.get('/api/admin/users',   authenticateToken, requireRole('admin'), listUsers);
app.delete('/api/users/:id',  authenticateToken, requireRole('admin', 'moderator'), deleteUser);
app.get('/api/profile',       authenticateToken, requireRole('user', 'admin'), getProfile);

// Permission-based (finer grain)
const PERMISSIONS = {
  admin:     ['read', 'write', 'delete', 'manage_users'],
  moderator: ['read', 'write', 'delete'],
  user:      ['read', 'write'],
  guest:     ['read'],
};

function requirePermission(permission) {
  return (req, res, next) => {
    const userPermissions = PERMISSIONS[req.user.role] || [];
    if (!userPermissions.includes(permission)) {
      return res.status(403).json({ error: `Permission '${permission}' required` });
    }
    next();
  };
}
```

### Rate Limiting (Brute Force Protection)

```javascript
const rateLimit = require('express-rate-limit');
const RedisStore = require('rate-limit-redis');

// General API rate limit
const apiLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,  // 15 minutes
  max: 100,                    // 100 requests per window per IP
  standardHeaders: true,
  legacyHeaders: false,
  message: { error: 'Too many requests, please try again later' },
  store: new RedisStore({ client: redisClient }), // use Redis in production
});

// Stricter limit for auth endpoints
const authLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 5,                      // only 5 login attempts per 15 min
  skipSuccessfulRequests: true, // don't count successful logins
  message: { error: 'Too many login attempts, account locked temporarily' },
});

app.use('/api/', apiLimiter);
app.post('/auth/login', authLimiter, loginHandler);
app.post('/auth/register', authLimiter, registerHandler);
```

---

## 6. SQL INJECTION & NOSQL INJECTION

---

### What is SQL Injection?

SQL Injection is an attack where **malicious SQL code is inserted into a query** through user input, allowing attackers to manipulate the database.

**Classic SQL Injection Example:**

```javascript
// VULNERABLE CODE — NEVER DO THIS
app.get('/user', async (req, res) => {
  const username = req.query.username;

  // Attacker sends: username = ' OR '1'='1
  const query = `SELECT * FROM users WHERE username = '${username}'`;
  // Becomes: SELECT * FROM users WHERE username = '' OR '1'='1'
  // Returns ALL users!

  const result = await db.query(query);
  res.json(result);
});

// MORE DANGEROUS:
// username = '; DROP TABLE users; --
// Query becomes:
// SELECT * FROM users WHERE username = ''; DROP TABLE users; --'
// Drops the entire users table!

// username = ' UNION SELECT username, password FROM admin_users --
// Extracts admin credentials!
```

**Types of SQL Injection:**

```
1. Classic / In-Band
   - Error-based: uses DB error messages to extract info
   - UNION-based: uses UNION to combine results from another table

2. Blind SQL Injection (no visible output)
   - Boolean-based: sends true/false queries; infers from response difference
   - Time-based: uses SLEEP() — delays indicate truthy conditions

3. Out-of-Band
   - Uses DNS/HTTP requests to exfiltrate data
```

### SQL Injection Prevention

```javascript
// ✅ METHOD 1: Parameterized Queries (ALWAYS USE THIS)
// Using 'pg' (PostgreSQL)
const { Pool } = require('pg');
const pool = new Pool();

app.get('/user', async (req, res) => {
  const { username } = req.query;

  // Parameterized query — user input is NEVER part of SQL string
  const result = await pool.query(
    'SELECT id, username, email FROM users WHERE username = $1',
    [username]   // username is passed as a parameter, not embedded in SQL
  );

  res.json(result.rows);
});

// Multiple parameters
const result = await pool.query(
  'SELECT * FROM orders WHERE user_id = $1 AND status = $2 AND amount > $3',
  [userId, status, minAmount]
);

// Using 'mysql2'
const mysql = require('mysql2/promise');
const connection = await mysql.createConnection(config);

const [rows] = await connection.execute(
  'SELECT * FROM users WHERE email = ? AND password = ?',
  [email, hashedPassword]   // ? placeholders — fully parameterized
);

// ✅ METHOD 2: ORM (Object-Relational Mapping)
// Sequelize — all queries are automatically parameterized
const user = await User.findOne({
  where: { username: req.query.username }
});

// Prisma — type-safe ORM
const user = await prisma.user.findUnique({
  where: { username: req.query.username }
});

// ✅ METHOD 3: Stored Procedures (with parameterization)
await pool.query('CALL get_user_by_username($1)', [username]);

// ✅ METHOD 4: Input Validation (Defense in depth)
const { body, query, validationResult } = require('express-validator');

app.get('/user',
  query('username')
    .trim()
    .isAlphanumeric()
    .isLength({ min: 3, max: 50 }),
  (req, res) => {
    const errors = validationResult(req);
    if (!errors.isEmpty()) {
      return res.status(400).json({ errors: errors.array() });
    }
    // now safe to use req.query.username
  }
);

// ✅ METHOD 5: Least Privilege Database User
// DB user should only have SELECT/INSERT/UPDATE — NOT DROP/ALTER/CREATE
// CREATE USER 'app_user'@'localhost' IDENTIFIED BY 'password';
// GRANT SELECT, INSERT, UPDATE ON myapp.* TO 'app_user'@'localhost';
// No DROP, CREATE, ALTER permissions
```

### NoSQL Injection (MongoDB)

```javascript
// VULNERABLE — MongoDB injection via JSON operator injection
app.post('/login', async (req, res) => {
  const { username, password } = req.body;

  // Attacker sends: { "username": { "$ne": null }, "password": { "$ne": null } }
  // $ne = not equal — matches ALL users!
  const user = await User.findOne({ username, password }); // VULNERABLE
});

// ✅ PREVENTION — Input validation and sanitization
const mongoSanitize = require('express-mongo-sanitize');

// Remove $ and . from user input (strips operator injection)
app.use(mongoSanitize({
  replaceWith: '_',  // replace $ with _ instead of removing
}));

// Or manual check
function sanitizeInput(input) {
  if (typeof input === 'object') {
    for (const key in input) {
      if (key.startsWith('$')) {
        throw new Error('Invalid input: MongoDB operators not allowed');
      }
    }
  }
  return input;
}

// Use mongoose schema validation
const userSchema = new mongoose.Schema({
  username: {
    type: String,
    required: true,
    match: /^[a-zA-Z0-9_]{3,50}$/,  // only alphanumeric + underscore
  },
  email: {
    type: String,
    required: true,
    lowercase: true,
    validate: {
      validator: (v) => /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(v),
      message: 'Invalid email format',
    },
  },
});
```

---

## 7. OWASP TOP 10 — 2021 EDITION WITH MITIGATIONS

---

### Overview

The **OWASP Top 10** is a standard awareness document representing the most critical security risks to web applications.

---

### A01 — Broken Access Control (Most Common)

**What it is:** Users can access resources or perform actions they shouldn't be permitted to do.

**Attack examples:**
- Accessing another user's data by changing URL parameter: `/api/users/123` → `/api/users/456`
- Privilege escalation: regular user accesses admin endpoints
- CORS misconfiguration allowing unauthorized API access

**Mitigation in Node.js:**

```javascript
// ❌ VULNERABLE — no ownership check
app.get('/api/orders/:id', authenticateToken, async (req, res) => {
  const order = await Order.findById(req.params.id);
  res.json(order);  // any authenticated user can see ANY order!
});

// ✅ SECURE — verify resource ownership
app.get('/api/orders/:id', authenticateToken, async (req, res) => {
  const order = await Order.findOne({
    _id: req.params.id,
    userId: req.user.sub   // ensure order belongs to requesting user
  });

  if (!order) {
    return res.status(404).json({ error: 'Order not found' });
    // 404 instead of 403 — don't reveal resource exists for other users
  }

  res.json(order);
});

// Centralized authorization — use libraries like CASL
const { AbilityBuilder, createMongoAbility } = require('@casl/ability');

function defineAbilitiesFor(user) {
  const { can, cannot, build } = new AbilityBuilder(createMongoAbility);

  if (user.role === 'admin') {
    can('manage', 'all');           // admins can do everything
  } else {
    can('read', 'Article');         // users can read articles
    can('create', 'Article');
    can('update', 'Article', { authorId: user.id }); // only own articles
    cannot('delete', 'Article');    // cannot delete
  }

  return build();
}
```

---

### A02 — Cryptographic Failures

**What it is:** Sensitive data exposed due to weak/missing encryption, or cryptographic mistakes.

**Attack examples:**
- Passwords stored in plain text or with MD5/SHA1
- Sensitive data transmitted over HTTP
- Weak keys, outdated algorithms (DES, RC4, MD5)

**Mitigation:**

```javascript
// ❌ NEVER store passwords in plain text or weak hash
user.password = md5(password);        // WRONG — MD5 is broken
user.password = sha256(password);     // WRONG — SHA is too fast for passwords
user.password = password;             // CATASTROPHIC

// ✅ Use bcrypt or Argon2 for passwords
user.password = await bcrypt.hash(password, 12);

// ✅ Encrypt PII (Personally Identifiable Information) at rest
const encryptedSSN = encrypt(user.ssn, ENCRYPTION_KEY);
await db.save({ ...user, ssn: encryptedSSN, ssnPlain: undefined });

// ✅ Never log sensitive data
// BAD:
console.log('User login:', { email, password });   // logs password!
// GOOD:
console.log('User login attempt:', { email });      // log only safe fields

// ✅ Classify data — apply stronger protection to sensitive fields
// Public: user's display name
// Internal: user's email
// Confidential: phone number, address
// Restricted: SSN, credit card, password

// ✅ Use HTTPS for all communication
// ✅ Set HSTS header (see Security Headers section)
```

---

### A03 — Injection (SQL, NoSQL, OS, LDAP)

**What it is:** Untrusted data is sent to an interpreter as part of a command or query.

**Mitigation:** See Section 6 (SQL Injection) above, plus:

```javascript
// OS Command Injection
const { exec } = require('child_process');

// ❌ VULNERABLE
app.get('/ping', (req, res) => {
  exec(`ping -c 4 ${req.query.host}`, (err, stdout) => res.send(stdout));
  // Attacker: host = "google.com; rm -rf /"
});

// ✅ Use execFile with arguments array (no shell injection possible)
const { execFile } = require('child_process');
app.get('/ping', (req, res) => {
  const host = req.query.host;
  // Validate: only allow valid hostnames
  if (!/^[a-zA-Z0-9.-]+$/.test(host)) {
    return res.status(400).json({ error: 'Invalid host' });
  }
  execFile('ping', ['-c', '4', host], (err, stdout) => res.send(stdout));
  // Arguments passed as array — no shell involved — injection impossible
});

// LDAP Injection — sanitize LDAP special characters
const ldapEscape = require('ldap-escape');
const filter = ldapEscape.filter`(username=${req.body.username})`;
```

---

### A04 — Insecure Design

**What it is:** Missing or ineffective security controls from the design phase itself.

**Mitigation:**

```javascript
// Threat modeling — ask for each feature:
// "What's the worst thing an attacker could do with this?"

// ✅ Principle of Least Privilege
// DB user has only needed permissions
// API keys have only needed scopes
// Files have only needed permissions (chmod 640, not 777)

// ✅ Secure defaults
// All endpoints private by default; explicitly mark public ones
// app.use(authenticateToken); // auth all routes by default
// app.get('/health', publicRoute, healthCheck); // explicitly mark public

// ✅ Defense in depth
// Multiple layers: input validation → parameterized queries → WAF → monitoring

// ✅ Fail securely
// On error, deny access — don't silently allow
function authorize(user, resource) {
  try {
    return checkPermission(user, resource);
  } catch (err) {
    return false; // default DENY on error
  }
}
```

---

### A05 — Security Misconfiguration

**What it is:** Incorrect security settings, default configurations, verbose error messages.

**Mitigation:**

```javascript
const express = require('express');
const helmet = require('helmet');

const app = express();

// ✅ Use Helmet.js — sets secure HTTP headers automatically
app.use(helmet());

// ✅ Disable X-Powered-By header (hides tech stack)
app.disable('x-powered-by');
// helmet() already does this

// ✅ Never expose stack traces in production
app.use((err, req, res, next) => {
  console.error(err.stack); // log internally

  if (process.env.NODE_ENV === 'production') {
    res.status(500).json({ error: 'Internal server error' }); // generic message
  } else {
    res.status(500).json({ error: err.message, stack: err.stack }); // dev only
  }
});

// ✅ Set NODE_ENV=production in production
// This disables debug output in many frameworks

// ✅ Remove/disable unused features
// Disable directory listing in Express:
// (Express doesn't enable this by default — don't use serve-index in production)

// ✅ Change default ports, credentials, and configurations
// Don't use default MongoDB port 27017 with no auth
// Don't use default Redis with no password

// ✅ Dependency scanning
// npm audit
// npm audit fix
// Use Snyk: snyk test

// ✅ Remove development endpoints in production
if (process.env.NODE_ENV !== 'production') {
  app.use('/dev-tools', devToolsRouter);
}
```

---

### A06 — Vulnerable and Outdated Components

**What it is:** Using libraries/frameworks with known vulnerabilities.

**Mitigation:**

```bash
# Check for vulnerabilities
npm audit

# Fix automatically where possible
npm audit fix

# Force fix (may break things — test after)
npm audit fix --force

# Check for outdated packages
npm outdated

# Update to latest minor/patch (non-breaking)
npm update

# Specific package
npm install express@latest

# Use automated tools
# Snyk: https://snyk.io
# Dependabot (GitHub): auto PRs for security updates
# OWASP Dependency-Check

# Lock exact versions to prevent unexpected updates
# package-lock.json — commit this to version control

# Use npm ci instead of npm install in CI/CD
npm ci  # installs exact versions from package-lock.json
```

```javascript
// Check package.json regularly
// Avoid packages that are:
// - No longer maintained (last commit > 2 years ago)
// - Have known CVEs
// - Have very few downloads/stars (untrusted)

// Check package authenticity
// npm install --ignore-scripts  // prevent malicious postinstall scripts
```

---

### A07 — Identification and Authentication Failures

**What it is:** Weak authentication, broken session management, credential exposure.

**Mitigation:**

```javascript
// ✅ Strong password policy
const zxcvbn = require('zxcvbn');   // password strength estimator

app.post('/register', async (req, res) => {
  const { password } = req.body;
  const strength = zxcvbn(password);

  if (strength.score < 3) {   // 0-4 scale; require at least 3
    return res.status(400).json({
      error: 'Password too weak',
      suggestions: strength.feedback.suggestions,
    });
  }
  // ...
});

// ✅ Multi-Factor Authentication (MFA/2FA)
const speakeasy = require('speakeasy');
const qrcode = require('qrcode');

// Generate TOTP secret
const secret = speakeasy.generateSecret({ name: 'MyApp' });
// Store secret.base32 in user record
// Show secret.otpauth_url as QR code

// Verify TOTP token
const isValid = speakeasy.totp.verify({
  secret: user.totpSecret,
  encoding: 'base32',
  token: req.body.totpCode,
  window: 1,   // allow 1 period of clock drift
});

// ✅ Account lockout after failed attempts
const LOCKOUT_THRESHOLD = 5;
const LOCKOUT_DURATION  = 15 * 60 * 1000; // 15 minutes

async function trackLoginAttempt(userId, success) {
  if (success) {
    await User.update({ id: userId }, { failedAttempts: 0, lockedUntil: null });
    return;
  }

  const user = await User.findById(userId);
  const attempts = user.failedAttempts + 1;

  if (attempts >= LOCKOUT_THRESHOLD) {
    await User.update({ id: userId }, {
      failedAttempts: attempts,
      lockedUntil: new Date(Date.now() + LOCKOUT_DURATION),
    });
  } else {
    await User.update({ id: userId }, { failedAttempts: attempts });
  }
}

// ✅ Secure password reset
// Use time-limited, single-use, cryptographically random tokens
const crypto = require('crypto');

async function initiatePasswordReset(email) {
  const user = await User.findOne({ email });
  if (!user) return; // don't reveal if email exists

  const token = crypto.randomBytes(32).toString('hex');
  const hashedToken = crypto.createHash('sha256').update(token).digest('hex');
  const expiry = new Date(Date.now() + 60 * 60 * 1000); // 1 hour

  await PasswordReset.create({ userId: user.id, token: hashedToken, expiry });
  await sendEmail(email, `Reset link: /reset-password?token=${token}`);
  // Send raw token via email; store hashed token in DB
}
```

---

### A08 — Software and Data Integrity Failures

**What it is:** Code and infrastructure that does not protect against integrity violations (e.g., insecure deserialization, untrusted update mechanisms).

**Mitigation:**

```javascript
// ✅ Verify integrity of downloaded packages
// Use package-lock.json / yarn.lock — cryptographically pins dependencies
// Use npm ci instead of npm install

// ✅ Subresource Integrity (SRI) for CDN resources
// In React's index.html or server-rendered HTML:
// <script
//   src="https://cdn.example.com/lib.min.js"
//   integrity="sha384-<hash>"
//   crossorigin="anonymous"
// ></script>
// Browser verifies hash before executing — prevents CDN compromise

// ✅ Validate deserialized data
// When deserializing JSON from untrusted sources, validate schema
const Joi = require('joi');

const userSchema = Joi.object({
  id:    Joi.string().uuid().required(),
  name:  Joi.string().min(1).max(100).required(),
  email: Joi.string().email().required(),
  role:  Joi.string().valid('user', 'admin').required(),
});

const { error, value } = userSchema.validate(untrustedData);
if (error) throw new Error('Invalid data structure');

// ✅ NEVER deserialize untrusted data into objects (prototype pollution)
// BAD:
const obj = {};
Object.assign(obj, JSON.parse(userInput));   // possible prototype pollution

// Good: use safe merge or validate schema first
const safe = JSON.parse(userInput);
if (userSchema.validate(safe).error) throw new Error('Invalid');
```

---

### A09 — Security Logging and Monitoring Failures

**What it is:** Insufficient logging and monitoring allows attacks to go undetected.

**Mitigation:**

```javascript
const winston = require('winston');

// ✅ Structured logging
const logger = winston.createLogger({
  level: 'info',
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.errors({ stack: true }),
    winston.format.json()  // machine-readable
  ),
  transports: [
    new winston.transports.File({ filename: 'logs/error.log', level: 'error' }),
    new winston.transports.File({ filename: 'logs/combined.log' }),
  ],
});

if (process.env.NODE_ENV !== 'production') {
  logger.add(new winston.transports.Console({ format: winston.format.simple() }));
}

// ✅ Log security events (what to log)
// - Authentication: logins (success/fail), logouts, password changes
// - Authorization: access denied events
// - Data access: sensitive data read/write
// - Admin actions: user creation, permission changes
// - Input validation failures
// - System errors

// Log login failure
app.post('/auth/login', async (req, res) => {
  const { email } = req.body;
  // ... authentication logic ...

  if (!user || !passwordMatch) {
    logger.warn('Failed login attempt', {
      email,
      ip:        req.ip,
      userAgent: req.headers['user-agent'],
      timestamp: new Date().toISOString(),
      event:     'AUTH_FAILURE',
    });
    return res.status(401).json({ error: 'Invalid credentials' });
  }

  logger.info('Successful login', {
    userId:    user.id,
    ip:        req.ip,
    event:     'AUTH_SUCCESS',
  });
});

// ✅ NEVER log sensitive data
// BAD:  logger.info('Login', { email, password, token });
// GOOD: logger.info('Login', { userId, ip, event: 'LOGIN' });

// ✅ Audit log for sensitive actions
async function auditLog(userId, action, resourceType, resourceId, details = {}) {
  await AuditLog.create({
    userId,
    action,       // 'CREATE', 'READ', 'UPDATE', 'DELETE'
    resourceType, // 'User', 'Order', 'Payment'
    resourceId,
    details,
    ipAddress: req.ip,
    timestamp: new Date(),
  });
}

// ✅ Alerting — alert on suspicious patterns
// - 5+ failed logins from same IP in 5 minutes
// - Login from unusual geographic location
// - Mass data access (> 100 records in 1 request)
// - Admin actions outside business hours
```

---

### A10 — Server-Side Request Forgery (SSRF)

**What it is:** Attacker makes the server perform HTTP requests to unintended locations — often to internal services.

**Attack example:**
```
Attacker submits: { "imageUrl": "http://169.254.169.254/latest/meta-data/iam/security-credentials" }
Server fetches that URL — leaks AWS metadata (credentials, keys)!
```

**Mitigation:**

```javascript
const axios = require('axios');
const dns = require('dns').promises;
const { URL } = require('url');

// ✅ Whitelist allowed domains
const ALLOWED_DOMAINS = ['api.trusted.com', 'cdn.myapp.com'];

async function safeFetch(url) {
  let parsed;
  try {
    parsed = new URL(url);
  } catch {
    throw new Error('Invalid URL');
  }

  // Only allow HTTPS
  if (parsed.protocol !== 'https:') {
    throw new Error('Only HTTPS URLs allowed');
  }

  // Check hostname against whitelist
  if (!ALLOWED_DOMAINS.includes(parsed.hostname)) {
    throw new Error('Domain not allowed');
  }

  // Resolve hostname and block private IP ranges
  const addresses = await dns.resolve4(parsed.hostname);
  for (const addr of addresses) {
    if (isPrivateIP(addr)) {
      throw new Error('Requests to private IPs not allowed');
    }
  }

  return axios.get(url, { timeout: 5000 });
}

function isPrivateIP(ip) {
  const parts = ip.split('.').map(Number);
  return (
    parts[0] === 10 ||                                          // 10.0.0.0/8
    (parts[0] === 172 && parts[1] >= 16 && parts[1] <= 31) ||  // 172.16.0.0/12
    (parts[0] === 192 && parts[1] === 168) ||                   // 192.168.0.0/16
    (parts[0] === 127) ||                                        // 127.0.0.0/8 (loopback)
    (parts[0] === 169 && parts[1] === 254)                      // 169.254.0.0/16 (link-local / AWS metadata)
  );
}
```

---

## 8. NODE.JS SECURITY BEST PRACTICES

---

```javascript
// ✅ 1. Use Helmet.js for security headers
const helmet = require('helmet');
app.use(helmet());

// ✅ 2. Validate and sanitize all user input
const { body, validationResult } = require('express-validator');

app.post('/api/user',
  body('email').isEmail().normalizeEmail(),
  body('name').trim().escape().isLength({ min: 1, max: 100 }),
  body('age').isInt({ min: 0, max: 150 }),
  (req, res) => {
    const errors = validationResult(req);
    if (!errors.isEmpty()) return res.status(400).json({ errors: errors.array() });
    // safe to use req.body
  }
);

// ✅ 3. Limit request body size (prevent DoS)
app.use(express.json({ limit: '10kb' }));
app.use(express.urlencoded({ extended: false, limit: '10kb' }));

// ✅ 4. Prevent parameter pollution
const hpp = require('hpp');
app.use(hpp());
// Prevents: ?sort=asc&sort=desc (duplicates — can crash some apps)

// ✅ 5. Sanitize against XSS
const xss = require('xss');
const cleanInput = xss(req.body.comment);

// ✅ 6. Use environment variables for secrets
// NEVER hardcode secrets in source code
const secret = process.env.JWT_SECRET; // correct
// const secret = "mysecret123";       // WRONG — exposed in git!

// ✅ 7. Avoid eval() and new Function()
// eval('alert(1)')  — NEVER execute dynamic code from user input

// ✅ 8. Prevent directory traversal
const path = require('path');
app.get('/files/:filename', (req, res) => {
  const safePath = path.join(__dirname, 'uploads',
    path.basename(req.params.filename) // basename strips directory components
  );
  // Prevents: /files/../../etc/passwd
  res.sendFile(safePath);
});

// ✅ 9. Set secure cookie options
app.use(session({
  secret: process.env.SESSION_SECRET,
  resave: false,
  saveUninitialized: false,
  cookie: {
    httpOnly:  true,     // no JS access
    secure:    true,     // HTTPS only
    sameSite:  'strict', // CSRF protection
    maxAge:    30 * 60 * 1000, // 30 minutes
  },
  // Use Redis session store in production (not in-memory)
  store: new RedisStore({ client: redisClient }),
}));

// ✅ 10. Keep dependencies updated and scan regularly
// npm audit
// Automated: GitHub Dependabot, Snyk
```

---

## 9. REACT SECURITY BEST PRACTICES

---

### XSS (Cross-Site Scripting) in React

React automatically escapes JSX values — this is a core security feature.

```jsx
// ✅ React automatically escapes — SAFE
const userInput = '<script>alert("xss")</script>';
return <div>{userInput}</div>;
// Renders as: &lt;script&gt;alert("xss")&lt;/script&gt; — harmless text

// ❌ dangerouslySetInnerHTML — bypasses React's escaping — DANGEROUS
// Only use if you absolutely must render raw HTML (e.g., rich text editor output)
return <div dangerouslySetInnerHTML={{ __html: userContent }} />;

// ✅ If you MUST use dangerouslySetInnerHTML — sanitize first with DOMPurify
import DOMPurify from 'dompurify';

const sanitizedHTML = DOMPurify.sanitize(userContent, {
  ALLOWED_TAGS: ['b', 'i', 'em', 'strong', 'p', 'br'],
  ALLOWED_ATTR: [],
});
return <div dangerouslySetInnerHTML={{ __html: sanitizedHTML }} />;

// ❌ javascript: URLs — XSS via href
const url = `javascript:alert('xss')`;
return <a href={url}>Click me</a>; // DANGEROUS

// ✅ Validate URLs before using in href
function isSafeUrl(url) {
  try {
    const parsed = new URL(url);
    return ['http:', 'https:'].includes(parsed.protocol);
  } catch {
    return false;
  }
}

return isSafeUrl(url) ? <a href={url}>Link</a> : <span>Invalid link</span>;
```

### Sensitive Data in React

```jsx
// ❌ NEVER store sensitive data in:
localStorage.setItem('token', accessToken);    // persists, accessible by any JS
sessionStorage.setItem('token', accessToken);  // accessible by any JS (XSS risk)
window.myToken = accessToken;                  // accessible by any JS

// ✅ Store access token in memory (React state or context)
// It is cleared on page refresh — use refresh token in httpOnly cookie to re-issue

// Auth context pattern
const AuthContext = createContext(null);

export function AuthProvider({ children }) {
  const [accessToken, setAccessToken] = useState(null); // in-memory only

  const login = async (email, password) => {
    const res = await api.post('/auth/login', { email, password });
    setAccessToken(res.data.accessToken);  // store in memory
    // refresh token stored in httpOnly cookie by server
  };

  const logout = async () => {
    await api.post('/auth/logout');  // server clears httpOnly cookie
    setAccessToken(null);
  };

  // Refresh access token on mount (from httpOnly cookie silently)
  useEffect(() => {
    api.post('/auth/refresh')
      .then(res => setAccessToken(res.data.accessToken))
      .catch(() => setAccessToken(null));
  }, []);

  return (
    <AuthContext.Provider value={{ accessToken, login, logout }}>
      {children}
    </AuthContext.Provider>
  );
}

// ❌ NEVER log sensitive data to console in production
console.log('User:', { email, password, ssn });  // visible in browser devtools

// ✅ Remove console logs in production (use babel-plugin-transform-remove-console)
// Or check environment:
if (process.env.NODE_ENV !== 'production') {
  console.log('Debug info:', data);
}
```

### CSRF Protection in React

```javascript
// CSRF (Cross-Site Request Forgery) — attacker's site makes requests to your API
// using victim's cookies without victim's knowledge

// ✅ SameSite cookie attribute (primary defense)
// Set on server: cookie: { sameSite: 'strict' }
// Browsers won't send cookie for cross-origin requests

// ✅ CSRF token (for SameSite=Lax or older browsers)
// Server sends CSRF token (in cookie or response body)
// Client must include it in every state-changing request header

// React: include CSRF token in Axios default headers
import axios from 'axios';

// Read CSRF token from cookie (set by server, readable by JS — not httpOnly)
function getCsrfToken() {
  return document.cookie
    .split('; ')
    .find(row => row.startsWith('csrf-token='))
    ?.split('=')[1];
}

const api = axios.create({ baseURL: '/api' });

api.interceptors.request.use(config => {
  if (['post', 'put', 'patch', 'delete'].includes(config.method)) {
    config.headers['X-CSRF-Token'] = getCsrfToken();
  }
  return config;
});

// ✅ Also: verify Origin and Referer headers on server
app.use((req, res, next) => {
  const origin = req.headers.origin || req.headers.referer;
  if (['POST', 'PUT', 'PATCH', 'DELETE'].includes(req.method)) {
    if (!origin || !origin.startsWith(process.env.ALLOWED_ORIGIN)) {
      return res.status(403).json({ error: 'CSRF detected' });
    }
  }
  next();
});
```

### Dependency Security in React

```bash
# Audit React app dependencies
npm audit

# Check bundle for known vulnerable packages
npx snyk test

# Use Content Security Policy to reduce XSS impact (see Section 10)
```

---

## 10. SECURITY HEADERS & CORS

---

### Security Headers

```javascript
const helmet = require('helmet');

// helmet() sets all these by default — configure as needed:
app.use(helmet({
  // Content Security Policy — controls what resources can load
  contentSecurityPolicy: {
    directives: {
      defaultSrc:  ["'self'"],
      scriptSrc:   ["'self'", "'nonce-{random}'"],  // only from origin
      styleSrc:    ["'self'", 'https://fonts.googleapis.com'],
      fontSrc:     ["'self'", 'https://fonts.gstatic.com'],
      imgSrc:      ["'self'", 'data:', 'https://cdn.myapp.com'],
      connectSrc:  ["'self'", 'https://api.myapp.com'],
      frameSrc:    ["'none'"],         // block iframes
      objectSrc:   ["'none'"],         // block Flash, etc.
      upgradeInsecureRequests: [],     // upgrade HTTP to HTTPS
    },
  },

  // HSTS — browser always uses HTTPS for this domain
  strictTransportSecurity: {
    maxAge: 63072000,      // 2 years (in seconds)
    includeSubDomains: true,
    preload: true,
  },

  // Prevents MIME type sniffing
  xContentTypeOptions: true,   // X-Content-Type-Options: nosniff

  // Controls referrer information
  referrerPolicy: { policy: 'strict-origin-when-cross-origin' },

  // Prevents clickjacking
  frameguard: { action: 'deny' },  // X-Frame-Options: DENY

  // Disables browser XSS filter (modern apps use CSP instead)
  xXssProtection: false,
}));

// Manual header setting
app.use((req, res, next) => {
  res.setHeader('Permissions-Policy',
    'geolocation=(), microphone=(), camera=(), payment=()');
  res.setHeader('Cross-Origin-Opener-Policy', 'same-origin');
  res.setHeader('Cross-Origin-Embedder-Policy', 'require-corp');
  next();
});
```

### CORS Configuration

```javascript
const cors = require('cors');

// ❌ Too permissive — allows ANY origin
app.use(cors()); // origin: '*'

// ✅ Strict CORS configuration
const allowedOrigins = [
  'https://myapp.com',
  'https://www.myapp.com',
  process.env.NODE_ENV === 'development' && 'http://localhost:3000',
].filter(Boolean);

const corsOptions = {
  origin: (origin, callback) => {
    // Allow requests with no origin (e.g., mobile apps, curl)
    if (!origin || allowedOrigins.includes(origin)) {
      callback(null, true);
    } else {
      callback(new Error(`CORS policy: origin ${origin} not allowed`));
    }
  },
  methods: ['GET', 'POST', 'PUT', 'PATCH', 'DELETE', 'OPTIONS'],
  allowedHeaders: ['Content-Type', 'Authorization', 'X-CSRF-Token'],
  exposedHeaders: ['X-Total-Count'],
  credentials: true,     // allow cookies with cross-origin requests
  maxAge: 86400,         // preflight cache: 24 hours
};

app.use(cors(corsOptions));

// Handle preflight (OPTIONS) for all routes
app.options('*', cors(corsOptions));
```

---

## 11. SECRETS & ENVIRONMENT MANAGEMENT

---

```javascript
// ✅ Use .env for local development
// npm install dotenv
require('dotenv').config();

// .env file (NEVER commit to git)
// JWT_ACCESS_SECRET=your-super-secret-access-token-key
// JWT_REFRESH_SECRET=your-super-secret-refresh-token-key
// DB_PASSWORD=your-db-password
// ENCRYPTION_KEY=32-char-hex-string

// .gitignore — always include
// .env
// .env.local
// .env.production

// ✅ Validate required environment variables on startup
const requiredEnvVars = [
  'JWT_ACCESS_SECRET',
  'JWT_REFRESH_SECRET',
  'DB_CONNECTION_STRING',
  'ENCRYPTION_KEY',
];

for (const varName of requiredEnvVars) {
  if (!process.env[varName]) {
    console.error(`FATAL: Missing required environment variable: ${varName}`);
    process.exit(1);
  }
}

// ✅ Generate strong secrets
const crypto = require('crypto');
// Run once: node -e "console.log(require('crypto').randomBytes(64).toString('hex'))"

// ✅ Rotate secrets regularly
// Use versioned secrets: JWT_SECRET_V1, JWT_SECRET_V2
// Accept both during rotation window, then drop V1

// ✅ Use secret managers in production
// AWS Secrets Manager
// HashiCorp Vault
// Azure Key Vault
// GCP Secret Manager

// Example with AWS Secrets Manager
const { SecretsManagerClient, GetSecretValueCommand } = require('@aws-sdk/client-secrets-manager');

const client = new SecretsManagerClient({ region: 'ap-south-1' });

async function getSecret(secretName) {
  const command = new GetSecretValueCommand({ SecretId: secretName });
  const data = await client.send(command);
  return JSON.parse(data.SecretString);
}

const secrets = await getSecret('myapp/production');
const JWT_SECRET = secrets.jwtSecret;
```

---

## 12. SECURITY CHECKLIST — PRODUCTION READY

---

### Transport Security
```
✅ HTTPS enforced on all endpoints
✅ HTTP redirects to HTTPS (301)
✅ TLS 1.2 minimum (TLS 1.3 preferred)
✅ Weak cipher suites disabled
✅ HSTS enabled (maxAge >= 1 year, includeSubDomains, preload)
✅ Certificates from trusted CA (Let's Encrypt is free)
✅ Certificate auto-renewal configured
```

### Authentication & Session
```
✅ Passwords hashed with bcrypt (cost ≥ 12) or Argon2id
✅ JWT access tokens short-lived (15 min)
✅ Refresh tokens stored in httpOnly, Secure, SameSite=Strict cookie
✅ JWT secrets are strong (256-bit+) and rotated periodically
✅ JWT algorithm explicitly specified and validated (no 'none')
✅ Rate limiting on auth endpoints (max 5/15min per IP)
✅ Account lockout after repeated failures
✅ MFA/2FA available for sensitive accounts
✅ Secure password reset (time-limited, single-use, hashed tokens)
✅ Logout properly invalidates tokens
```

### Input & Output
```
✅ All user input validated and sanitized (express-validator)
✅ All database queries use parameterized statements or ORM
✅ MongoDB inputs sanitized (express-mongo-sanitize)
✅ File uploads validated (type, size, content scanning)
✅ Dangerous HTML sanitized with DOMPurify (if needed)
✅ dangerouslySetInnerHTML avoided or sanitized in React
✅ Output encoding appropriate to context (HTML, JS, SQL, URL)
```

### Headers & CORS
```
✅ Helmet.js applied
✅ Content-Security-Policy configured
✅ CORS whitelist defined (no wildcard *)
✅ X-Frame-Options: DENY
✅ X-Content-Type-Options: nosniff
✅ Referrer-Policy configured
✅ Permissions-Policy configured
```

### Access Control
```
✅ Authentication required on all non-public endpoints
✅ Authorization checks on every request (not just UI level)
✅ Resource ownership verified (user can only access their data)
✅ Principle of least privilege for DB user
✅ Admin features protected by role check
✅ Sensitive operations require fresh auth (re-login for payments)
```

### Infrastructure & Dependencies
```
✅ Dependencies audited (npm audit)
✅ Outdated packages updated
✅ Unused packages removed
✅ Secrets in environment variables (not in code/git)
✅ .env in .gitignore
✅ NODE_ENV=production in production
✅ Error messages generic in production (no stack traces)
✅ Debug endpoints removed in production
✅ Request body size limited
✅ Rate limiting on all public endpoints
```

### Logging & Monitoring
```
✅ Security events logged (logins, failures, access denied)
✅ Sensitive data NOT logged (passwords, tokens, PII)
✅ Logs stored securely and centrally (ELK, CloudWatch)
✅ Alerts configured for suspicious patterns
✅ Audit trail for sensitive operations
✅ Incident response plan documented
```

---

*Stay secure. Threat model everything. Defense in depth.*
