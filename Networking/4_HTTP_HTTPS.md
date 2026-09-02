
# HTTP and HTTPS

> **HTTP (Hypertext Transfer Protocol)** is a request-response protocol used for communication between clients and servers.

It is widely used for:

- Websites
- REST APIs
- Mobile backends
- Service-to-service communication
- Web applications

**HTTPS = HTTP + TLS**

TLS provides:

- Encryption
- Data integrity
- Server authentication

HTTPS is the standard for production systems.

---

# 1. HTTP Request-Response

The basic HTTP communication model is:

```text
Client
   |
   | HTTP Request
   ↓
Server
   |
   | HTTP Response
   ↓
Client
````

Example:

```text
GET /users/123 HTTP/1.1
Host: api.example.com
Authorization: Bearer <token>
```

Response:

```text
HTTP/1.1 200 OK
Content-Type: application/json

{
  "id": 123,
  "name": "Pavan"
}
```

An HTTP request/response contains:

* **Method** → what operation is requested
* **URL/Path** → which resource
* **Headers** → additional information
* **Body** → data being sent

---

# 2. HTTP Request

A request commonly contains:

```text
Method
   ↓
GET /users/123

Headers
   ↓
Authorization: ...
Content-Type: application/json

Body
   ↓
JSON data (optional)
```

### Common Methods

| Method   | Purpose                             | Idempotent     |
| -------- | ----------------------------------- | -------------- |
| `GET`    | Read resource                       | Yes            |
| `POST`   | Create resource / perform operation | No by default  |
| `PUT`    | Create or replace resource          | Yes            |
| `PATCH`  | Partially update resource           | Not guaranteed |
| `DELETE` | Delete resource                     | Yes            |
| `HEAD`   | Get headers without response body   | Yes            |

### Safe vs Idempotent

**Safe** means the request is intended to read data without changing server state.

Example:

```text
GET /users/123
```

**Idempotent** means repeating the same request should have the same intended effect as performing it once.

Example:

```text
PUT /users/123
```

If the same PUT is sent multiple times, the intended final state should be the same.

---

# 3. HTTP Response

A response contains:

```text
Status Code
     ↓
200 OK

Headers
     ↓
Content-Type: application/json

Body
     ↓
{
   "name": "Pavan"
}
```

### Common Status Codes

| Range | Meaning                   | Examples                                 |
| ----- | ------------------------- | ---------------------------------------- |
| `1xx` | Informational             | `100`                                    |
| `2xx` | Success                   | `200`, `201`, `204`                      |
| `3xx` | Redirect / cache-related  | `301`, `302`, `304`                      |
| `4xx` | Client/request problem    | `400`, `401`, `403`, `404`, `409`, `429` |
| `5xx` | Server/dependency problem | `500`, `502`, `503`, `504`               |

Important examples:

```text
200 → Success
201 → Resource created
204 → Success, no response body

400 → Bad request
401 → Authentication required/failed
403 → Not allowed
404 → Resource not found
409 → Conflict
429 → Too many requests

500 → Internal server error
502 → Bad gateway
503 → Service unavailable
504 → Gateway timeout
```

Status codes are part of the API contract.

---

# 4. How HTTP Works

A typical HTTPS request looks like:

```text
Client
  |
  | 1. DNS lookup
  ↓
DNS
  |
  | IP address
  ↓
Client
  |
  | 2. TCP connection
  ↓
Server
  |
  | 3. TLS handshake
  ↓
Secure connection
  |
  | 4. HTTP request
  ↓
Load Balancer / Reverse Proxy
  |
  | 5. Route request
  ↓
Application Server
  |
  | 6. Process request
  ↓
HTTP Response
  |
  ↓
Client
```

Connections can often be **reused** for multiple HTTP requests instead of creating a new connection every time.

---

# 5. HTTP Is Stateless

> HTTP is a stateless protocol.

This means HTTP does not require the server to remember previous requests.

Each request should contain enough information for the server to understand it.

Example:

```text
Request 1:
GET /users/123
Authorization: token-A

