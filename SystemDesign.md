# System Design Interview Topics: Basic to Advanced

---

## TABLE OF CONTENTS

1. [What is System Design?](#1-what-is-system-design)
2. [Client-Server Architecture](#2-client-server-architecture)
3. [Networking Fundamentals](#3-networking-fundamentals)
4. [APIs — REST, GraphQL, gRPC](#4-apis--rest-graphql-grpc)
5. [Databases — SQL vs NoSQL](#5-databases--sql-vs-nosql)
6. [Caching](#6-caching)
7. [Content Delivery Networks (CDN)](#7-content-delivery-networks-cdn)
8. [Load Balancing](#8-load-balancing)
9. [Horizontal vs Vertical Scaling](#9-horizontal-vs-vertical-scaling)
10. [Database Scaling — Sharding, Replication, Partitioning](#10-database-scaling--sharding-replication-partitioning)
11. [CAP Theorem](#11-cap-theorem)
12. [Consistency Patterns](#12-consistency-patterns)
13. [Availability & Reliability](#13-availability--reliability)
14. [Message Queues & Event-Driven Architecture](#14-message-queues--event-driven-architecture)
15. [Microservices Architecture](#15-microservices-architecture)
16. [API Gateway & Service Mesh](#16-api-gateway--service-mesh)
17. [Rate Limiting & Throttling](#17-rate-limiting--throttling)
18. [Authentication & Authorization at Scale](#18-authentication--authorization-at-scale)
19. [Storage Systems](#19-storage-systems)
20. [Search Systems](#20-search-systems)
21. [Real-Time Systems](#21-real-time-systems)
22. [Distributed Systems Concepts](#22-distributed-systems-concepts)
23. [Data Pipelines & Stream Processing](#23-data-pipelines--stream-processing)
24. [Monitoring, Logging & Alerting](#24-monitoring-logging--alerting)
25. [Security in System Design](#25-security-in-system-design)
26. [System Design — Step-by-Step Framework](#26-system-design--step-by-step-framework)
27. [Classic System Design Problems](#27-classic-system-design-problems)

---

## 1. What is System Design?

### Definition
System Design is the process of defining the **architecture, components, modules, interfaces, and data flow** of a system to satisfy specified requirements. In interviews, it evaluates your ability to build large-scale, reliable, and efficient systems.

### Two Types of System Design Interviews

**High-Level Design (HLD):**
- Overall architecture
- Components and their interaction
- Technology choices (database, cache, queue)
- Scalability, availability, reliability

**Low-Level Design (LLD):**
- Class diagrams and object modeling
- Design patterns
- API contracts
- Database schema design

### What Interviewers Look For
- Ability to clarify requirements before jumping to solutions
- Structured and logical thinking
- Trade-off awareness (e.g., consistency vs availability)
- Scalability considerations
- Practical knowledge of real-world systems

### Interview Q&A
**Q: How do you approach a system design interview?**
> Clarify requirements → Estimate scale → Define API → Design data model → High-level architecture → Deep dive into components → Identify bottlenecks → Discuss trade-offs.

---

## 2. Client-Server Architecture

### Definition
A model where **clients** (browsers, mobile apps) request services from **servers** (backend systems). The server processes requests and returns responses.

### Types of Architecture

**Monolithic Architecture:**
```
Client → [Single Server: UI + Business Logic + Database Access]
```
- Everything in one codebase and deployment unit.
- Simple to develop initially.
- Hard to scale individual components.
- A bug can bring down the entire system.

**Layered (N-tier) Architecture:**
```
Client → Presentation Layer → Business Logic Layer → Data Layer → Database
```
- Clear separation of concerns.
- Each layer communicates only with adjacent layers.

**Microservices Architecture:**
```
Client → API Gateway → [User Service] [Order Service] [Payment Service]
                              ↓               ↓               ↓
                           User DB        Order DB        Payment DB
```

### Communication Models

**Synchronous (Request-Response):**
- Client sends request, waits for response.
- Simple, predictable.
- Creates tight coupling.
- e.g., REST API, gRPC

**Asynchronous (Event-Driven):**
- Client sends request, continues execution; server processes and delivers result later.
- Decoupled, resilient.
- Harder to debug.
- e.g., Message Queues, WebSockets, Webhooks

### Interview Q&A
**Q: What are the trade-offs between monolithic and microservices?**
> Monolithic is simpler to develop, test, and deploy initially but becomes a bottleneck at scale. Microservices enable independent scaling and deployment but add operational complexity (service discovery, network calls, distributed tracing).

---

## 3. Networking Fundamentals

### OSI Model (7 Layers)

| Layer | Name | Example |
|-------|------|---------|
| 7 | Application | HTTP, DNS, SMTP |
| 6 | Presentation | SSL/TLS, JPEG |
| 5 | Session | NetBIOS |
| 4 | Transport | TCP, UDP |
| 3 | Network | IP, ICMP |
| 2 | Data Link | Ethernet, MAC |
| 1 | Physical | Cables, Fiber |

### TCP vs UDP

| Feature | TCP | UDP |
|---------|-----|-----|
| Connection | Connection-oriented | Connectionless |
| Reliability | Guaranteed delivery | No guarantee |
| Order | In-order delivery | No ordering |
| Speed | Slower | Faster |
| Use Case | HTTP, FTP, Email | Video streaming, DNS, Gaming |

### HTTP/HTTPS
- **HTTP/1.1**: One request per TCP connection (with keep-alive for reuse).
- **HTTP/2**: Multiplexing — multiple requests over single connection; header compression.
- **HTTP/3**: Built on QUIC (UDP-based), lower latency, better for mobile.

### DNS (Domain Name System)
Translates domain names (`google.com`) to IP addresses (`142.250.80.46`).

**DNS Resolution Process:**
```
Browser → Local Cache → OS Cache → Resolver (ISP) → Root Server → TLD Server → Authoritative Server → IP Address
```

**DNS Record Types:**
- `A` — Domain to IPv4
- `AAAA` — Domain to IPv6
- `CNAME` — Alias (subdomain to domain)
- `MX` — Mail exchange
- `TXT` — Text records (SPF, DKIM)

### IP Addresses & Ports
- IPv4: `192.168.1.1` (32-bit, ~4 billion addresses)
- IPv6: `2001:0db8::1` (128-bit, virtually unlimited)
- Well-known ports: HTTP(80), HTTPS(443), MySQL(3306), Redis(6379), MongoDB(27017)

### Latency Numbers Every Engineer Should Know

| Operation | Approximate Latency |
|-----------|-------------------|
| L1 cache reference | 0.5 ns |
| RAM reference | 100 ns |
| SSD read | 150 µs |
| HDD seek | 10 ms |
| Same datacenter round trip | 0.5 ms |
| Cross-continent round trip | 150 ms |

### Interview Q&A
**Q: What happens when you type `www.google.com` in a browser?**
> DNS resolution → TCP connection (3-way handshake) → TLS handshake (HTTPS) → HTTP request sent → Server processes → HTTP response received → Browser renders HTML → Loads CSS/JS/Images.

---

## 4. APIs — REST, GraphQL, gRPC

### REST (Representational State Transfer)

**Principles:**
- Stateless — each request contains all needed information
- Resource-based URLs (nouns): `/users/123/orders`
- Standard HTTP methods (GET, POST, PUT, PATCH, DELETE)
- Cacheable responses

**HTTP Status Codes:**
- `200 OK`, `201 Created`, `204 No Content`
- `400 Bad Request`, `401 Unauthorized`, `403 Forbidden`, `404 Not Found`, `409 Conflict`
- `500 Internal Server Error`, `502 Bad Gateway`, `503 Service Unavailable`

**REST API Design Best Practices:**
```
GET    /users          → List users
GET    /users/123      → Get user 123
POST   /users          → Create user
PUT    /users/123      → Replace user 123
PATCH  /users/123      → Partially update user 123
DELETE /users/123      → Delete user 123

GET    /users/123/orders       → User's orders
POST   /users/123/orders       → Create order for user

# Versioning
GET /api/v1/users
GET /api/v2/users

# Pagination
GET /users?page=2&limit=20
GET /users?cursor=abc123&limit=20

# Filtering & Sorting
GET /users?role=admin&sort=createdAt:desc
```

### GraphQL

A query language for APIs where clients request exactly the data they need.

```graphql
# Client specifies exact fields needed
query {
  user(id: "123") {
    name
    email
    orders {
      id
      total
      status
    }
  }
}

# Mutation (write)
mutation {
  createUser(name: "Alice", email: "alice@example.com") {
    id
    name
  }
}

# Subscription (real-time)
subscription {
  orderStatusChanged(userId: "123") {
    orderId
    status
  }
}
```

**GraphQL vs REST:**

| Feature | REST | GraphQL |
|---------|------|---------|
| Data fetching | Fixed endpoints, fixed response | Client-specified fields |
| Over-fetching | Common | Eliminated |
| Under-fetching | Multiple requests needed | Single request |
| Versioning | URL versioning | Schema evolution |
| Caching | Easy (HTTP cache) | Complex (need custom) |
| Learning curve | Low | Medium |
| Best for | Simple CRUD APIs | Complex, data-rich apps |

### gRPC (Google Remote Procedure Call)

Uses **Protocol Buffers** (binary format) instead of JSON. Faster, strongly typed, supports streaming.

```protobuf
// user.proto
syntax = "proto3";

service UserService {
  rpc GetUser (GetUserRequest) returns (User);
  rpc ListUsers (ListUsersRequest) returns (stream User);
}

message GetUserRequest {
  string id = 1;
}

message User {
  string id = 1;
  string name = 2;
  string email = 3;
}
```

**gRPC vs REST:**

| Feature | REST | gRPC |
|---------|------|------|
| Protocol | HTTP/1.1 | HTTP/2 |
| Format | JSON (text) | Protocol Buffers (binary) |
| Speed | Slower | ~5-10x faster |
| Streaming | Limited | Bi-directional streaming |
| Browser support | Full | Limited (needs proxy) |
| Best for | Public APIs | Internal microservices |

### WebSockets vs Webhooks vs Long Polling

**Long Polling:** Client polls server repeatedly. Simple but inefficient.

**WebSockets:** Persistent bi-directional connection. Great for real-time (chat, gaming).

**Webhooks:** Server pushes events to a registered client URL. Event-driven (GitHub push notifications, payment confirmations).

---

## 5. Databases — SQL vs NoSQL

### Relational Databases (SQL)

Data stored in structured tables with predefined schemas. Relationships enforced via foreign keys.

**Examples:** PostgreSQL, MySQL, SQLite, Oracle, SQL Server

**ACID Properties:**
- **Atomicity** — All operations succeed or none do (transactions)
- **Consistency** — Data remains valid after transaction
- **Isolation** — Concurrent transactions don't interfere
- **Durability** — Committed data persists even after crash

```sql
-- Table with relationships
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  name VARCHAR(100) NOT NULL,
  email VARCHAR(255) UNIQUE NOT NULL,
  created_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE orders (
  id SERIAL PRIMARY KEY,
  user_id INT REFERENCES users(id) ON DELETE CASCADE,
  total DECIMAL(10, 2),
  status VARCHAR(50)
);

-- JOIN query
SELECT u.name, o.total, o.status
FROM users u
INNER JOIN orders o ON u.id = o.user_id
WHERE u.id = 1;
```

### NoSQL Databases

**1. Document Databases (MongoDB, CouchDB)**
- Stores JSON-like documents
- Flexible schema
- Good for: Product catalogs, user profiles, content management

```json
{
  "_id": "user123",
  "name": "Alice",
  "address": {
    "city": "Mumbai",
    "zip": "400001"
  },
  "tags": ["premium", "verified"]
}
```

**2. Key-Value Stores (Redis, DynamoDB)**
- Simplest NoSQL model
- Extremely fast lookups
- Good for: Caching, sessions, leaderboards, rate limiting

```
SET user:123 '{"name": "Alice"}' EX 3600
GET user:123
INCR page:views:home
```

**3. Wide-Column Stores (Cassandra, HBase)**
- Data stored in rows with dynamic columns grouped into column families
- Excellent write performance, scales horizontally
- Good for: Time-series data, IoT, logs, analytics

```cql
CREATE TABLE sensor_data (
  device_id UUID,
  timestamp TIMESTAMP,
  temperature FLOAT,
  PRIMARY KEY (device_id, timestamp)
) WITH CLUSTERING ORDER BY (timestamp DESC);
```

**4. Graph Databases (Neo4j, Amazon Neptune)**
- Nodes (entities) and edges (relationships)
- Good for: Social networks, recommendation engines, fraud detection

```cypher
MATCH (user:User {name: "Alice"})-[:FRIEND]->(friend)
RETURN friend.name
```

**5. Time-Series Databases (InfluxDB, TimescaleDB)**
- Optimized for time-stamped data
- Good for: Metrics, monitoring, financial data

### SQL vs NoSQL Decision Guide

| Factor | Choose SQL | Choose NoSQL |
|--------|-----------|-------------|
| Data structure | Structured, relational | Flexible, hierarchical |
| Schema | Fixed schema | Dynamic schema |
| Consistency | Strong ACID required | Eventual consistency OK |
| Scale | Vertical (with read replicas) | Horizontal (built-in sharding) |
| Queries | Complex joins and aggregations | Simple lookups by key |
| Examples | Banking, ERP, Inventory | Social feeds, IoT, Real-time analytics |

### Database Indexing

An index is a data structure (typically B-Tree) that speeds up data retrieval.

```sql
-- Without index: Full table scan O(n)
SELECT * FROM users WHERE email = 'alice@example.com';

-- With index: O(log n)
CREATE INDEX idx_users_email ON users(email);

-- Composite index
CREATE INDEX idx_orders_user_status ON orders(user_id, status);

-- Check query plan
EXPLAIN ANALYZE SELECT * FROM users WHERE email = 'alice@example.com';
```

**Index Trade-offs:**
- Speeds up reads, but slows writes (index must be updated on insert/update/delete)
- Consumes additional disk space
- Don't over-index; index columns used in WHERE, JOIN, ORDER BY, GROUP BY

---

## 6. Caching

### What is Caching?
Storing frequently accessed data in fast memory to reduce latency and database load.

### Cache Levels

```
Request → Browser Cache → CDN Cache → Load Balancer Cache → Application Cache (Redis) → Database
```

**L1 — Browser Cache:** Stores static assets (CSS, JS, images) locally.
**L2 — CDN Cache:** Geographically distributed cache for static/semi-static content.
**L3 — Application Cache (Redis/Memcached):** In-memory cache for dynamic data.
**L4 — Database Cache:** Query result cache, buffer pool.

### Caching Strategies

**1. Cache-Aside (Lazy Loading) — Most Common**
```
Read:
  1. Check cache
  2. If miss → query DB → store in cache → return
  3. If hit → return from cache

Write:
  1. Write to DB
  2. Invalidate (delete) the cache entry
```

```python
def get_user(user_id):
    cached = redis.get(f"user:{user_id}")
    if cached:
        return json.loads(cached)             # Cache hit
    user = db.query("SELECT * FROM users WHERE id = ?", user_id)
    redis.setex(f"user:{user_id}", 3600, json.dumps(user))  # Store with TTL
    return user
```

**2. Write-Through**
- Write to cache AND database simultaneously.
- Cache always consistent with DB.
- Slower writes, but reads always fast.

**3. Write-Behind (Write-Back)**
- Write to cache immediately, sync to DB asynchronously.
- Very fast writes, risk of data loss if cache crashes.

**4. Read-Through**
- Cache sits in front of DB; cache itself fetches from DB on miss.

### Cache Invalidation Strategies
- **TTL (Time-To-Live):** Expire after a set duration.
- **Event-based:** Invalidate on data change.
- **LRU (Least Recently Used):** Evict the least recently accessed item when cache is full.
- **LFU (Least Frequently Used):** Evict the least frequently accessed item.

### Cache Problems

**Cache Stampede (Thundering Herd):**
Many requests hit the DB simultaneously when a popular cache entry expires.

Solution: Probabilistic early expiration or mutex locks:
```python
def get_user(user_id):
    lock_key = f"lock:user:{user_id}"
    if redis.set(lock_key, 1, nx=True, ex=5):  # Acquire lock
        user = db.query(...)
        redis.setex(f"user:{user_id}", 3600, json.dumps(user))
        redis.delete(lock_key)
```

**Cache Penetration:**
Requests for keys that don't exist in cache or DB (e.g., attackers querying random IDs).

Solution: Cache null results, or use a Bloom Filter to check if a key could exist.

**Cache Avalanche:**
Many cache entries expire at the same time, overloading the DB.

Solution: Add random jitter to TTL:
```python
ttl = 3600 + random.randint(0, 300)  # 1 hour + up to 5 minutes
redis.setex(key, ttl, value)
```

### Redis Data Structures

```
String  → Simple key-value, counters
Hash    → User profile fields
List    → Recent activity, queues
Set     → Unique tags, followers
Sorted Set → Leaderboards, rate limiting
Bitmap  → Online presence, feature flags
HyperLogLog → Approximate unique counts
Stream  → Event log, message queue
```

### Interview Q&A
**Q: What is a cache eviction policy and which is most common?**
> Eviction policies determine what to remove when the cache is full. LRU (Least Recently Used) is the most common — it evicts items that haven't been accessed for the longest time, under the assumption that recently used items are more likely to be needed again.

---

## 7. Content Delivery Networks (CDN)

### What is a CDN?
A geographically distributed network of servers (PoPs — Points of Presence) that cache and deliver content from the nearest location to the user.

```
User (Mumbai) → CDN Edge Server (Mumbai) → Fast response
vs.
User (Mumbai) → Origin Server (US East)  → High latency
```

### How CDN Works
1. User requests `https://cdn.example.com/image.jpg`
2. DNS routes to nearest CDN edge server
3. If cached → serve directly (cache hit)
4. If not cached → fetch from origin, cache locally, serve to user (cache miss)

### What CDNs Cache
- Static assets: images, CSS, JavaScript, fonts, videos
- Downloadable files
- API responses (with appropriate cache headers)
- Dynamic content (with edge computing, e.g., Cloudflare Workers)

### CDN Cache Control Headers

```http
Cache-Control: public, max-age=86400        # Cache for 1 day
Cache-Control: private, no-cache           # Don't cache (user-specific)
Cache-Control: no-store                    # Never cache (sensitive data)
ETag: "abc123"                            # Fingerprint for validation
Last-Modified: Wed, 01 Jan 2025 00:00:00 GMT
```

### Popular CDNs
- **Cloudflare** — Security + CDN + edge computing
- **AWS CloudFront** — Deep AWS integration
- **Akamai** — Enterprise, largest CDN
- **Fastly** — Programmable CDN
- **Google Cloud CDN**

### Interview Q&A
**Q: How do you invalidate CDN cache after deploying a new version of a file?**
> Use cache-busting: include a version hash in the filename or URL (`app.v2.js` or `app.js?v=abc123`). On deployment, the new URL is treated as a new resource by the CDN.

---

## 8. Load Balancing

### What is Load Balancing?
Distributing incoming traffic across multiple servers to ensure no single server becomes a bottleneck.

```
               ┌─→ Server 1
Client → LB ───┼─→ Server 2
               └─→ Server 3
```

### Load Balancing Algorithms

**Round Robin:**
Requests distributed sequentially (1→2→3→1→2→3).
Simple, works when all servers have equal capacity.

**Weighted Round Robin:**
Servers assigned weights based on capacity.
Server A (weight 3) gets 3x more traffic than Server B (weight 1).

**Least Connections:**
Route to the server with fewest active connections.
Best for long-lived connections.

**IP Hash (Sticky Sessions):**
Hash client IP to always route to the same server.
Needed when server-side session state exists.

**Least Response Time:**
Route to server with lowest latency + fewest connections.

**Random:**
Randomly select a server. Simple, roughly equal distribution.

### Layer 4 vs Layer 7 Load Balancing

**Layer 4 (Transport Layer):**
- Routes based on IP and TCP/UDP port
- Fast, minimal processing
- Cannot inspect HTTP content
- Example: AWS NLB, HAProxy in TCP mode

**Layer 7 (Application Layer):**
- Routes based on HTTP headers, URL, cookies, content type
- Can perform SSL termination, content-based routing
- Slightly more overhead
- Example: AWS ALB, Nginx, HAProxy in HTTP mode

```nginx
# Nginx Layer 7 Load Balancer config
upstream backend {
    least_conn;
    server backend1.example.com:8080 weight=3;
    server backend2.example.com:8080 weight=1;
    server backend3.example.com:8080 backup;
}

server {
    listen 80;

    # Route API traffic to API servers
    location /api/ {
        proxy_pass http://backend;
    }

    # Route static files to CDN
    location /static/ {
        proxy_pass https://cdn.example.com;
    }
}
```

### Health Checks
Load balancers periodically check server health and remove unhealthy servers from rotation.

```
GET /health → 200 OK          # Server is healthy
GET /health → 503              # Server is unhealthy → removed from pool
```

### Interview Q&A
**Q: What is session persistence (sticky sessions) and when is it needed?**
> Sticky sessions ensure a user always hits the same server. Needed when session data is stored locally on the server. Better practice: store sessions in a shared store (Redis) and use stateless servers.

---

## 9. Horizontal vs Vertical Scaling

### Vertical Scaling (Scale Up)
Add more resources to an existing server (more CPU, RAM, SSD).

```
Before: 1 server (4 CPU, 16GB RAM)
After:  1 server (32 CPU, 128GB RAM)
```

**Pros:** Simple, no application changes needed, no distribution complexity.
**Cons:** Has a hardware limit, single point of failure, expensive, requires downtime.

### Horizontal Scaling (Scale Out)
Add more servers to distribute load.

```
Before: 1 server
After:  10 servers behind a load balancer
```

**Pros:** Theoretically unlimited, high availability, cost-effective (commodity hardware).
**Cons:** Application must be stateless, adds operational complexity, data consistency challenges.

### Stateless vs Stateful Design

**Stateful (hard to scale horizontally):**
Server stores session data locally. Sticky sessions required.

**Stateless (easy to scale horizontally):**
No local state. Sessions stored in Redis. Any server can handle any request.

```
Stateless design:
Client → [Server 1, 2, 3 (any)] → Redis (shared state) → DB
```

### Auto-Scaling
Automatically add/remove servers based on load.

```
CloudWatch Metric: CPU > 70% for 5 minutes → Add 2 servers
CloudWatch Metric: CPU < 30% for 10 minutes → Remove 1 server
Min instances: 2 (always keep 2 for HA)
Max instances: 20 (cost control)
```

### Interview Q&A
**Q: When would you choose vertical over horizontal scaling?**
> Vertical scaling is appropriate for stateful applications that are hard to distribute (e.g., certain databases), or as a quick short-term fix. Long-term, horizontal scaling is preferred for web services.

---

## 10. Database Scaling — Sharding, Replication, Partitioning

### Database Replication

**Master-Slave (Primary-Replica):**
```
        ┌─→ Replica 1 (Read)
Master ─┼─→ Replica 2 (Read)
(Write) └─→ Replica 3 (Read)
```
- All writes go to Master
- Reads distributed across Replicas
- Async replication (slight lag)
- If Master fails, promote a Replica

**Use case:** Read-heavy workloads (social feeds, dashboards)

**Multi-Master:**
- Multiple writable nodes
- Conflict resolution required
- Complex but high write availability

### Database Partitioning

Dividing a large table into smaller, more manageable pieces.

**Horizontal Partitioning (Sharding):**
Rows distributed across multiple tables/databases.
```
Shard 1: users where id 1–1,000,000
Shard 2: users where id 1,000,001–2,000,000
```

**Vertical Partitioning:**
Columns split across multiple tables.
```
Table 1: users (id, name, email)         — frequently accessed
Table 2: user_details (id, bio, address) — rarely accessed
```

### Sharding

Distributing data across multiple database nodes (shards), each holding a subset of the data.

**Sharding Strategies:**

**1. Range-Based Sharding:**
```
Shard 1: user_id 1 – 1,000,000
Shard 2: user_id 1,000,001 – 2,000,000
Shard 3: user_id 2,000,001 – 3,000,000
```
Pro: Easy range queries.
Con: Hotspot risk (new users all go to one shard).

**2. Hash-Based Sharding:**
```
shard_id = hash(user_id) % num_shards
```
Pro: Even distribution.
Con: Range queries hit all shards; resharding is expensive.

**3. Directory-Based Sharding:**
A lookup service maps each key to its shard.
Pro: Flexible.
Con: Lookup service is a single point of failure.

**4. Geo-Based Sharding:**
Data routed by user geography.
```
Users in India → Shard India
Users in US   → Shard US
```

**Sharding Challenges:**
- Cross-shard queries (JOINs across shards are expensive)
- Resharding (rebalancing when adding/removing shards)
- Hotspots (one shard overloaded)
- Transactions across shards (distributed transactions)

### Consistent Hashing
Used in distributed systems to minimize data movement when nodes are added or removed.

```
Virtual Ring: 0 ─────────────────── 360
              Server A: 90
              Server B: 180
              Server C: 270

Key K: hash(K) = 120 → goes to Server B (next clockwise)

Adding Server D at 150: Only keys between 120-150 move from B to D
(vs. hash % n which would require moving most keys)
```

**Used in:** Cassandra, DynamoDB, Redis Cluster, CDN routing.

### Interview Q&A
**Q: What are the challenges of database sharding?**
> Cross-shard joins, distributed transactions, uneven data distribution (hotspots), and the complexity of resharding when adding new nodes are the main challenges.

---

## 11. CAP Theorem

### Definition
In a distributed system, you can only guarantee **two out of three** properties:

- **C — Consistency:** Every read receives the most recent write or an error.
- **A — Availability:** Every request receives a response (not necessarily the latest data).
- **P — Partition Tolerance:** System continues operating despite network partitions.

### The Reality
**Network partitions are inevitable** in distributed systems. So the real choice is between **CP** and **AP**.

```
CP Systems (Consistent + Partition Tolerant):
  → When partition occurs, return error rather than stale data
  → Examples: HBase, MongoDB (strong consistency mode), Zookeeper, etcd
  → Use for: Banking, financial transactions, inventory (correctness critical)

AP Systems (Available + Partition Tolerant):
  → When partition occurs, return stale data rather than error
  → Eventually becomes consistent when partition heals
  → Examples: Cassandra, DynamoDB, CouchDB, DNS
  → Use for: Social feeds, product catalogs, DNS (availability critical)
```

### PACELC Theorem (Extension of CAP)
Even when there's no partition (normal operation), there's a trade-off between **Latency** and **Consistency**:

```
If Partition: choose between Availability vs Consistency
Else (normal): choose between Latency vs Consistency
```

### Interview Q&A
**Q: If you're designing a banking system, would you choose CP or AP?**
> CP. A bank cannot show stale balances. It's better to return an error (temporarily unavailable) than show incorrect data that could result in financial loss.

**Q: Why is CA not possible in distributed systems?**
> Because network partitions always happen. If you claim CA, you're actually a single-node system, which isn't truly distributed.

---

## 12. Consistency Patterns

### Strong Consistency
All reads return the most recent write. System behaves like a single node.
- Harder to achieve at scale
- Used in: banking, inventory systems

### Eventual Consistency
System will become consistent given enough time, but reads may return stale data.
- Better performance and availability
- Used in: social media, DNS, shopping carts

### Read-Your-Own-Writes Consistency
After you write, you can always read your own write.
- Example: After posting a tweet, you see it immediately (others may not yet)

### Monotonic Read Consistency
You never see older data after seeing newer data (reads don't go backward in time).

### Causal Consistency
Causally related operations are seen in the correct order by all nodes.
- If A causes B, everyone sees A before B.

### Interview Q&A
**Q: How does eventual consistency affect user experience?**
> Users might briefly see outdated data. Design around this by showing optimistic UI updates (assume success locally) and gracefully handling conflicts when they arise.

---

## 13. Availability & Reliability

### Availability Metrics (The "Nines")

| Availability | Downtime per Year | Downtime per Month |
|-------------|-------------------|--------------------|
| 99% (two 9s) | 3.65 days | 7.2 hours |
| 99.9% (three 9s) | 8.76 hours | 43.8 minutes |
| 99.99% (four 9s) | 52.6 minutes | 4.4 minutes |
| 99.999% (five 9s) | 5.26 minutes | 26 seconds |

### Reliability vs Availability
- **Availability:** System is up and reachable.
- **Reliability:** System consistently performs its intended function without failure.
- A system can be available but unreliable (returns wrong data).

### Techniques for High Availability

**Redundancy:**
Eliminate single points of failure (SPOF).
```
Database: Master + 2 Replicas
Load Balancer: Primary + Standby (using VRRP/Keepalived)
Servers: Minimum 2 instances across Availability Zones
```

**Failover:**
Automatically switch to a backup when the primary fails.
- Active-Passive: Backup is on standby, takes over on failure
- Active-Active: All nodes serve traffic; if one fails, others absorb load

**Circuit Breaker Pattern:**
Prevent cascading failures by stopping calls to a failing service.

```
States:
  CLOSED (normal): Requests pass through
  OPEN (tripped):  After N failures, return error immediately (no calls made)
  HALF-OPEN:       After timeout, allow a test request

  [CLOSED] → N failures → [OPEN] → timeout → [HALF-OPEN]
              ↑                                     |
              └────── success ──────────────────────┘
```

**Bulkhead Pattern:**
Isolate failures — limit resources (thread pools, connections) per service so one overload doesn't cascade.

**Retry with Exponential Backoff:**
```
Attempt 1: Wait 1s
Attempt 2: Wait 2s
Attempt 3: Wait 4s
Attempt 4: Wait 8s + jitter (random 0–1s)
```

### SLA, SLO, SLI

- **SLI (Service Level Indicator):** Actual measured metric (e.g., 99.95% availability this month)
- **SLO (Service Level Objective):** Target (e.g., 99.9% availability)
- **SLA (Service Level Agreement):** Contract with penalties for missing SLOs

---

## 14. Message Queues & Event-Driven Architecture

### Why Message Queues?
- **Decoupling:** Producer and consumer are independent.
- **Buffering:** Handle traffic spikes; consumers process at their own pace.
- **Reliability:** Messages persist even if consumer is down.
- **Async processing:** Don't block the request on slow operations.

### Core Concepts

**Producer:** Sends messages to the queue.
**Consumer:** Reads and processes messages.
**Queue/Topic:** Where messages are stored.
**Message Broker:** Manages queues (RabbitMQ, Kafka, SQS).

### Queue vs Pub/Sub

**Queue (Point-to-Point):**
```
Producer → [Queue] → Consumer (only one consumer processes each message)
```
Used for: Task queues, job processing, email sending

**Pub/Sub (Publish-Subscribe):**
```
Publisher → [Topic] → Consumer Group A (all consumers in group get a copy)
                   → Consumer Group B
```
Used for: Event broadcasting, notifications, fan-out

### Apache Kafka

High-throughput, distributed, durable event streaming platform.

**Key Concepts:**
- **Topic:** Named stream of messages (like a table)
- **Partition:** Topics split into partitions for parallelism
- **Offset:** Position of a message in a partition
- **Consumer Group:** Multiple consumers sharing work; each partition consumed by one consumer in a group
- **Retention:** Messages kept for a configurable period (default 7 days)

```
Topic: "orders"
  Partition 0: [msg1, msg3, msg5] ← Consumer A
  Partition 1: [msg2, msg4, msg6] ← Consumer B

Producer → Kafka Cluster → Consumer Group 1 (order-processor)
                        → Consumer Group 2 (order-notifier)
                        → Consumer Group 3 (order-analytics)
```

**Kafka vs RabbitMQ:**

| Feature | Kafka | RabbitMQ |
|---------|-------|----------|
| Model | Log-based pub/sub | Traditional queue |
| Throughput | Very high (millions/s) | High (thousands/s) |
| Message replay | Yes (configurable retention) | No (deleted after ack) |
| Consumer control | Consumer tracks offset | Broker pushes to consumer |
| Use case | Event streaming, analytics | Task queues, RPC |

### Common Patterns

**Event Sourcing:**
Store state as a sequence of events, not current state.
```
Events: [UserCreated, EmailChanged, AddressUpdated]
State:  Rebuild by replaying events
```

**CQRS (Command Query Responsibility Segregation):**
Separate read and write models.
```
Write Model: Optimized for writes → Command DB (normalized)
Read Model:  Optimized for reads  → Query DB (denormalized/materialized views)
```

**Saga Pattern:**
Manage distributed transactions across services using a sequence of local transactions with compensating transactions on failure.

```
Order Saga:
1. Create Order → success
2. Reserve Inventory → success
3. Charge Payment → FAIL
   → Compensate: Release Inventory
   → Compensate: Cancel Order
```

---

## 15. Microservices Architecture

### Core Principles
- Each service owns its data (no shared database)
- Services communicate via well-defined APIs (REST, gRPC, events)
- Independently deployable
- Organized around business capabilities
- Failure in one service doesn't bring down others

### Service Decomposition Strategies

**By Business Domain (Domain-Driven Design):**
```
E-commerce:
  ├── User Service
  ├── Product Service
  ├── Order Service
  ├── Payment Service
  ├── Inventory Service
  ├── Notification Service
  └── Shipping Service
```

**By Team (Conway's Law):**
Services aligned to team ownership. Each team owns and operates their services.

### Service Communication

**Synchronous (request-response):**
```
Order Service ──HTTP/gRPC──→ Payment Service
```
Tight coupling, simpler logic, but failure propagates.

**Asynchronous (event-driven):**
```
Order Service ──event──→ Kafka ──event──→ Inventory Service
                                       → Notification Service
```
Loose coupling, resilient, but eventual consistency and harder debugging.

### Service Discovery

How services find each other dynamically (IPs can change as services scale).

**Client-Side Discovery:**
Client queries a Service Registry (Consul, Eureka) and picks an instance.

**Server-Side Discovery:**
Client queries a Load Balancer/Router; router queries registry.

```
Service Registry (Consul/Eureka):
  payment-service: [10.0.0.1:8080, 10.0.0.2:8080, 10.0.0.3:8080]
  inventory-service: [10.0.1.1:8080]
```

### Distributed Tracing

Track a request as it flows through multiple services.

```
Request ID: abc-123
  → API Gateway     (10ms)
  → Order Service   (50ms)
    → Payment Svc   (200ms) ← slow!
    → Inventory Svc (30ms)
  Total: 290ms
```

Tools: Jaeger, Zipkin, AWS X-Ray, Datadog APM

### Interview Q&A
**Q: What are the disadvantages of microservices?**
> Network overhead between services, distributed tracing complexity, harder local development, data consistency challenges across service boundaries, and operational overhead (many services to deploy, monitor, and manage).

---

## 16. API Gateway & Service Mesh

### API Gateway
A single entry point for all client requests. Acts as a reverse proxy.

**Responsibilities:**
- Request routing
- Authentication & Authorization
- Rate limiting
- SSL termination
- Request/Response transformation
- Load balancing
- Caching
- Logging & monitoring

```
Client → API Gateway → [/users  → User Service]
                     → [/orders → Order Service]
                     → [/pay    → Payment Service]
```

Popular: AWS API Gateway, Kong, Nginx, Traefik, Apigee

### Service Mesh

Handles service-to-service communication within the cluster via sidecar proxies (Envoy).

```
Service A → [Envoy sidecar] ──────────→ [Envoy sidecar] → Service B
                 ↕                              ↕
           Control Plane (Istio) — manages all sidecars
```

**Features:**
- Mutual TLS (mTLS) between services
- Traffic management (canary, circuit breaking, retries)
- Observability (metrics, traces, logs)
- Zero-trust security

**API Gateway vs Service Mesh:**

| Aspect | API Gateway | Service Mesh |
|--------|------------|-------------|
| Traffic | North-South (client ↔ services) | East-West (service ↔ service) |
| Purpose | External API management | Internal service communication |
| Examples | Kong, AWS API Gateway | Istio, Linkerd, Consul Connect |

---

## 17. Rate Limiting & Throttling

### Why Rate Limiting?
- Prevent abuse and DoS attacks
- Ensure fair usage among clients
- Protect backend from overload
- Enforce API tier limits (free vs paid)

### Rate Limiting Algorithms

**1. Fixed Window Counter:**
Count requests per fixed time window (e.g., 100 requests per minute).
```
Window: 12:00:00 – 12:01:00
Counter: 87 requests so far
Remaining: 13
```
Problem: Burst at window boundary (100 at 12:00:59 + 100 at 12:01:01 = 200 in 2 seconds).

**2. Sliding Window Log:**
Track timestamp of each request. Count requests in the sliding window.
```
Current time: 12:01:30
Window: last 60 seconds (12:00:30 – 12:01:30)
Count timestamps in window
```
Accurate but memory-intensive.

**3. Sliding Window Counter (Hybrid):**
Combine fixed windows with interpolation. Memory efficient and reasonably accurate.

**4. Token Bucket:**
Tokens added to bucket at a fixed rate. Each request consumes a token.
```
Bucket capacity: 100 tokens
Refill rate: 10 tokens/second
Request arrives: consume 1 token; if empty → rate limit
Allows bursting up to bucket size
```
Used by: AWS API Gateway, Stripe

**5. Leaky Bucket:**
Requests enter a queue and are processed at a fixed rate.
```
Queue capacity: 100 requests
Processing rate: 10 requests/second
Incoming burst is smoothed — requests leak out at constant rate
```

### Implementation with Redis

```python
def is_rate_limited(user_id, limit=100, window=60):
    key = f"rate:{user_id}:{int(time.time() // window)}"
    count = redis.incr(key)
    if count == 1:
        redis.expire(key, window)
    return count > limit
```

### Rate Limit Headers (Standard Practice)

```http
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 42
X-RateLimit-Reset: 1735689600
Retry-After: 30          # When rate limited (HTTP 429)
```

---

## 18. Authentication & Authorization at Scale

### Authentication vs Authorization
- **Authentication (AuthN):** Who are you? (Identity)
- **Authorization (AuthZ):** What are you allowed to do? (Permissions)

### Session-Based Auth

```
1. User logs in → Server creates session → Stores in Redis
2. Server sends session ID in cookie
3. Subsequent requests: browser sends cookie → server validates against Redis
```
Problem at scale: All servers need access to the session store (shared Redis).

### JWT (JSON Web Tokens)

```
Header.Payload.Signature

Header: {"alg": "HS256", "typ": "JWT"}
Payload: {"userId": "123", "role": "admin", "exp": 1735689600}
Signature: HMACSHA256(base64(header) + "." + base64(payload), secret)
```

**Access Token + Refresh Token Pattern:**
```
Access Token: Short-lived (15 minutes), sent with every request
Refresh Token: Long-lived (7 days), stored in httpOnly cookie, used to get new access token

Login → [Access Token (15min), Refresh Token (7 days)]
Request → Send Access Token in Authorization header
Token expires → Send Refresh Token → Get new Access Token
Logout → Invalidate Refresh Token in DB
```

### OAuth 2.0

Authorization framework for third-party access.

```
Authorization Code Flow:
1. User clicks "Login with Google"
2. App redirects to Google with client_id, scope, redirect_uri
3. User authenticates on Google, grants permission
4. Google redirects back with authorization code
5. App exchanges code for access_token + refresh_token (server-to-server)
6. App uses access_token to call Google APIs
```

**Roles:**
- **Resource Owner:** The user
- **Client:** Your application
- **Authorization Server:** Issues tokens (Google, GitHub)
- **Resource Server:** Protected API

### RBAC vs ABAC

**RBAC (Role-Based Access Control):**
```
Roles: Admin, Editor, Viewer
Admin:  read + write + delete
Editor: read + write
Viewer: read only
```

**ABAC (Attribute-Based Access Control):**
More fine-grained policies based on attributes.
```
Policy: Allow access if user.department == resource.department AND user.clearance >= resource.classification
```

### SSO (Single Sign-On)
Login once, access multiple applications.

Protocols: SAML 2.0, OpenID Connect (OIDC)

```
User → App A → SSO Identity Provider (login once) → Access App A
                                                   → Access App B
                                                   → Access App C
```

---

## 19. Storage Systems

### Object Storage
Store and retrieve unstructured data (files, images, videos) as objects with metadata.

- **Examples:** AWS S3, Google Cloud Storage, Azure Blob Storage
- **Features:** Globally unique keys, virtually unlimited capacity, 99.999999999% durability
- **Use cases:** Media storage, backups, data lakes, static website hosting

```
S3 structure:
  Bucket: my-app-media
    Key: users/avatar/user123.jpg
    Key: uploads/docs/report.pdf
```

### Block Storage
Low-latency, high-performance storage attached to virtual machines (like a virtual hard drive).

- **Examples:** AWS EBS, Google Persistent Disk
- **Use cases:** Databases, OS volumes, applications requiring fast I/O

### File Storage (NFS)
Shared file system accessible by multiple servers simultaneously.

- **Examples:** AWS EFS, NFS servers
- **Use cases:** Shared configuration files, legacy apps, content management

### Storage Comparison

| Type | Latency | Throughput | Cost | Use Case |
|------|---------|------------|------|----------|
| Object | High (ms) | High | Low | Media, backups |
| Block | Low (µs) | Very high | High | Databases |
| File | Medium | Medium | Medium | Shared files |
| Cache (Redis) | Very low (µs) | High | High | Hot data |

### Blob Storage Upload Flow (Large Files)

```
1. Client requests upload URL from server (presigned URL)
2. Server generates presigned S3 URL (valid 15 min)
3. Client uploads directly to S3 (bypasses your server)
4. S3 triggers event notification on upload complete
5. Server processes the file (resize image, transcode video)
```

Multipart upload for very large files:
```
File split into chunks → Upload chunks in parallel → S3 assembles
```

---

## 20. Search Systems

### Why Not Use Database LIKE Queries?
```sql
SELECT * FROM products WHERE name LIKE '%running shoes%';
```
- Full table scan: O(n)
- No relevance ranking
- No typo tolerance (fuzzy search)
- Slow for large datasets

### Full-Text Search with Elasticsearch

Elasticsearch is a distributed search engine built on Apache Lucene.

**Key Concepts:**
- **Index:** Collection of documents (like a database table)
- **Document:** JSON object (like a row)
- **Inverted Index:** Maps words to documents containing them

**Inverted Index Example:**
```
Word         → Documents
"running"    → [doc1, doc3, doc7]
"shoes"      → [doc1, doc2, doc5]
"nike"       → [doc1, doc4]

Query: "nike running shoes"
Result: doc1 (contains all 3) ranked highest
```

**Elasticsearch Query:**
```json
{
  "query": {
    "multi_match": {
      "query": "nike running shoes",
      "fields": ["name^3", "description", "tags"],
      "fuzziness": "AUTO"
    }
  },
  "filter": {
    "range": { "price": { "gte": 50, "lte": 200 } }
  },
  "sort": [{ "_score": "desc" }, { "popularity": "desc" }]
}
```

### Search Architecture

```
User query → Search Service → Elasticsearch → Ranked results

Data write path:
DB (source of truth) → Change Data Capture (CDC) → Kafka → Elasticsearch (search index)
```

### Typeahead / Autocomplete

```
User types: "runni..."
→ Query prefix index in Redis or Elasticsearch
→ Return top 5 suggestions: ["running shoes", "running shorts", ...]

Implementation options:
  - Trie data structure (in-memory)
  - Elasticsearch prefix/edge-ngram queries
  - Redis Sorted Set with prefix scoring
```

---

## 21. Real-Time Systems

### Polling vs WebSockets vs SSE

**Short Polling:**
Client repeatedly asks: "Any new data?"
```
Client → GET /updates → Server (every 5s)
```
Simple but inefficient (many empty responses).

**Long Polling:**
Server holds the request until data is available or timeout.
```
Client → GET /updates (server holds) → Response when data ready → Client reconnects
```
Better than polling, but still creates new connections frequently.

**Server-Sent Events (SSE):**
Persistent one-way HTTP connection from server to client.
```
Client connects once → Server pushes events
Content-Type: text/event-stream
data: {"message": "Hello"}\n\n
```
Good for: News feeds, stock tickers, notifications.

**WebSockets:**
Full-duplex, persistent bi-directional connection.
```
Client ←──→ Server (persistent connection)
```
Good for: Chat, live collaboration, gaming, trading platforms.

### Designing a Chat System

```
Architecture:
User A ──→ WebSocket Server 1 ──→ Message Queue (Kafka)
                                        ↓
User B ←── WebSocket Server 2 ←── Message Service

Components:
  - WebSocket servers (stateful — track connected users)
  - Redis Pub/Sub: broadcast messages between WebSocket servers
  - Kafka: durable message storage and fan-out
  - Cassandra: store chat history (write-heavy, time-series)
  - S3: store media attachments
```

**Message delivery guarantees:**
```
At-most-once:  Fire and forget. May lose messages.
At-least-once: Retry until ack. May duplicate.
Exactly-once:  Deduplication + idempotency. Complex but reliable.
```

---

## 22. Distributed Systems Concepts

### Consensus Algorithms

**Raft:**
Leader election + log replication. Used by etcd, CockroachDB.
```
Leader elected → All writes go through leader → Replicated to majority of followers
If leader fails → New election
```

**Paxos:**
Classic consensus algorithm. More complex, harder to understand and implement.

### Distributed Transactions

**Two-Phase Commit (2PC):**
```
Phase 1 (Prepare): Coordinator asks all participants "Can you commit?"
Phase 2 (Commit):  If all say Yes → Coordinator sends Commit
                   If any say No → Coordinator sends Rollback
```
Problem: Coordinator is a SPOF; blocking if coordinator crashes after prepare.

**Saga Pattern (Preferred for Microservices):**
Series of local transactions with compensating transactions (rollback actions) on failure.

### Idempotency
An operation is idempotent if repeating it produces the same result.

```
GET /users/123    → Idempotent (reading doesn't change state)
DELETE /users/123 → Idempotent (second delete is a no-op)
POST /orders      → NOT idempotent by default (creates duplicate)

Making POST idempotent:
Client sends unique Idempotency-Key header.
Server stores key in Redis.
If key seen before → return cached response.
```

### Leader Election
When multiple nodes need to coordinate, one is elected as the leader.

Used by: Kafka brokers, database primaries, distributed schedulers.

Tools: Zookeeper, etcd, Redis (Redlock)

### Bloom Filters
Probabilistic data structure to test if an element is in a set.

```
Space-efficient, very fast (O(1))
False positives possible, false negatives impossible
"Definitely not in set" OR "Probably in set"
```

Uses: Check if username is taken (before DB query), cache penetration prevention, browser safe browsing lists.

### Distributed Locking

```python
# Redis-based distributed lock
lock_key = f"lock:resource:order_{order_id}"
acquired = redis.set(lock_key, unique_value, nx=True, ex=30)  # nx=only if not exists
if acquired:
    try:
        process_order(order_id)
    finally:
        redis.delete(lock_key)  # Release lock
else:
    # Another instance is processing this order
    raise ResourceLockedException()
```

---

## 23. Data Pipelines & Stream Processing

### Batch Processing vs Stream Processing

**Batch Processing:**
Process large amounts of data at scheduled intervals.
```
Every night at 2 AM:
  Read all day's transactions → Calculate daily totals → Update reports
```
Tools: Apache Hadoop, Apache Spark

**Stream Processing:**
Process data in real-time as it arrives.
```
Transaction arrives → Instantly detect fraud → Block transaction immediately
```
Tools: Apache Kafka Streams, Apache Flink, Apache Spark Streaming

### Lambda Architecture

Combines batch and streaming for comprehensive data processing.

```
Data Source → [Speed Layer (Kafka/Flink)]  → Real-time views
           → [Batch Layer (Hadoop/Spark)]  → Batch views
                                              ↓
                                         [Serving Layer] → Query
```

### ETL (Extract, Transform, Load)

```
Extract:    Pull data from sources (DB, APIs, files, events)
Transform:  Clean, validate, aggregate, enrich data
Load:       Write to data warehouse or data lake

Example pipeline:
  PostgreSQL → Extract → Filter nulls, normalize dates → Load → BigQuery → Dashboards
```

### Data Warehouse vs Data Lake

| Aspect | Data Warehouse | Data Lake |
|--------|---------------|-----------|
| Data type | Structured, processed | Raw, any format |
| Schema | Schema-on-write | Schema-on-read |
| Users | Business analysts | Data scientists |
| Tools | Redshift, BigQuery, Snowflake | S3 + Athena, HDFS |
| Cost | Higher | Lower |

---

## 24. Monitoring, Logging & Alerting

### The Four Golden Signals (Google SRE)

1. **Latency:** Time to service a request (P50, P95, P99 percentiles)
2. **Traffic:** Request rate (requests/second)
3. **Errors:** Error rate (5xx errors / total requests)
4. **Saturation:** How full is the system (CPU %, memory %, queue depth)

### Logging

**Structured Logging (JSON):**
```json
{
  "timestamp": "2025-01-01T10:00:00Z",
  "level": "ERROR",
  "service": "order-service",
  "trace_id": "abc-123",
  "user_id": "user456",
  "message": "Payment failed",
  "error": "Insufficient funds",
  "duration_ms": 234
}
```

**Log Levels:**
```
DEBUG   → Detailed dev info (not in production)
INFO    → General operational events
WARN    → Unexpected but handled situations
ERROR   → Errors that need attention
FATAL   → System crash
```

**ELK Stack:**
```
Application → Logstash (collect/parse) → Elasticsearch (store/index) → Kibana (visualize)
```

### Metrics & Monitoring

**Prometheus + Grafana:**
```
App exposes /metrics → Prometheus scrapes every 15s → Grafana dashboards

Types of metrics:
  Counter:   Monotonically increasing (request count, error count)
  Gauge:     Point-in-time value (current connections, memory usage)
  Histogram: Distribution of values (request latency in buckets)
  Summary:   Percentiles (P99 latency)
```

### Alerting

**Alert on symptoms, not causes:**
```
Bad:  CPU > 80%               (cause — doesn't always mean user impact)
Good: Error rate > 1%         (symptom — users are seeing errors)
Good: P99 latency > 2 seconds (symptom — users see slowness)
```

**Alert fatigue:** Too many low-priority alerts → oncall engineers ignore them. Keep alerts actionable.

### Distributed Tracing

Track requests across services.
```
Request enters API Gateway
  → Span: api-gateway (5ms)
  → Span: auth-service (10ms)
  → Span: order-service (50ms)
    → Span: db-query (40ms) ← bottleneck identified
  Total: 65ms
```

Tools: Jaeger, Zipkin, AWS X-Ray, Datadog APM

---

## 25. Security in System Design

### Defense in Depth
Apply multiple layers of security:
```
Layer 1: Network (Firewall, VPC, Security Groups)
Layer 2: Transport (HTTPS/TLS, mTLS between services)
Layer 3: Application (Input validation, authentication, authorization)
Layer 4: Data (Encryption at rest, data masking)
Layer 5: Monitoring (Audit logs, anomaly detection)
```

### OWASP Top 10 (Most Critical Web Vulnerabilities)
1. Broken Access Control
2. Cryptographic Failures
3. Injection (SQL, NoSQL, command)
4. Insecure Design
5. Security Misconfiguration
6. Vulnerable & Outdated Components
7. Identification & Authentication Failures
8. Software & Data Integrity Failures
9. Security Logging & Monitoring Failures
10. Server-Side Request Forgery (SSRF)

### Encryption

**Encryption in Transit:** TLS 1.3 for all HTTP traffic; mTLS between microservices.

**Encryption at Rest:** AES-256 for database encryption; use cloud KMS (Key Management Service).

**Hashing passwords:** bcrypt, Argon2 (never MD5 or SHA-1 for passwords).

### DDoS Protection
```
Layer 3/4 DDoS: Volumetric attacks (SYN flood) → AWS Shield, Cloudflare
Layer 7 DDoS:   Application attacks (HTTP flood) → WAF + rate limiting

Tools:
  - Cloudflare: Absorbs attacks at edge
  - AWS WAF: Web Application Firewall rules
  - Rate limiting per IP
  - CAPTCHA for suspicious traffic
```

### Zero Trust Architecture
```
"Never trust, always verify"
- Every service-to-service call is authenticated (mTLS)
- Least privilege access (only necessary permissions)
- No implicit trust based on network location
- Continuous verification (short-lived tokens)
```

---

## 26. System Design — Step-by-Step Framework

### The RESHADED Framework

**R — Requirements Clarification (5 minutes)**
```
Functional Requirements: What does the system do?
  - What are the core features?
  - What are the actors (users, admins)?
  - What are the main flows?

Non-Functional Requirements: How does it perform?
  - Scale: How many users? DAU/MAU?
  - Latency: What's acceptable response time?
  - Availability: 99.9%? 99.99%?
  - Consistency: Strong or eventual?
  - Durability: Can we afford data loss?
```

**E — Estimation (5 minutes)**
```
Users: 100M registered users, 10M DAU
Read/Write ratio: 100:1 (read-heavy)

Writes: 10M DAU × 1 write/day = ~115 writes/second
Reads: 115 × 100 = ~11,500 reads/second

Storage:
  1KB per post × 100 posts/user × 100M users = 10 TB/year

Bandwidth:
  Read: 11,500 req/s × 1KB = 11.5 MB/s
  Write: 115 req/s × 1KB = 115 KB/s
```

**S — System Interface (API Design)**
```
Define core API endpoints:
  POST /posts          → createPost(userId, content, mediaUrls)
  GET  /feed/{userId}  → getFeed(userId, page, limit)
  POST /posts/{id}/like → likePost(userId, postId)
```

**H — High-Level Design**
```
Draw the major components:
  Clients → CDN → Load Balancer → Web Servers → Cache → Database
                                              → Message Queue → Workers
```

**A — Addressing Bottlenecks & Scaling**
```
Identify potential bottlenecks:
  - DB write throughput → Sharding
  - DB read throughput → Read replicas + Cache
  - Compute bottleneck → Horizontal scaling
  - Storage → Object storage (S3)
  - Hot data → CDN + Redis cache
```

**D — Deep Dive into Key Components**
```
Pick 2-3 critical components and go deep:
  - How does the feed generation work?
  - How does the caching layer handle invalidation?
  - How does the DB schema look?
```

**E — Edge Cases & Failure Scenarios**
```
  - What happens if the DB goes down?
  - What if the message queue is full?
  - How do you handle duplicate messages?
  - What if a single user has 100M followers (celebrity problem)?
```

**D — Diagrams & Summary**
```
Summarize the design, state trade-offs made.
```

---

## 27. Classic System Design Problems

### Design a URL Shortener (bit.ly)

**Requirements:**
- Create short URL for long URL
- Redirect short URL to long URL
- 100M URLs created/day, 10B redirects/day

**Core Design:**
```
Short URL: base62(id) → 7 characters → 62^7 = 3.5 trillion unique URLs

Write: POST /shorten → Generate ID → base62 encode → Store (id → longUrl) → Return short URL
Read:  GET /{shortCode} → Look up longUrl → HTTP 301/302 redirect

Storage: Key-Value store (Redis for hot URLs, Cassandra for all)
Cache: Top 20% URLs serve 80% of traffic (cache them in Redis)

301 vs 302 redirect:
  301 Permanent → Browser caches; fewer requests to our servers (can't track clicks)
  302 Temporary → Browser always hits our server (enables analytics)
```

### Design Twitter/X Feed

**Key challenges:** Fan-out on write vs fan-out on read

```
Fan-out on Write (Push):
  When user posts → push tweet to all followers' feed lists (Redis sorted sets)
  Read: just read pre-built feed list (fast reads, slow writes for celebrities)

Fan-out on Read (Pull):
  When user reads feed → fetch tweets from all followings and merge
  Write is simple; read is slow (merge thousands of feeds)

Hybrid (Twitter's approach):
  - Regular users: fan-out on write
  - Celebrities (1M+ followers): fan-out on read
  - Mix on read: pre-built feed + inject celebrity tweets at read time
```

### Design a Rate Limiter

```
Requirements: 100 requests/minute per user, distributed across servers

Implementation with Redis:
  Key: rate:{userId}:{minute}
  INCR key → count
  If count == 1: EXPIRE key 60
  If count > 100: return 429

Token Bucket in Redis:
  Lua script for atomic token consumption:
    1. Get current tokens and last refill time
    2. Calculate tokens to add based on elapsed time
    3. Take 1 token if available
    4. Update Redis
  Lua script ensures atomicity without race conditions.
```

### Design a Notification System

```
Components:
  Notification Service → Kafka → [Email Worker (SendGrid)]
                               → [SMS Worker (Twilio)]
                               → [Push Worker (FCM/APNs)]
                               → [In-App Worker]

Features:
  - User preferences (opt-in/out per channel and type)
  - Template engine (personalize messages)
  - Deduplication (don't send same notification twice)
  - Rate limiting per user (don't spam)
  - Delivery tracking (sent, delivered, opened)
  - Retry with backoff on failure
```

### Design a Distributed Cache (Redis-like)

```
Core operations: GET, SET, DEL, TTL
Data structure: Hash map in memory

Distribution: Consistent hashing across N nodes
  Each key → hash → assigned to a node
  Virtual nodes to handle uneven distribution

Replication: 1 primary + 2 replicas per shard (HA)
Eviction: LRU (Least Recently Used)
Persistence: RDB snapshots (periodic) + AOF (append-only log) for durability

Cache cluster: 3 shards × 3 nodes = 9 total nodes
  Shard 1: Primary (A1) + Replica (A2, A3)
  Shard 2: Primary (B1) + Replica (B2, B3)
  Shard 3: Primary (C1) + Replica (C2, C3)
```

### Design YouTube/Netflix (Video Streaming)

```
Upload Flow:
  1. User uploads video → Object Storage (S3) (raw)
  2. Upload triggers event → Video Processing Queue (Kafka)
  3. Transcoding workers process:
     → Convert to multiple resolutions (360p, 720p, 1080p, 4K)
     → Generate thumbnails
     → Extract metadata
  4. Processed videos stored in S3
  5. CDN configured to serve video segments

Streaming Flow:
  1. User requests video
  2. Metadata (title, URL, resolutions) from DB
  3. Video served from CDN edge servers (nearest to user)
  4. Adaptive Bitrate Streaming (ABR): player adjusts quality based on bandwidth
     Protocol: HLS (Apple) or DASH (MPEG)

Storage:
  Raw video: 500MB average × 500 hours uploaded/minute = massive scale → S3
  Metadata: PostgreSQL (title, description, creator, views)
  View counts: Redis Counter (async sync to DB)
  Recommendations: ML model → pre-computed in batch jobs
```

---

## Quick Reference Cheat Sheet

### Choose the Right Database

| Need | Database |
|------|---------|
| Strong ACID transactions | PostgreSQL, MySQL |
| Flexible schema, documents | MongoDB |
| Caching, sessions, leaderboards | Redis |
| Massive write scale, time-series | Cassandra |
| Full-text search | Elasticsearch |
| Graph relationships | Neo4j |
| Analytics, data warehouse | BigQuery, Redshift |
| Simple key-value at scale | DynamoDB |

### Choose the Right Tool

| Problem | Solution |
|---------|----------|
| Static content delivery | CDN (CloudFront, Cloudflare) |
| Distribute traffic | Load Balancer (Nginx, AWS ALB) |
| Service-to-service async | Message Queue (Kafka, SQS) |
| Shared state between servers | Redis |
| Large file storage | Object Storage (S3) |
| Internal microservice calls | gRPC |
| External API | REST |
| Real-time bi-directional | WebSockets |
| Server-to-client push | SSE |
| Search with relevance | Elasticsearch |

### Numbers to Remember

| Metric | Value |
|--------|-------|
| 1 day in seconds | ~86,400 |
| 1 month in seconds | ~2.6 million |
| 1 billion in bytes | ~1 GB |
| Single machine RPS | ~10,000-50,000 |
| Read from SSD | ~150 µs |
| Redis GET latency | ~0.1 ms |
| Cross-region latency | ~150 ms |
| 99.99% uptime/year | 52 min downtime |

---

*Master the fundamentals, practice trade-offs, and always clarify requirements before designing! 🚀*
