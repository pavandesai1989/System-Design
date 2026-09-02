

````markdown
## Layer 4 vs Layer 7 Load Balancing

Load Balancers can operate at different layers of the network stack.

The layer determines **how much information the Load Balancer can inspect** when making routing decisions.

        OSI Model
        ─────────

Layer 7 → Application     ← HTTP, HTTPS
Layer 6 → Presentation
Layer 5 → Session
Layer 4 → Transport       ← TCP, UDP
Layer 3 → Network         ← IP
Layer 2 → Data Link
Layer 1 → Physical

Why Layer 4 Load Balancer?

Because it operates primarily using information from the Transport Layer (Layer 4).

At Layer 4, the important information includes:

TCP / UDP
Source Port
Destination Port
Connection

---

### Layer 4 Load Balancing

**Layer 4 = Transport Layer (TCP/UDP)**

A Layer 4 Load Balancer makes routing decisions using **connection-level information**.

#### What it can see

- Source IP address
- Source port
- Destination IP address
- Destination port
- TCP/UDP protocol

#### What it cannot inspect

It does not understand application-level information such as:

- HTTP method (`GET`, `POST`)
- URL path (`/api/users`, `/api/feed`)
- HTTP headers
- Cookies
- HTTP request body

#### Routing Decision

Routing is based on information such as:

```text
Source IP
Destination IP
Port
Protocol
Connection state
````

Example:

```text
Client
   │
   │ TCP :443
   ▼
Layer 4 LB
   │
   ├──→ Server 1
   ├──→ Server 2
   └──→ Server 3
```

The LB can use algorithms such as:

* Round Robin
* Weighted Round Robin
* Least Connections

#### Key Characteristic

Layer 4 does **not need to understand the application protocol**.

It can forward TCP/UDP traffic without inspecting HTTP-level content.

This generally makes it:

* Faster
* Lower overhead
* Suitable for very high connection volumes

---

### Layer 7 Load Balancing

**Layer 7 = Application Layer (HTTP/HTTPS)**

A Layer 7 Load Balancer understands the **application-level protocol**, such as HTTP.

#### What it can see

Everything Layer 4 can see, plus HTTP information such as:

* HTTP method (`GET`, `POST`, `PUT`, `DELETE`)
* URL path (`/api/users/123`)
* HTTP headers
* Cookies
* Query parameters
* Content type
* HTTP request data

#### Routing Decision

It can make intelligent routing decisions based on application information.

Example:

```text
                    ┌──→ User Service
                    │
Client → L7 LB ─────┼──→ Feed Service
                    │
                    └──→ Payment Service
```

For example:

```text
/api/users/*   → User Service
/api/feed/*    → Feed Service
/api/payments/* → Payment Service
```

It can also route based on:

```text
URL path
HTTP headers
Cookies
API version
Query parameters
```

#### Other Capabilities

Layer 7 Load Balancers can also perform:

* TLS termination
* URL rewriting
* Header modification
* Cookie-based routing
* Content-based routing
* HTTP-aware health checks

---

## Layer 4 vs Layer 7

| Dimension             | Layer 4                               | Layer 7                   |
| --------------------- | ------------------------------------- | ------------------------- |
| Network layer         | Transport                             | Application               |
| Protocol examples     | TCP, UDP                              | HTTP, HTTPS               |
| Understands HTTP      | No                                    | Yes                       |
| URL-based routing     | No                                    | Yes                       |
| Header-based routing  | No                                    | Yes                       |
| Cookie-based routing  | No                                    | Yes                       |
| Content-based routing | No                                    | Yes                       |
| TLS termination       | Can support/pass through or terminate | Commonly terminates TLS   |
| URL rewriting         | No                                    | Yes                       |
| Overhead              | Lower                                 | Higher                    |
| Routing intelligence  | Basic                                 | Advanced                  |
| Typical use           | High-throughput TCP/UDP traffic       | Web applications and APIs |

---

## Example: Why Layer 7 Is More Intelligent

Suppose we have:

```text
Client
   │
   ▼
Load Balancer
```

The client sends:

```http
GET /api/users/123
```

### Layer 4

Layer 4 can see:

```text
TCP
Destination Port: 443
```

It **cannot understand** that the request is for `/api/users/123`.

So it cannot say:

```text
/api/users → User Service
```

### Layer 7

Layer 7 can inspect the HTTP request:

```text
GET /api/users/123
```

Therefore it can route:

```text
/api/users/* → User Service
```

This is the major difference:

> **Layer 4 routes based on connection/network information.**
>
> **Layer 7 routes based on application information.**

---

## When to Use Layer 4

Use Layer 4 when:

* You need very high throughput
* You are handling TCP/UDP traffic
* You don't need HTTP-aware routing
* Backend services should handle TLS themselves
* Low processing overhead is important

Examples:

```text
Game servers
TCP services
Database proxies
High-volume internal TCP traffic
```

**AWS example:** Network Load Balancer (NLB)

---

## When to Use Layer 7

Use Layer 7 when:

* You are running HTTP/HTTPS applications
* You need URL/path-based routing
* You need header or cookie-based routing
* You need TLS termination
* You need HTTP-aware features

Examples:

```text
/api/users  → User Service
/api/orders → Order Service
/api/feed   → Feed Service
```

**AWS example:** Application Load Balancer (ALB)

---

## Simple Mental Model

```text
Layer 4
────────

"What connection is this?"

IP + Port + Protocol
        │
        ▼
   Choose Server


Layer 7
────────

"What request is this?"

HTTP Method
URL
Headers
Cookies
        │
        ▼
Choose Service / Server
```

### Easy Memory

```text
L4 → CONNECTION

L7 → CONTENT
```

> **Layer 4 = Fast, connection-level routing**
>
> **Layer 7 = Intelligent, application-level routing**

### Interview Tip

For a typical web application:

```text
Client
   │
   ▼
Layer 7 Load Balancer
   │
   ├──→ User Service
   ├──→ Order Service
   └──→ Payment Service
```

Layer 7 is commonly preferred when the system needs **HTTP-aware routing**.

Layer 4 is preferred when **high-throughput TCP/UDP traffic and low overhead** are more important.

The two can also be combined:

```text
Internet
    │
    ▼
Layer 4 LB
    │
    ▼
Layer 7 LB
    │
    ├──→ Service A
    ├──→ Service B
    └──→ Service C
```

This allows the first layer to handle high-volume connections while Layer 7 performs intelligent application-level routing.

```

**One thing I would remove from your original notes:** specific numbers like “millions of connections/sec” vs “hundreds of thousands/sec.” Those are **not fixed properties** of L4 vs L7; actual capacity depends heavily on hardware, implementation, TLS, request complexity, and configuration.
```
