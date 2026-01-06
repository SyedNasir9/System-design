# Choosing Databases Based on Use Case

---

## The Core Rule

There is **no best database**.

There is only:
- Best database **for a workload**
- Best database **for constraints**
- Best database **for failure tolerance**

Choosing the wrong database usually works fine.
Until it doesn’t.
And then everything is on fire.

---

## First: Ask These Questions

Before picking a database, answer this:

1. What kind of data is this?
2. How often is it read vs written?
3. Does it need strict consistency?
4. How fast does it need to be?
5. How much will it grow?
6. Can data be eventually consistent?
7. Do we need complex queries or simple lookups?

If you skip this, you’re guessing.

---

## Data Shape → Database Type

### 1. Structured Data (Fixed Schema)

**Use when:**
- Tables
- Relationships
- Constraints
- Joins matter

**Database Type:**
- Relational (SQL)

**Examples:**
- PostgreSQL
- MySQL
- Oracle

**Use cases:**
- User accounts
- Financial systems
- Orders & payments
- Inventory

**Why SQL fits:**
- ACID guarantees
- Referential integrity
- Strong consistency
- Complex queries

---

### 2. Semi-Structured / Flexible Schema

**Use when:**
- Schema changes often
- Fields vary per record
- Rapid iteration

**Database Type:**
- Document stores (NoSQL)

**Examples:**
- MongoDB
- CouchDB

**Use cases:**
- User profiles
- CMS content
- Product catalogs
- Configuration data

**Why NoSQL fits:**
- Schema flexibility
- Easy horizontal scaling
- JSON-native storage

---

### 3. Key-Value Access Patterns

**Use when:**
- Simple get/set
- Ultra-low latency
- No complex queries

**Database Type:**
- Key-Value store

**Examples:**
- Redis
- DynamoDB

**Use cases:**
- Session storage
- Feature flags
- Caches
- Rate limiting counters

**Why this fits:**
- Extremely fast
- Predictable performance
- Scales easily

---

### 4. High Write Volume, Append-Only Data

**Use when:**
- Massive write throughput
- Time-based data
- Rare updates

**Database Type:**
- Time-series databases

**Examples:**
- InfluxDB
- Prometheus TSDB

**Use cases:**
- Metrics
- Logs
- Monitoring data
- IoT telemetry

**Why this fits:**
- Optimized for writes
- Efficient compression
- Time-based queries

---

### 5. Relationship-Heavy Data

**Use when:**
- Data is about connections
- Traversals matter
- Relationships change frequently

**Database Type:**
- Graph databases

**Examples:**
- Neo4j
- Amazon Neptune

**Use cases:**
- Social networks
- Recommendation engines
- Fraud detection
- Network topology

**Why this fits:**
- Fast relationship traversal
- Natural graph modeling
- Avoids join explosions

---

### 6. Full-Text Search & Analytics

**Use when:**
- Text search
- Aggregations
- Log analysis

**Database Type:**
- Search engines

**Examples:**
- Elasticsearch
- OpenSearch

**Use cases:**
- Search features
- Log analytics
- Observability dashboards

**Why this fits:**
- Inverted indexes
- Fast text queries
- Powerful aggregations

---

## Consistency vs Availability

CAP theorem matters whether you like it or not.

### Strong Consistency Needed
Choose:
- SQL databases
- Leader-based systems

Use cases:
- Payments
- Inventory counts
- Banking

---

### Eventual Consistency Acceptable
Choose:
- Distributed NoSQL
- Multi-region systems

Use cases:
- Social feeds
- Analytics
- Caching layers

---

## Scaling Requirements

### Vertical Scaling
Good when:
- Traffic is predictable
- Dataset is manageable

Databases:
- Traditional SQL

---

### Horizontal Scaling
Required when:
- Massive traffic
- Global users
- High availability

Databases:
- NoSQL
- Sharded SQL
- Cloud-native DBs

---

## Read vs Write Optimization

| Pattern | Better Choice |
|------|--------------|
| Read-heavy | Caches, replicas |
| Write-heavy | Log-based, NoSQL |
| Mixed | SQL + cache |
| Bursty traffic | Auto-scaling NoSQL |

---

## Latency Sensitivity

- Sub-millisecond → In-memory (Redis)
- Low ms → Local DB + cache
- Higher latency ok → Distributed systems

---

## Real-World Architectures Use Multiple Databases

This is normal:

- SQL for transactions
- Redis for caching
- Elasticsearch for search
- Object storage for blobs
- Time-series DB for metrics

One database to rule them all is a myth.

---

## Anti-Patterns to Avoid

- Using NoSQL to avoid schema design
- Using SQL for massive unbounded logs
- Using Elasticsearch as a primary DB
- Sharding before you need it
- Choosing DB based on resume value

---

## Decision Cheat Sheet

| Use Case | Database Type |
|-------|---------------|
| Payments | SQL |
| User sessions | Key-Value |
| Product catalog | Document |
| Metrics | Time-series |
| Social graph | Graph |
| Search | Search engine |

---

## Key Takeaways

- Start with the workload, not the tool
- Consistency requirements drive design
- Scale comes with trade-offs
- Multiple databases is normal
- Wrong DB choice hurts later, not now

---

## Mental Model

Databases are tools, not religions.

Choose the one that:
- Hurts the least
- Breaks the slowest
- Scales when you’re asleep
