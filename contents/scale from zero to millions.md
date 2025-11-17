# Scale From Zero To Millions: System Design Essentials

This guide walks through growing a simple app from a single machine to handling millions of users. It covers single-server setup, how requests and responses flow through servers and databases, database choices, vertical vs. horizontal scaling, and how to put a load balancer in front with practical examples.

## 1) Single-Server Setup (Start Here)

- App, database, and cache run on one host/VM/container.
- Easiest to build, deploy, and debug. Perfect for MVPs.

Basic architecture:

```
Client (Browser/App)
	 |
	 v
App Server (API + Views)  ──> (optional in-memory cache)
	 |
	 v
Database (e.g., Postgres/MySQL)
```

Pros:
- Simple ops, lowest latency (local DB), cheap.

Cons:
- Single point of failure, limited capacity, noisy neighbors (app and DB competing for CPU/RAM/IO).

## 2) How Request/Response Flows Through Server and Database

Sequence for a typical read path:

1. Client sends HTTP request to server (e.g., `GET /users/123`).
2. Server authenticates/authorizes request.
3. Server checks cache (memory/Redis) for `user:123`.
4. If cache hit → return cached value.
5. If cache miss → query DB (`SELECT * FROM users WHERE id=123`).
6. Server serializes result to JSON/HTML.
7. Server sends HTTP response back to client.

ASCII flow:

```
Client → App → [Cache?] → DB → App → Client
```

Write path (simplified):

1. Client sends `POST /users` with payload.
2. Server validates input and writes to DB within a transaction.
3. Server may invalidate or update cache keys related to the new data.
4. Server returns 201/200 with created/updated resource.

## 3) Which Database Is “Best”?

There is no universal “best”; choose based on workload and data model. Practical defaults:

- Start with a relational database: Postgres (recommended) or MySQL.
	- Strong consistency, ACID transactions, mature tooling, flexible schema (with migrations).
	- Great for OLTP workloads (most web apps).
- Add Redis as a cache (hot keys, session store, queues/lightweight rate limits).
- Consider NoSQL only for specific needs:
	- MongoDB: flexible documents, rapidly evolving schemas, nested JSON.
	- DynamoDB/Cassandra: massive write throughput, horizontally scalable key-value/columnar models.
	- Elastic/OpenSearch: full-text search and analytics (not a source of truth).

Rules of thumb:
- If your data has relationships, transactions, and complex queries → Postgres/MySQL.
- If you need huge write scale with simple key patterns → DynamoDB/Cassandra.
- For caching and ephemeral data → Redis/Memcached.

## 4) Vertical Scaling (Scale Up)

Upgrade the machine: more CPU, RAM, faster disks.

Pros:
- Easiest change: no app code change, minimal infra work.

Cons:
- Diminishing returns and hard limits.
- Still a single point of failure.

Use when:
- You’re early-stage and under capacity; it buys time quickly.

## 5) Horizontal Scaling (Scale Out)

Run multiple instances of the app and/or database across machines.

App tier:
- Make app stateless: no local sessions or local file writes.
- Use shared session store (Redis) and shared object storage (S3/GCS) for uploads.
- Put a load balancer in front.

Database tier:
- Add read replicas for read-heavy workloads.
- Use a write primary + multiple read replicas; route reads accordingly.
- For very large datasets/throughput, consider sharding/partitioning.

Cache tier:
- Add Redis/Memcached cluster to reduce DB load and tail latency.

Messaging/async:
- Introduce a queue (SQS, RabbitMQ, Kafka) for background jobs (emails, webhooks, indexing) to keep request latency low.

## 6) Load Balancer

Role:
- Distribute traffic across multiple healthy app instances.
- Perform health checks, TLS termination, and optional sticky sessions.

Common algorithms:
- Round Robin, Least Connections, IP Hash, Weighted variants.

Sticky sessions:
- Useful if you must keep session affinity, but prefer stateless sessions with a shared store for true elasticity.

## 7) Practical Implementation (Minimal Examples)

### 7.1 Simple App Server (Node.js/Express)

