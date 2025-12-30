# HTTP (Hypertext Transfer Protocol)

---

## What is HTTP?
HTTP is an **application-layer protocol** used for fetching resources such as:
- HTML documents
- Images
- Videos
- Scripts
- API data

It is the **foundation of data exchange on the Web** and follows a **client–server model**, where the client initiates requests and the server responds.

A single web page is usually composed of multiple resources fetched via HTTP.

---

## Key Characteristics of HTTP
- Client–server protocol
- Request–response based
- Stateless by default
- Extensible via headers
- Runs over reliable transport protocols

---

## HTTP in the Network Stack
- **Application Layer:** HTTP
- **Transport Layer:** TCP (or QUIC over UDP)
- **Network Layer:** IP

HTTP focuses on **what** data is exchanged, not **how** packets are routed.

---

## Components of HTTP-Based Systems

### Client (User-Agent)
- Usually a web browser
- Always initiates requests
- Fetches HTML first, then additional resources:
  - CSS
  - JavaScript
  - Images
  - Videos

Browsers also:
- Parse responses
- Execute scripts
- Render pages
- Fetch additional resources dynamically

---

### Server
- Handles client requests
- Returns responses
- May represent:
  - A single machine
  - A cluster of servers
  - Load balancers
  - Cache layers
  - Application servers

Appears as a single logical endpoint to the client.

---

### Proxies
Intermediate systems between client and server.

Common proxy functions:
- Caching
- Load balancing
- Authentication
- Filtering
- Logging
- Tunneling

Proxies can be:
- Transparent (forward unchanged requests)
- Non-transparent (modify requests or responses)

---

## Basic Aspects of HTTP

### HTTP is Simple
- Human-readable in HTTP/1.x
- Easy to debug
- Text-based headers

---

### HTTP is Extensible
- Headers allow new features
- Clients and servers can agree on custom behavior
- Core protocol remains stable

---

### HTTP is Stateless (But Not Sessionless)
- Each request is independent
- No memory of previous requests

State is added using:
- Cookies
- Tokens
- Sessions managed at application level

---

## HTTP and Connections

HTTP does **not** manage connections directly.

### Transport Behavior by Version
- **HTTP/1.0:** One TCP connection per request
- **HTTP/1.1:** Persistent connections
- **HTTP/2:** Multiplexing over a single connection
- **HTTP/3:** Runs over QUIC (UDP-based)

---

## What Can Be Controlled by HTTP
- Caching behavior
- Authentication
- Sessions
- Cross-origin access (CORS)
- Proxy behavior
- Content negotiation
- Redirection

---

## HTTP Request–Response Flow

1. Client opens a connection
2. Client sends HTTP request
3. Server processes request
4. Server sends HTTP response
5. Connection is closed or reused

---

## HTTP Messages

Two message types:
- Requests
- Responses

---

## HTTP Request Structure

A request consists of:
- HTTP method
- Resource path
- HTTP version
- Headers (optional)
- Body (optional)

Example:
GET / HTTP/1.1
Host: example.com
Accept-Language: en-US

yaml
Copy code

---

## HTTP Response Structure

A response consists of:
- HTTP version
- Status code
- Status message
- Headers
- Optional body

Example:
HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 1024

yaml
Copy code

---

## Common HTTP Methods

| Method | Purpose |
|------|---------|
| GET | Retrieve resource |
| POST | Submit data |
| PUT | Replace resource |
| PATCH | Partial update |
| DELETE | Remove resource |
| HEAD | Headers only |
| OPTIONS | Capability discovery |

---

## HTTP Status Code Classes

| Range | Meaning |
|------|--------|
| 1xx | Informational |
| 2xx | Success |
| 3xx | Redirection |
| 4xx | Client errors |
| 5xx | Server errors |

---

## HTTP APIs

### Fetch API
- Modern JavaScript API for HTTP requests
- Replaces XMLHttpRequest
- Promise-based

---

### Server-Sent Events (SSE)
- One-way communication
- Server pushes events to client
- Uses HTTP as transport

---

## HTTP Versions Overview

| Version | Key Feature |
|------|------------|
| HTTP/1.0 | Basic request-response |
| HTTP/1.1 | Persistent connections |
| HTTP/2 | Multiplexing, header compression |
| HTTP/3 | QUIC-based transport |

---

## Key Takeaways (System Design Perspective)
- HTTP is stateless by design
- Sessions are layered on top
- Performance depends on:
  - Caching
  - Connection reuse
  - Protocol version
- HTTP powers:
  - Web apps
  - REST APIs
  - Microservices
  - Cloud services

---

## Final Notes
- HTTP semantics have stayed stable since HTTP/1.0
- New versions optimize performance, not meaning
- Understanding HTTP is mandatory for backend, DevOps, and system design
