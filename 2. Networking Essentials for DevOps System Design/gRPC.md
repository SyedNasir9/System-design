# gRPC (Google Remote Procedure Call)

---

## What is gRPC?
gRPC is a **high-performance Remote Procedure Call (RPC) framework** developed by Google.

It allows a **client application to directly call methods on a server application** running on a different machine as if it were a local object.

gRPC is commonly used for:
- Microservices communication
- Internal service-to-service APIs
- High-performance, low-latency systems

---

## Key Characteristics of gRPC
- Uses **HTTP/2** as the transport protocol
- Supports **bi-directional streaming**
- Strongly typed APIs
- Efficient binary serialization
- Language-agnostic (polyglot support)

---

## gRPC vs REST (High-Level)
- REST: Resource-based, HTTP + JSON
- gRPC: Method-based, HTTP/2 + Protocol Buffers

gRPC is generally preferred for **internal APIs**, while REST is common for **public APIs**.

---

## Core gRPC Concepts

### Client–Server Model
- The **client** calls methods defined on the server
- The **server** implements those methods and responds
- Communication feels like a local function call

---

### Service Definition
gRPC services are defined using **Protocol Buffers (.proto files)**.

A service specifies:
- Available RPC methods
- Request message types
- Response message types

---

### Stub (Client)
- Generated client-side code
- Exposes the same methods as the server
- Handles serialization, networking, and deserialization

---

### Server
- Implements the service interface
- Runs a gRPC server to handle incoming calls

---

## gRPC Architecture (Conceptual)

Client → Stub → HTTP/2 → gRPC Server → Service Implementation

Both client and server:
- Can run in different environments
- Can be written in different languages

---

## Language & Platform Support
gRPC supports many languages, including:
- Go
- Java
- C++
- Python
- C#
- Ruby
- JavaScript
- Dart

Clients and servers can be written in **different languages**.

---

## Protocol Buffers (Protobuf)

### What are Protocol Buffers?
Protocol Buffers are Google’s **binary serialization format** used by gRPC.

They are:
- Faster than JSON
- Smaller in size
- Strongly typed
- Language-neutral

---

## Defining Data with Protocol Buffers

Data structures are defined in `.proto` files.

Example:
```proto
message Person {
  string name = 1;
  int32 id = 2;
  bool has_ponycopter = 3;
}
