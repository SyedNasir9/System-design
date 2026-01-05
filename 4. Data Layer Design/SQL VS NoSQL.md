# SQL vs NoSQL (System Design Perspective)

---

## What Problem Databases Solve
Databases exist to:
- Store data
- Retrieve data efficiently
- Maintain correctness under concurrent access
- Scale with usage

The **core difference** between SQL and NoSQL is **how they prioritize consistency, structure, and scalability**.

---

## What is SQL?

### Definition
SQL databases (Relational Databases) store data in **structured tables** with:
- Fixed schema
- Rows and columns
- Relationships enforced via keys

Examples:
- MySQL
- PostgreSQL
- Oracle
- SQL Server

---

### Data Model
- Tables
- Rows = records
- Columns = attributes
- Relationships via **primary keys** and **foreign keys**

Example:
- Users table
- Orders table
- Linked using user_id

---

### Key Characteristics
- **Schema-first** design
- Strong data integrity
- ACID transactions
- Powerful querying with joins

---

### ACID Properties
SQL databases prioritize **ACID**:

- **Atomicity** – All or nothing
- **Consistency** – Data always valid
- **Isolation** – Concurrent transactions don’t interfere
- **Durability** – Data survives crashes

This makes SQL reliable but sometimes slower at scale.

---

### Strengths of SQL
- Strong consistency
- Complex queries and joins
- Mature tooling and ecosystem
- Ideal for transactional systems

---

### Limitations of SQL
- Vertical scaling preferred (bigger machines)
- Schema changes are costly
- Performance bottlenecks at massive scale
- Harder to distribute globally

---

## What is NoSQL?

### Definition
NoSQL databases are **non-relational** and designed for:
- High scalability
- Flexible schemas
- Distributed systems

Examples:
- MongoDB
- Cassandra
- DynamoDB
- Redis
- Couchbase

---

### Data Models in NoSQL
NoSQL is not one thing. Common models include:

- **Key-Value** (Redis, DynamoDB)
- **Document** (MongoDB)
- **Wide-Column** (Cassandra)
- **Graph** (Neo4j)

---

### Schema Model
- **Schema-less or schema-flexible**
- Each record can have different fields
- Application enforces structure, not the database

---

### Consistency Model
Most NoSQL systems follow **BASE** instead of ACID:

- **Basically Available**
- **Soft state**
- **Eventually consistent**

This trades strict correctness for scalability and availability.

---

### CAP Theorem Context
In distributed systems, you can only fully guarantee **two** of:

- **Consistency**
- **Availability**
- **Partition tolerance**

SQL systems lean toward **Consistency**
NoSQL systems often lean toward **Availability + Partition tolerance**

---

### Strengths of NoSQL
- Horizontal scaling (add more nodes)
- Handles massive traffic
- Flexible data models
- Designed for cloud-native systems

---

### Limitations of NoSQL
- Weaker consistency guarantees
- Limited joins or none at all
- Data duplication is common
- Complex queries handled at application layer

---

## SQL vs NoSQL Comparison

| Aspect | SQL | NoSQL |
|-----|----|------|
| Data model | Tables | Key-value / Document / Column |
| Schema | Fixed | Flexible |
| Consistency | Strong (ACID) | Eventual (BASE) |
| Scaling | Vertical | Horizontal |
| Joins | Supported | Limited or none |
| Transactions | Strong | Limited / scoped |
| Use case | OLTP systems | Large-scale distributed apps |

---

## When to Use SQL

Use SQL when:
- Data relationships matter
- Strong consistency is mandatory
- Transactions are critical
- Data size is manageable
- Reporting and analytics are complex

Examples:
- Banking systems
- E-commerce payments
- Inventory systems
- User authentication

---

## When to Use NoSQL

Use NoSQL when:
- Scale is massive
- Traffic is unpredictable
- Schema evolves frequently
- Global distribution is required
- Latency matters more than strict consistency

Examples:
- Social media feeds
- IoT data ingestion
- Session storage
- Caching
- Event logging

---

## Hybrid Reality (What Actually Happens)
Most real systems use **both**.

Example:
- SQL for users, payments, orders
- NoSQL for sessions, feeds, analytics, caching

Choosing one exclusively is usually a design failure.

---

## Common Design Mistakes
- Using NoSQL just because “scale”
- Using SQL for unbounded event logs
- Treating NoSQL as schema-free chaos
- Ignoring operational complexity
- Designing around tools instead of access patterns

---

## System Design Takeaways
- SQL = correctness-first
- NoSQL = scale-first
- Neither is “better”
- Data access patterns matter more than trends
- Design for failure, not perfection

---

## Final Mental Model
SQL says:  
**“Data must always be correct.”**

NoSQL says:  
**“The system must always be up.”**

Good architecture knows **when to prioritize which**.
