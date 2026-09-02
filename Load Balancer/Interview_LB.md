

````markdown
# Load Balancing — Interview Questions & Answers

## 1. Why do we need a Load Balancer?

A Load Balancer distributes incoming traffic across multiple backend servers.

It provides:

- Scalability
- High availability
- Fault tolerance
- Traffic distribution
- Server health monitoring

Without a Load Balancer, one server can become overloaded or a single point of failure.

> **Key idea:** Load Balancer = Distribute traffic + Improve availability.

---

## 2. How does a Load Balancer work?

```text
Client
   │
   ▼
Load Balancer
   │
   ├──→ Server 1
   ├──→ Server 2
   └──→ Server 3
````

The Load Balancer:

1. Receives the client request.
2. Checks which backend servers are healthy.
3. Selects a server using a load-balancing algorithm.
4. Forwards the request.
5. Receives the response.
6. Returns the response to the client.

---

## 3. What happens if one backend server goes down?

The Load Balancer detects the failure using health checks.

```text
Before:

LB
├── S1 ✓
├── S2 ✓
└── S3 ✓

S2 fails

After:

LB
├── S1 ✓
├── S2 ✗  ← removed
└── S3 ✓
```

The LB stops sending new requests to S2.

Clients do not need to know that S2 failed.

---

## 4. What is Round Robin?

Round Robin distributes requests sequentially across servers.

```text
Request 1 → S1
Request 2 → S2
Request 3 → S3
Request 4 → S1
Request 5 → S2
```

Pattern:

```text
S1 → S2 → S3 → S1 → S2 → S3
```

It works well when servers have similar capacity and requests have similar processing requirements.

---

## 5. What is Weighted Round Robin?

Weighted Round Robin assigns different weights to servers based on their capacity.

Example:

```text
S1 → Weight 4
S2 → Weight 1
S3 → Weight 1
```

Approximately:

```text
S1 → 67%
S2 → 17%
S3 → 17%
```

A more powerful server therefore receives more traffic.

> **Round Robin = Equal distribution**
>
> **Weighted Round Robin = Distribution based on capacity**

---

## 6. What is Least Connections?

Least Connections sends a new request to the server with the fewest active connections.

```text
S1 → 10 connections
S2 →  3 connections  ← selected
S3 →  7 connections
```

It is useful when requests have different processing times.

Unlike Round Robin, it considers the **current state of the servers**.

---

## 7. What is Least Response Time?

Least Response Time routes traffic toward the server responding fastest.

```text
S1 → 120 ms
S2 →  40 ms  ← selected
S3 →  80 ms
```

It is useful when backend servers have different response times.

> **Least Connections → Least busy**
>
> **Least Response Time → Fastest responding**

---

## 8. Static vs Dynamic Load-Balancing Algorithms?

### Static

Uses a predefined rule without considering current server state.

Examples:

* Round Robin
* Weighted Round Robin

### Dynamic

Considers the current state of backend servers.

Examples:

* Least Connections
* Least Response Time

```text
Static
   ↓
Fixed rule

Dynamic
   ↓
Current server state
```

---

## 9. Layer 4 vs Layer 7 Load Balancer?

### Layer 4

Works at the Transport Layer.

Uses:

* IP address
* Port
* TCP/UDP
* Connection information

It does not understand HTTP-level information.

### Layer 7

Works at the Application Layer.

Can understand:

* HTTP method
* URL path
* Headers
* Cookies
* Query parameters

Example:

```text
/api/users  → User Service
/api/orders → Order Service
```

> **L4 → Connection-level routing**
>
> **L7 → Application-level routing**

---

## 10. Why is Layer 7 more intelligent than Layer 4?

Because Layer 7 understands application-level information.

For example:

```http
GET /api/users/123
```

An L7 Load Balancer can inspect `/api/users/123` and route it to the User Service.

An L4 Load Balancer only sees connection-level information such as:

```text
TCP
Destination Port: 443
```

It does not understand the URL.

---

## 11. What is a Health Check?

A health check is a periodic probe sent by the Load Balancer to determine whether a backend server can handle requests.

Example:

```text
LB → GET /health → Server
                  ↓
                200 OK
```

If the server repeatedly fails health checks, the LB removes it from the traffic pool.

---

## 12. What is a Shallow vs Deep Health Check?

### Shallow

Checks whether the application/process is running.

```text
GET /health
     ↓
  200 OK
```

### Deep

Checks whether important dependencies are working.

```text
Application
 ├── Database ✓
 ├── Cache ✓
 └── Disk ✓
```

> **Shallow = Is the process running?**
>
> **Deep = Can the application actually serve requests?**

---

## 13. Can a Load Balancer itself be a Single Point of Failure?

Yes.

If there is only one Load Balancer:

```text
Client
   │
   X
   │
  LB  ← failed
   │
Servers
```

All backend servers may be healthy, but clients cannot reach them.

### Solution

Use multiple redundant Load Balancers.

```text
             ┌──→ LB 1
Client ──────┤
             └──→ LB 2
                   │
                   ▼
                Servers
```

Common approaches:

* Active-Passive
* Active-Active

> **The Load Balancer itself must not become a Single Point of Failure.**

---

## 14. Active-Passive vs Active-Active?

### Active-Passive

```text
Client → Active LB → Servers
              │
          Passive LB
             standby
```

Only the active LB handles traffic.

If it fails, the passive LB takes over.

### Active-Active

```text
             ┌──→ LB 1 → Servers
Client ──────┤
             └──→ LB 2 → Servers