```javascript
// server.js
const express = require('express');
const app = express();
app.use(express.json());

app.get('/health', (req, res) => res.send('ok'));

app.get('/users/:id', async (req, res) => {
	// pseudo: cache.get(`user:${req.params.id}`) → db fallback
	res.json({ id: req.params.id, name: 'Alice' });
});

const port = process.env.PORT || 3000;
app.listen(port, () => console.log(`Server on :${port}`));
```

Run two instances locally:

```bash
PORT=3000 node server.js &
PORT=3001 node server.js &
```

### 7.2 Nginx as a Load Balancer

`nginx.conf` snippet:

```nginx
http {
	upstream app_upstream {
		server 127.0.0.1:3000;
		server 127.0.0.1:3001;
	}

	server {
		listen 8080;
		location / {
			proxy_pass http://app_upstream;
			proxy_set_header Host $host;
			proxy_set_header X-Real-IP $remote_addr;
			proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
			proxy_set_header X-Forwarded-Proto $scheme;
		}
	}
}
```

Test:

```bash
curl -s http://localhost:8080/health
```

### 7.3 HAProxy as a Load Balancer (Alternative)

`haproxy.cfg` snippet:

```haproxy
frontend http_in
	bind *:8080
	default_backend app_servers

backend app_servers
	balance roundrobin
	server s1 127.0.0.1:3000 check
	server s2 127.0.0.1:3001 check
```

Run:

```bash
haproxy -f haproxy.cfg
```

### 7.4 Postgres Quick Start (Local)

Using Docker:

```bash
docker run --name pg -e POSTGRES_PASSWORD=postgres -p 5432:5432 -d postgres:16
```

Basic table and query:

```sql
CREATE TABLE users (id BIGSERIAL PRIMARY KEY, name TEXT NOT NULL);
INSERT INTO users (name) VALUES ('Alice');
SELECT * FROM users WHERE id = 1;
```

Node.js driver example (pg):

```javascript
const { Pool } = require('pg');
const pool = new Pool({ connectionString: process.env.DATABASE_URL });

async function getUser(id) {
	const { rows } = await pool.query('SELECT id, name FROM users WHERE id=$1', [id]);
	return rows[0];
}
```

### 7.5 Redis for Sessions/Cache

```bash
docker run -p 6379:6379 -d redis:7
```

Node.js (ioredis) example:

```javascript
const Redis = require('ioredis');
const redis = new Redis();
await redis.set('user:1', JSON.stringify({ id: 1, name: 'Alice' }), 'EX', 300);
const val = await redis.get('user:1');
```

### 7.6 Horizontal Scaling With Kubernetes (Optional)

```bash
kubectl create deploy web --image=node:20 -- node server.js
kubectl expose deploy web --type=ClusterIP --port=3000
kubectl scale deploy web --replicas=5
```

### 7.7 Cloud Load Balancer (AWS ALB Example)

Steps:
- Put app instances in an Auto Scaling Group.
- Create an ALB, add target group pointing to instances.
- Configure health checks on `/health`.
- Point DNS (Route 53) to the ALB.

## 8) Evolution Path

1. Single server (app+DB) → measure.
2. Add Redis cache; move sessions off the app.
3. Split DB to its own host; add read replicas.
4. Add load balancer and multiple stateless app instances.
5. Introduce async jobs (queue) and object storage for files.
6. Consider sharding/partitioning and services as needed.

## 9) Observability and Reliability

- Metrics (CPU, memory, latency, error rate, throughput).
- Logs (structured, centralized: Loki/ELK).
- Traces (OpenTelemetry/Jaeger).
- Health checks and autoscaling policies.
- Backups and restore drills for the database.

## 10) Common Pitfalls

- Premature sharding or microservices; start simple.
- Keeping state on the app instance; breaks scaling.
- Missing cache invalidation strategy; stale data bugs.
- No backpressure or rate limiting; cascading failures.
- Ignoring idempotency for writes and retries.

---

Use this as a reference to grow pragmatically: start simple, measure, and scale where the bottleneck actually is.

