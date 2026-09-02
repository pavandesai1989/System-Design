

````markdown
## Dynamic Load-Balancing Algorithms

Dynamic algorithms make routing decisions based on the **current state of backend servers**.

Unlike static algorithms such as Round Robin, they can consider factors such as:

- Number of active connections
- Server response time
- Current server load
- Server health

### 1. Least Connections

The Load Balancer sends the new request to the server with the **fewest active connections**.

Example:

```text
                    Active Connections

Client ──→ LB ──→  S1  ── 10 connections
             │
             ├──→  S2  ──  3 connections  ← SELECT
             │
             └──→  S3  ──  7 connections
````

Since **S2 has the fewest active connections**, the LB sends the new request to S2.

#### Example

```text
Before:

S1 → 10 connections
S2 →  3 connections
S3 →  7 connections

New Request
     │
     ▼
    LB
     │
     └────→ S2

After:

S1 → 10
S2 →  4
S3 →  7
```

#### Why use it?

Round Robin distributes requests equally:

```text
S1 → S2 → S3 → S1 → S2 → S3
```

But servers may have different workloads.

Least Connections instead asks:

```text
"Which server is currently handling the fewest connections?"
```

This can provide better distribution when requests take different amounts of time.

#### Advantages

* Considers current server state
* Helps avoid overloaded servers
* Works well when requests have different processing times

#### Disadvantage

* LB needs to track the current number of connections
* More overhead than simple Round Robin

### 2. Least Response Time

The Load Balancer sends the request to the server that has the **lowest response time**, usually while also considering its current connections or load.

Example:

```text
                    Response Time

Client ──→ LB ──→  S1  ── 120 ms
             │
             ├──→  S2  ──  40 ms  ← SELECT
             │
             └──→  S3  ──  80 ms
```

S2 currently responds fastest, so the LB sends the request to S2.

#### Why use it?

A server may have fewer connections but still be slow.

Example:

```text
S1 → 2 connections → 200 ms response time
S2 → 5 connections →  40 ms response time
S3 → 7 connections →  80 ms response time
```

Least Connections might choose **S1**.

Least Response Time would prefer **S2** because it is responding faster.

#### Advantages

* Considers actual server performance
* Can route traffic away from slow servers
* Useful when server response times vary

#### Disadvantage

* Requires monitoring response times
* More complex than Round Robin
* Additional monitoring overhead

## Least Connections vs Least Response Time

| Feature                 | Least Connections  | Least Response Time |
| ----------------------- | ------------------ | ------------------- |
| Decision based on       | Active connections | Response time       |
| Considers current state | Yes                | Yes                 |
| Avoids busy servers     | Yes                | Yes                 |
| Detects slow servers    | Indirectly         | Directly            |
| Complexity              | Medium             | Higher              |
| Example                 | Fewest connections | Fastest response    |

## Static vs Dynamic

```text
STATIC
│
├── Round Robin
│   └── Fixed sequence
│
└── Weighted Round Robin
    └── Fixed weights


DYNAMIC
│
├── Least Connections
│   └── Fewest active connections
│
└── Least Response Time
    └── Fastest response
```

### Easy Memory

```text
ROUND ROBIN
→ "Follow the sequence"

WEIGHTED ROUND ROBIN
→ "Follow the weights"

LEAST CONNECTIONS
→ "Which server is least busy?"

LEAST RESPONSE TIME
→ "Which server is responding fastest?"
```

> **Static = Follow a fixed rule.**
>
> **Dynamic = Look at the current server state.**

```
```
