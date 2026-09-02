

````markdown
# Scalability

As an application grows, its **load** grows too:

- More users
- More requests
- More data
- More queries

A design that works for 1,000 users may not work for 1 million users.

---

## What is Scalability?

> **Scalability is the ability of a system to handle increased load by adding resources.**

A scalable system can grow to meet demand **without requiring a complete architectural redesign**.

---

# Measuring Scalability

Before scaling, we need to understand the **load and performance** of the system.

| Metric | What it measures |
|---|---|
| **RPS / QPS** | Requests or queries handled per second |
| **Throughput** | Amount of work or data processed per unit of time |
| **Latency** | How long a request takes to complete |
| **Concurrent users** | Users using the system at the same time |
| **Data volume** | Amount of data stored or processed |

---

## Throughput

> **Throughput = how much work a system can process per unit of time.**

Examples:

```text
1 GB/sec
10,000 transactions/sec
5,000 messages/sec
````

---

## RPS vs Throughput vs Latency

### RPS

> How many requests are handled per second?

### Throughput

> How much work or data is processed per second?

### Latency

> How long does one request take?

### Toll Booth Example

```text
RPS
→ How many cars arrive per second?

Throughput
→ How many cars can the toll system process per second?

Latency
→ How long does one car take to pass?
```

Generally, we want:

> **High throughput + Low latency**

---

# Vertical Scaling — Scale Up

**Vertical scaling means adding more resources to an existing machine.**

```text
Before

┌─────────────────┐
│     Server      │
│                 │
│  4 GB RAM       │
│  2 CPU cores    │
└─────────────────┘

        ↓ Scale Up

┌─────────────────┐
│     Server      │
│                 │
│  16 GB RAM      │
│  8 CPU cores    │
└─────────────────┘
```

You **replace or upgrade the existing server with a more powerful server**.

### Pros

* **Simple** — Usually requires fewer architectural changes.
* **Lower complexity** — No need to coordinate multiple application servers.
* **Potentially lower latency** — Data can remain on the same machine.

### Cons

* **Hardware limits** — A machine cannot be upgraded indefinitely.
* **Single Point of Failure** — If the only server fails, the service can become unavailable.
* **Cost** — Very powerful machines can become expensive.
* **Limited scalability** — Eventually, horizontal scaling may be required.

### Good for

* **Databases** when a single machine is still sufficient.
* **Early-stage applications** where simplicity matters.
* **Moderate workloads** where one machine is sufficient.

---

# Horizontal Scaling — Scale Out

**Horizontal scaling means adding more machines instead of making one machine more powerful.**

```text
                 Load Balancer
                /      |      \
               ↓       ↓       ↓
          ┌───────┐ ┌───────┐ ┌───────┐
          │Server 1│ │Server 2│ │Server 3│
          └───────┘ └───────┘ └───────┘
```

The **load balancer distributes incoming requests** across the servers.

### Pros

* **High scalability** — Add more servers as traffic increases.
* **Fault tolerance** — One server can fail while others continue serving traffic.
* **Load distribution** — Traffic is distributed across multiple servers.
* **Flexible scaling** — Servers can be added or removed based on demand.

### Cons

* **Complexity** — Distributed systems are harder to build and operate.
* **Data consistency** — Multiple machines may need to coordinate data.
* **Network overhead** — Servers communicate over a network.
* **Stateless architecture often preferred** — Any server should ideally be able to handle any request.

---

# Vertical vs Horizontal Scaling

|                 | **Vertical**                | **Horizontal**                |
| --------------- | --------------------------- | ----------------------------- |
| Also called     | Scale Up                    | Scale Out                     |
| Approach        | Bigger machine              | More machines                 |
| Complexity      | Lower                       | Higher                        |
| Scalability     | Limited by machine capacity | Can grow by adding machines   |
| Fault tolerance | Lower with a single machine | Higher with multiple machines |
| Common example  | Database                    | Application servers           |

---

## ⭐ Most Important Thing to Remember

```text
Vertical Scaling
        ↓
Make the machine BIGGER

Horizontal Scaling
        ↓
Add MORE machines
```

> **Vertical scaling = Scale Up**

> **Horizontal scaling = Scale Out**

### Interview Tip

For large distributed systems, **horizontal scaling is usually preferred for application servers** because we can add more servers as demand grows and continue operating even if an individual server fails.

Don't try to memorize every advantage and disadvantage yet.

**Understand:**

> **Scale Up = Bigger machine**

> **Scale Out = More machines**

```

One small conceptual point worth remembering for interviews: **scalability is not the same as performance**. Performance asks *“How well does the system perform?”*; scalability asks *“How well does it continue to perform as load increases?”*
```
