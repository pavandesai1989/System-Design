
# Proxy

A **proxy** is a server that sits between a client and another server and forwards traffic.

The key question is:

> **Whose side does the proxy represent?**

```text
Forward Proxy
Client → Proxy → Internet
   ↑
represents client


Reverse Proxy
Internet → Proxy → Backend Servers
             ↑
       represents servers
````

---

# 1. Forward Proxy

A **forward proxy** sits between clients and the servers they want to access.

```text
Client 1 ──┐
Client 2 ──┼──→ Forward Proxy ──→ Internet
Client 3 ──┘
```

The destination server sees the **proxy** as the direct caller.

### Common Uses

* Control outbound traffic
* Block websites/services
* Logging and auditing
* Content filtering
* IP masking
* Caching

Example:

```text
Employee → Company Proxy → Internet
```

The company can control and monitor traffic leaving its network.

---

# 2. Reverse Proxy

A **reverse proxy** sits in front of backend servers.

```text
                 ┌──→ Server 1
Client → Reverse ├──→ Server 2
        Proxy    └──→ Server 3
```

Clients communicate with the reverse proxy instead of directly accessing backend servers.

### Common Uses

* Load balancing
* HTTPS/TLS termination
* Request routing
* Caching
* Rate limiting
* Authentication support
* Security/WAF
* Logging and monitoring

Example:

```text
Client
  │
  ▼
api.example.com
  │
  ▼
Reverse Proxy
  │
  ├──→ App Server 1
  ├──→ App Server 2
  └──→ App Server 3
```

Backend servers can remain private and can be added or removed without changing the public endpoint.

---

# 3. Forward Proxy vs Reverse Proxy

|                  | Forward Proxy                  | Reverse Proxy                       |
| ---------------- | ------------------------------ | ----------------------------------- |
| Represents       | **Clients**                    | **Servers**                         |
| Traffic          | Outbound                       | Inbound                             |
| Sits in front of | Clients                        | Backend servers                     |
| Main purpose     | Control client traffic         | Protect/manage backend traffic      |
| Common uses      | Filtering, logging, IP masking | LB, routing, TLS, caching, security |

### Easy Memory Trick

```text
Forward Proxy
→ Client → Proxy → Server

Reverse Proxy
→ Client → Proxy → Servers
```

**Forward = represents the client**

**Reverse = represents the server**

---

# 4. Reverse Proxy vs Load Balancer

These concepts overlap.

### Reverse Proxy

A reverse proxy **forwards and manages requests** between clients and backends.

### Load Balancer

A load balancer's primary job is to **distribute traffic across healthy backend instances**.

```text
Client
   │
   ▼
Reverse Proxy / Load Balancer
   │
   ├──→ Server 1
   ├──→ Server 2
   └──→ Server 3
```

A product can perform **both roles**.

---

# 5. Reverse Proxy vs API Gateway

An **API Gateway** is essentially a specialized reverse proxy for APIs.

```text
Client
  │
  ▼
API Gateway
  │
  ├──→ User Service
  ├──→ Order Service
  └──→ Payment Service
```

API Gateway commonly adds:

* Authentication
* Rate limiting
* API routing
* Request/response transformation
* API versioning
* Logging and monitoring

So:

```text
Reverse Proxy
    ↓
General traffic management

API Gateway
    ↓
API-specific traffic management
```

---

# 6. Layer 4 vs Layer 7 Proxy

### Layer 4

Works mainly with:

* TCP/UDP
* IP addresses
* Ports
* Connections

It does not need to understand HTTP details.

### Layer 7

Understands application protocols such as HTTP.

Can route based on:

* Host
* URL path
* HTTP method
* Headers
* Cookies

Example:

```text
/api/users/*   → User Service
/api/orders/*  → Order Service
```

---

# 7. Important Failure

A reverse proxy can become a **Single Point of Failure** if there is only one.

```text
Client
  │
  ▼
Proxy ❌
  │
  X
Backend servers are healthy,
but clients cannot reach them.
```

Therefore production systems usually use redundant/managed proxy instances.

---

# Mental Model

```text
Forward Proxy
→ Represents CLIENTS
→ Controls outbound traffic

Reverse Proxy
→ Represents SERVERS
→ Controls inbound traffic

Load Balancer
→ Distributes traffic across backend instances

API Gateway
→ Reverse proxy + API-specific capabilities

L4 Proxy
→ TCP/UDP level

L7 Proxy
→ HTTP/application level
```

> **Most important:** Don't memorize product names. Understand **which side the proxy represents and what decisions it makes**.

```

For your current fundamentals sequence, **this is enough**. Skip the detailed NGINX configuration, VPN comparison, TLS inspection, forwarded-header edge cases, cache semantics, and proxy failure taxonomy for now.
```
