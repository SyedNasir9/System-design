# REST API (Representational State Transfer)

---

## What is a REST API?
REST API stands for **Representational State Transfer Application Programming Interface**.

It is an **architectural style** that enables communication between systems over the Internet using **HTTP**.

REST APIs:
- Use HTTP methods
- Exchange data via requests and responses
- Commonly use **JSON** as the data format

---

## REST vs HTTP (Important Distinction)
- **REST** is an architectural design style
- **HTTP** is the communication protocol

REST defines:
- How APIs should be structured
- How resources should be identified
- How operations should behave

HTTP defines:
- How messages are sent over the network

They work together, but they are **not the same thing**.

---

## How REST APIs Work

1. Client sends an HTTP request to a URL
2. Request uses an HTTP method
3. Server processes the request
4. Server returns a response (usually JSON)
5. Client consumes the response

---

## REST and Resources
- Everything is treated as a **resource**
- Resources are identified by **URLs**

Examples:
/users
/users/123
/orders/456

yaml
Copy code

---

## CRUD Operations in REST

| Operation | HTTP Method |
|---------|-------------|
| Create | POST |
| Read | GET |
| Update | PUT / PATCH |
| Delete | DELETE |

---

## Common HTTP Methods Used in REST

### 1. GET
Used to **retrieve** a resource.

- Safe
- Idempotent
- No request body (typically)

Example:
GET /users/123

yaml
Copy code

Success:
- `200 OK`

Errors:
- `404 Not Found`
- `400 Bad Request`

---

### 2. POST
Used to **create** a new resource.

- Not safe
- Not idempotent

Example:
POST /users
{
"name": "Anjali",
"email": "gfg@example.com"
}

yaml
Copy code

Success:
- `201 Created`
- `Location` header points to new resource

---

### 3. PUT
Used to **update or replace** a resource.

- Sends the entire resource
- Idempotent

Example:
PUT /users/123
{
"name": "Anjali",
"email": "gfg@example.com"
}

yaml
Copy code

If the resource does not exist, it **may be created**.

---

### 4. PATCH
Used to **partially update** a resource.

- Sends only changed fields
- Not always idempotent

Example:
PATCH /users/123
{
"email": "new.email@example.com"
}

yaml
Copy code

---

### PUT vs PATCH

| PUT | PATCH |
|----|------|
| Replaces entire resource | Updates specific fields |
| Full payload required | Partial payload |
| Idempotent | Not guaranteed |

---

### 5. DELETE
Used to **delete** a resource.

- Idempotent

Example:
DELETE /users/123

yaml
Copy code

Success:
- `200 OK`
- `204 No Content`

---

## Idempotence
An HTTP method is **idempotent** if:
- Multiple identical requests produce the same result

Examples:
- GET ✔
- PUT ✔
- DELETE ✔
- POST ✘

---

## REST API Core Principles

### Stateless
- Each request contains all required information
- Server does not store client session state

---

### Client–Server Architecture
- Client and server are independent
- Enables scalability and separation of concerns

---

### Cacheable
- Responses can be cached
- Improves performance and reduces server load

---

### Uniform Interface
- Consistent URL structure
- Standard HTTP methods
- Predictable behavior

---

### Layered System
- APIs can be deployed across multiple layers
- Improves security and scalability

---

## Real-World Use Cases
- Social media platforms
- E-commerce systems
- Payment gateways
- Weather and geolocation services
- Mobile and web application backends

---

## Key Takeaways (System Design Perspective)
- REST is resource-oriented
- HTTP methods map cleanly to CRUD
- Stateless design improves scalability
- Idempotence matters for retries and failures
- REST APIs are foundational to microservices

---

## Final Notes
- REST is simple but easy to misuse
- Correct HTTP semantics matter
- Clean URLs + stateless design = maintainable APIs