```

Both LBs handle traffic.

If one fails, the other continues handling traffic.

> **Active-Passive → One active, one standby**
>
> **Active-Active → Both handle traffic**

---

## 15. What is GSLB?

GSLB (Global Load Balancing) distributes users across data centers in different geographic regions.

```text
                 GSLB
              /    |    \
             ↓     ↓     ↓
          Mumbai London  US
             DC      DC    DC
```

It answers:

> **Which region/data center should handle this user?**

A regional Load Balancer then answers:

> **Which backend server should handle the request?**

```text
GSLB
  ↓
Which Region?
  ↓
Regional LB
  ↓
Which Server?
```

---

## 16. Why use GSLB?

For globally distributed applications, routing users to an appropriate nearby/healthy region can:

* Reduce latency
* Improve availability
* Provide disaster recovery
* Support multi-region deployments

Example:

```text
Mumbai User  → Mumbai DC
London User  → London DC
US User      → US DC
```

---

## 17. What happens when an entire data center goes down?

GSLB can detect the failed region and route users to another healthy region.

```text
Normal:

User → GSLB → Mumbai DC


Mumbai DC fails:

User → GSLB → Singapore DC
```

This provides **multi-region failover**.

---

## 18. Why are stateless servers preferred behind a Load Balancer?

If servers are stateless, any server can handle any request.

```text
Request 1 → S1
Request 2 → S2
Request 3 → S3
```

The system does not depend on a particular server storing the user's session.

This makes:

* Load balancing easier
* Horizontal scaling easier
* Server failure easier to handle

If session state is stored locally on a server, sticky sessions or external session storage may be required.

---

## 19. What are Sticky Sessions?

Sticky sessions ensure that requests from the same client/session are routed to the same backend server.

```text
User A
  │
  ▼
 LB
  │
  └──→ Server 1

User A's future requests
  │
  └──→ Server 1
```

This is useful when session state is stored locally on the backend.

However, it can make load distribution less flexible.

A better approach is often to store session state in a shared/external store such as Redis.

---

## 20. Does a Load Balancer terminate TLS?

It can.

```text
Client
  │ HTTPS
  ▼
Load Balancer
  │ HTTP/HTTPS
  ▼
Backend
```

The LB can terminate the client's TLS connection and then establish a new connection to the backend.

Benefits include:

* Centralized certificate management
* Reduced TLS processing on backend servers
* Simpler backend configuration

TLS can also be maintained between the LB and backend when required.

---

## 21. What is the difference between a Reverse Proxy and a Load Balancer?

A Load Balancer primarily distributes traffic across multiple backend servers.

A Reverse Proxy sits in front of backend servers and can provide functions such as:

* Routing
* TLS termination
* Caching
* Compression
* Request filtering

A reverse proxy can also perform load balancing.

For example, Nginx can act as both a **Reverse Proxy and Load Balancer**.

> **Load Balancer = Traffic distribution**
>
> **Reverse Proxy = Gateway between clients and backend servers**

---

## 22. How do you scale a Load Balancer?

A Load Balancer can itself become a bottleneck.

Solutions include:

```text
                    ┌──→ LB 1 ──→ Servers
Clients → DNS/LB ───┤
                    └──→ LB 2 ──→ Servers
```

Use:

* Multiple Load Balancers
* Active-Active architecture
* Horizontal scaling
* Managed cloud Load Balancers

The goal is to ensure the LB layer has enough capacity and no single point of failure.

---

## 23. Which Load-Balancing algorithm would you choose?

It depends on the workload.

```text
Similar servers + similar requests
        ↓
   Round Robin


Different server capacities
        ↓
Weighted Round Robin


Requests have different processing times
        ↓
Least Connections


Server response times vary
        ↓
Least Response Time
```

There is no single best algorithm for every system.

---

## 24. What happens when a new server is added?

The new server is registered with the Load Balancer.

```text
Before:

LB
├── S1
├── S2
└── S3


After:

LB
├── S1
├── S2
├── S3
└── S4  ← new server
```

The Load Balancer can then start sending traffic to S4.

Clients continue using the same Load Balancer endpoint.

This is one of the main benefits of load balancing for horizontal scaling.

---

## 25. What happens when a server is removed?

The Load Balancer stops sending new requests to the server.

```text
LB
├── S1 ✓
├── S2 ✓
└── S3 → removed
```

In production systems, the server is often **drained** first:

```text
Stop new traffic
       ↓
Allow existing requests/connections to finish
       ↓
Remove server
```

This prevents active requests from being abruptly terminated.

---

# Most Important Questions to Remember

For an interview, prioritize these:

```text
⭐⭐⭐ MUST KNOW

1. Why do we need a Load Balancer?
2. How does a Load Balancer work?
3. Round Robin vs Weighted Round Robin
4. Least Connections
5. Layer 4 vs Layer 7
6. Health Checks
7. What happens when a server fails?
8. How do you prevent the LB from becoming a SPOF?
9. Active-Passive vs Active-Active
10. GSLB and multi-region routing

⭐⭐ IMPORTANT

11. Stateless servers and Load Balancing
12. Sticky Sessions
13. TLS termination
14. Least Response Time
15. Reverse Proxy vs Load Balancer
16. How to scale the Load Balancer
17. What happens when a server is added/removed
```

## Final Mental Model

```text
                         GLOBAL
                           │
                           ▼
                         GSLB
                    "Which region?"
                           │
                           ▼
                  REGIONAL LOAD BALANCER
                    "Which server?"
                           │
              ┌────────────┼────────────┐
              ▼            ▼            ▼
             S1           S2           S3
             ✓            ✓            ✗
                                      Health Check
```

> **Load Balancer = Distribute traffic + Detect failures + Keep the system available.**

```

I’d consider this **more than enough for Load Balancing interview preparation**. The ⭐⭐⭐ questions are the ones I’d actually rehearse verbally.
```
