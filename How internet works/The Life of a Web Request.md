.

````markdown
# The Life of a Web Request

Before designing distributed systems, we need to understand how a web request travels between a client and a server.

Almost every modern system — chat, video streaming, payments, e-commerce — relies on these fundamental concepts.

---

## What We Will Cover

1. Client-Server Model
2. DNS
3. IP Addresses and Ports
4. TCP and UDP
5. HTTP and HTTPS
6. Request-Response Lifecycle
7. Latency and Round-Trip Time (RTT)

---

## Why This Matters in System Design

| Concept | Where It Matters |
|---|---|
| **DNS** | CDN routing, global load balancing, failover |
| **TCP vs UDP** | APIs, chat, video streaming, gaming |
| **HTTP/HTTPS** | APIs, REST, authentication, status codes |
| **Latency** | Performance, caching, server placement |

---

## The Big Picture

When a user opens a website or mobile application, a request typically goes through several components:

```text
User
  │
  ▼
DNS
  │
  ▼
Internet
  │
  ▼
Load Balancer / CDN
  │
  ▼
Web / Application Server
  │
  ├────► Cache
  │
  └────► Database / Object Storage
````

Each component solves a different problem.

### Key Idea

> **Understanding the journey of a web request is the foundation for understanding system design.**

We will trace this journey step by step, starting with the **Client-Server Model**.

```

I would **not** add the detailed “YouTube → DNS → TCP → TLS → HTTP → LB → server...” flow here. That belongs later as a final **complete request lifecycle** section, where it will be much more useful.
```
