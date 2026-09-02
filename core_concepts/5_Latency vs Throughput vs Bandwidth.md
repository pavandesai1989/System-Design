
# Latency vs Throughput vs Bandwidth

These three concepts describe different aspects of system performance.

---

## Latency

> **Latency = how long it takes for an operation or request to complete.**

Usually measured in **milliseconds (ms)**.

```text
User → Server
       100 ms
````

Lower latency → faster response.

### Example

If a request takes 200 ms to receive a response:

```text
Latency = 200 ms
```

---

## Throughput

> **Throughput = how much work a system completes per unit of time.**

Examples:

```text
10,000 requests/sec
5,000 transactions/sec
1 GB/sec
```

Higher throughput → more work processed per unit of time.

### Example

```text
Server
  ↓
Processes 10,000 requests/sec

Throughput = 10,000 requests/sec
```

---

## Bandwidth

> **Bandwidth = maximum amount of data that can be transferred per unit of time.**

Usually measured in:

```text
Mbps
Gbps
```

Example:

```text
Network bandwidth = 1 Gbps
```

Think of bandwidth as the **capacity of the network pipe**.

---

# Simple Highway Analogy

```text
Bandwidth
→ Number of lanes on the highway

Throughput
→ Number of cars actually passing per second

Latency
→ Time taken by one car to travel from A → B
```

---

# Latency vs Throughput vs Bandwidth

|                | **Meaning**                     | **Example** |
| -------------- | ------------------------------- | ----------- |
| **Latency**    | Time taken for an operation     | 100 ms      |
| **Throughput** | Work completed per unit of time | 10,000 RPS  |
| **Bandwidth**  | Maximum data-transfer capacity  | 1 Gbps      |

---

## Important Relationship

A system can have:

```text
High bandwidth
      +
Low throughput
```

For example, the network may have plenty of capacity, but the **application server or database may be the bottleneck**.

So:

> **Bandwidth is available capacity; throughput is the work actually being processed.**

---

# How to Improve Them

## Reduce Latency

Common approaches:

* **Caching** → avoid repeated expensive operations.
* **CDN** → serve content closer to users.
* **Fewer network calls** → reduce network round trips.
* **Servers closer to users** → reduce network distance.
* **Database optimization** → reduce query time.

---

## Increase Throughput

Common approaches:

* **Horizontal scaling** → add more servers.
* **Load balancing** → distribute work across servers.
* **Async processing** → move non-critical work to background processing.
* **Batching** → process multiple operations together.
* **Caching** → reduce repeated work.

---

## Increase Bandwidth

> **Increase the network's data-transfer capacity.**

For example:

```text
1 Gbps
   ↓
10 Gbps
```

This can be achieved by upgrading network capacity or infrastructure.

---

## ⭐ Remember

```text
Latency
→ How long does one operation take?

Throughput
→ How much work can we process per second?

Bandwidth
→ How much data can the network carry per second?
```

### Interview Tip

Don't confuse **throughput** with **bandwidth**:

> **Bandwidth = capacity**

> **Throughput = actual work/data processed**

```

**This is enough.** I would not add Little's Law, queueing theory, p50/p95/p99, or detailed network calculations here. Those are useful later when you reach **performance and capacity planning**.
```
