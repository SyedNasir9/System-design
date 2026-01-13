# Databases (High-Level Overview)

## Why Databases Matter
The choice of database is one of the most impactful decisions in system design.  
It directly affects:

- Performance
- Consistency
- Availability
- Cost
- Scalability

A poor database choice can introduce long-term limitations and often leads to painful and risky migrations later in the system’s life.

---

## Common Database Categories

### Relational Databases
Relational databases store data in structured tables with fixed schemas and strong transactional guarantees.

**Characteristics**
- Strong consistency
- ACID transactions
- Complex querying support

**Common Use Cases**
- Financial systems
- Inventory management
- Order processing

---

### Key-Value Stores
Key-value databases store data as simple key–value pairs and are optimized for speed and scalability.

**Characteristics**
- Extremely fast reads and writes
- Horizontally scalable
- Limited querying capabilities

**Common Use Cases**
- Caching
- Session storage
- User personalization data

---

### Document Databases
Document databases store semi-structured data, typically in JSON-like formats.

**Characteristics**
- Flexible schema
- Easy to evolve over time
- Natural fit for object-based data models

**Common Use Cases**
- User profiles
- Content management systems
- Rapidly evolving applications

---

### Wide-Column Stores
Wide-column databases organize data into column families and are designed for high write throughput.

**Characteristics**
- Highly scalable
- Optimized for large datasets
- Suitable for time-series or event data

**Common Use Cases**
- Logging systems
- Telemetry data
- Analytics pipelines

---

### Graph Databases
Graph databases focus on relationships between entities rather than individual records.

**Characteristics**
- Efficient relationship traversal
- Optimized for connected data
- Not ideal for generic workloads

**Common Use Cases**
- Social networks
- Recommendation engines
- Fraud detection

---

## Key Takeaway
Choose databases based on **access patterns, consistency needs, and scale requirements**, not popularity or trends.