Request 2:
GET /orders/456
Authorization: token-A
```

The server does not depend on an implicit transport-level session to understand Request 2.

However, real applications still maintain state.

State can live in:

```text
Client
  ↓
Cookies / Tokens

Server
  ↓
Session Store / Cache

Database
  ↓
Persistent State
```

This distinction is important:

> **HTTP is stateless, but the application can still be stateful.**

Stateless application servers are easier to horizontally scale because any server can handle any request.

---

# 6. HTTPS

> **HTTPS = HTTP over TLS**

```text
HTTP
  ↓
TLS
  ↓
TCP
  ↓
IP
```

HTTPS provides three important protections:

### 1. Confidentiality

Others cannot easily read the HTTP data while it is traveling across the network.

### 2. Integrity

Attackers cannot silently modify the protected data without detection.

### 3. Server Authentication

The client can verify that it is communicating with the intended server using the server's TLS certificate.

Example:

```text
Client
   |
   | HTTPS
   | 🔒 encrypted
   ↓
Server
```

HTTPS does **not** mean the application itself is secure.

It does not prevent:

* Broken authorization
* SQL injection
* Invalid business logic
* Compromised servers
* Malicious authenticated users

HTTPS protects the **communication channel**.

---

# 7. TLS

TLS is the security layer used by HTTPS.

At a high level:

```text
Client                          Server
   |                              |
   | ---- TLS Handshake --------> |
   | <--- Certificate ----------- |
   |                              |
   | ---- Key Exchange ---------> |
   |                              |
   | ===== Encrypted Data ======> |
   | <===== Encrypted Data ====== |
```

The TLS handshake establishes shared encryption keys.

After the handshake:

```text
HTTP Request
     ↓
TLS Encryption
     ↓
Network
     ↓
TLS Decryption
     ↓
HTTP Request
```

Modern systems use **TLS 1.3**.

You do not need to memorize the detailed TLS cryptographic steps for system-design fundamentals.

---

# 8. HTTP Versions

HTTP has evolved while keeping the same basic concepts:

```text
Request → Server → Response
```

The major difference is how HTTP is transported.

## HTTP/1.1

```text
HTTP/1.1
   ↓
 TCP
```

Characteristics:

* Text-based messages
* Persistent connections
* Simple and widely supported
* Multiple requests may require multiple connections for better concurrency

---

## HTTP/2

```text
HTTP/2
   ↓
 TCP
```

HTTP/2 uses **binary frames** instead of the traditional HTTP/1.1 text format.

Important features:

* Multiple streams over one TCP connection
* Header compression
* Better connection utilization
* Binary framing

Example:

```text
One TCP Connection
        |
        +--- HTTP Stream 1
        +--- HTTP Stream 2
        +--- HTTP Stream 3
```

However, all streams still share the same TCP connection.

If TCP loses a packet:

```text
TCP packet lost
      ↓
TCP retransmission
      ↓
Streams may wait
```

So HTTP/2 reduces HTTP-level blocking but does not eliminate TCP-level head-of-line blocking.

---

# 9. HTTP/3

```text
HTTP/3
   ↓
 QUIC
   ↓
 UDP
   ↓
 IP
```

HTTP/3 uses **QUIC** instead of TCP.

QUIC provides:

* Reliability
* Encryption
* Congestion control
* Multiple independent streams
* Connection migration

One major advantage is that packet loss on one QUIC stream does not block unrelated streams in the same way as TCP.

```text
HTTP/3
   |
   +--- Stream 1
   +--- Stream 2
   +--- Stream 3

Loss on Stream 1
       ↓
Stream 2 and 3 can continue
```

HTTP/3 can be especially useful for:

* Modern web applications
* Mobile clients
* High-latency networks
* Applications with many concurrent streams

---

# 10. HTTP Version Comparison

|                      | HTTP/1.1    | HTTP/2        | HTTP/3                    |
| -------------------- | ----------- | ------------- | ------------------------- |
| Transport            | TCP         | TCP           | QUIC / UDP                |
| Wire format          | Text        | Binary frames | Binary frames             |
| Multiplexing         | Limited     | Yes           | Yes                       |
| TCP HOL blocking     | Yes         | Yes           | No TCP layer              |
| Encryption           | Usually TLS | Usually TLS   | Built into QUIC           |
| Connection migration | No          | No            | Yes                       |
| Main improvement     | Simplicity  | Multiplexing  | Better transport behavior |

Remember:

```text
HTTP/1.1 → TCP
HTTP/2   → TCP
HTTP/3   → QUIC → UDP
```

---

# 11. HTTP Caching

HTTP supports caching to reduce latency and server load.

Common caching headers include:

* `Cache-Control`
* `ETag`
* `Last-Modified`
* `If-None-Match`

Example:

```text
Client
   |
   | GET /product/123
   ↓
Cache
   |
   | HIT
   ↓
Response
```

If the resource has not changed, the client can use a conditional request:

```text
Client
   |
   | If-None-Match: "abc123"
   ↓
Server
   |
   | 304 Not Modified
   ↓
Client uses cached copy
```

Caching can exist at multiple layers:

```text
Browser
   ↓
CDN
   ↓
Reverse Proxy
   ↓
Application Cache
   ↓
Database
```

Caching can significantly reduce latency and backend load.

---

# 12. HTTP in Distributed Systems

In distributed systems, HTTP requests often travel through multiple components:

```text
Client
   ↓
DNS
   ↓
CDN
   ↓
Load Balancer
   ↓
API Gateway / Reverse Proxy
   ↓
Service
   ↓
Database / Cache / Other Services
```

Each additional network hop adds:

* Latency
* Failure possibilities
* Connection management
* Timeout requirements

Therefore, distributed systems need careful handling of HTTP failures.

---

# 13. Timeouts

Every HTTP client should have a timeout.

Without a timeout:

```text
Client
   |
   | HTTP request
   ↓
Slow Server
   |
   | never responds
   ↓
Client waits...
   ↓
Connections/threads get exhausted
```

Timeouts prevent one slow dependency from consuming resources indefinitely.

Common timeout categories include:

* Connection timeout
* TLS timeout
* Request/response timeout
* Overall request deadline

---

# 14. Retries

A client may retry a failed HTTP request.

But retries are not always safe.

```text
Client
   |
   | POST /payment
   ↓
Server
   |
   | Payment succeeds
   |
   X Response lost
```

The client may think the request failed and retry:

```text
Retry
  ↓
Payment again
```

This can result in a duplicate operation.

Therefore, important state-changing APIs often use:

* Idempotency keys
* Request IDs
* Duplicate detection
* Retry limits
* Exponential backoff
* Jitter

Example:

```text
POST /payments

Idempotency-Key: abc123
```

If the same request is retried, the server can recognize it as the same operation.

---

# 15. HTTP vs TCP vs IP

These operate at different levels:

```text
HTTP
 ↓
What does the application want to communicate?

TCP
 ↓
How should application data be reliably transported?

IP
 ↓
Where should packets be routed?
```

Example:

```text
GET /users/123
      ↓
     HTTP
      ↓
TCP connection
      ↓
Destination IP
      ↓
Network
```

---

# ⭐ Interview Summary

```text
HTTP
 ↓
Request / Response
Methods / Headers / Status Codes / Body

HTTPS
 ↓
HTTP + TLS
 ↓
Encryption + Integrity + Server Authentication

HTTP/1.1
 ↓
TCP

HTTP/2
 ↓
TCP + Multiplexing

HTTP/3
 ↓
QUIC → UDP
```

### Remember

**HTTP → Application communication**

**HTTPS → Secure HTTP**

**TCP → Reliable ordered transport**

**UDP → Datagram transport**

**QUIC → Modern transport over UDP**

**HTTP/1.1 → TCP**

**HTTP/2 → TCP**

**HTTP/3 → QUIC → UDP**

For system design, the most important HTTP topics are:

> **Methods → Status Codes → Statelessness → HTTPS → HTTP Versions → Caching → Timeouts → Retries → Idempotency**

```

**This is the version I would put in your repository.** It is comprehensive enough for SDE3 system-design interviews while keeping the fundamentals section from becoming a networking deep dive.
```
