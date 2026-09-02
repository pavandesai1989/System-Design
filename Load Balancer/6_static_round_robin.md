

````markdown
### Round Robin and Weighted Round Robin

## 1. Round Robin

**Round Robin** is one of the simplest load-balancing algorithms.

The Load Balancer distributes requests to servers **sequentially**, one after another.

After reaching the last server, it starts again from the first server.

### Example

Servers:

```text
A, B, C
````

Requests are distributed as:

```text
Request 1 → Server A
Request 2 → Server B
Request 3 → Server C
Request 4 → Server A
Request 5 → Server B
Request 6 → Server C
...
```

The pattern is:

```text
A → B → C → A → B → C → ...
```

If there are 3 servers, each server receives approximately **1/3 of the requests**.

### When Round Robin Works Well

Round Robin works well when:

* Servers have **similar hardware capacity**.
* Requests require **roughly similar processing time**.
* Servers are expected to handle a similar amount of traffic.
* Session affinity is not required.

---

## 2. Problem with Round Robin

Round Robin assumes that all servers can handle approximately the same amount of traffic.

But in a real system, servers may have different capacities.

For example:

```text
Server A → 16 vCPUs, 64 GB RAM   (Powerful)
Server B →  4 vCPUs, 16 GB RAM   (Weak)
Server C →  4 vCPUs, 16 GB RAM   (Weak)
```

Round Robin still distributes traffic equally:

```text
Server A → 33%
Server B → 33%
Server C → 33%
```

This can cause:

```text
Server A → Underutilized
Server B → Overloaded
Server C → Overloaded
```

### Why?

Server A has much higher capacity, but receives the same amount of traffic as the weaker servers.

> **Problem:** Equal traffic distribution does not always mean equal workload or efficient resource utilization.

---

## 3. Weighted Round Robin

**Weighted Round Robin** solves this problem by assigning a **weight** to each server based on its capacity.

> **Higher weight → More traffic**

### Example

Suppose:

```text
Server A → Weight 4
Server B → Weight 1
Server C → Weight 1
```

The total weight is:

```text
4 + 1 + 1 = 6
```

Therefore:

```text
Server A → 4/6 ≈ 67%
Server B → 1/6 ≈ 17%
Server C → 1/6 ≈ 17%
```

### Request Distribution

```text
Request 1 → A
Request 2 → A
Request 3 → A
Request 4 → A
Request 5 → B
Request 6 → C

Request 7 → A
Request 8 → A
Request 9 → A
Request 10 → A
Request 11 → B
Request 12 → C
...
```

The pattern is approximately:

```text
A → A → A → A → B → C → A → A → A → A → B → C → ...
```

So Server A receives approximately **67% of the traffic**, while B and C each receive approximately **17%**.

### Why It Works

The weights reflect the relative capacity of the servers:

```text
Server A → 4x capacity → Weight 4
Server B → 1x capacity → Weight 1
Server C → 1x capacity → Weight 1
```

Therefore, the more powerful server receives more traffic.

---

## Round Robin vs Weighted Round Robin

|                                           | Round Robin      | Weighted Round Robin   |
| ----------------------------------------- | ---------------- | ---------------------- |
| Distribution                              | Equal            | Proportional to weight |
| Server capacity considered?               | ❌ No             | ✅ Yes                  |
| Suitable for identical servers?           | ✅ Yes            | ✅ Yes                  |
| Suitable for different server capacities? | ⚠️ Less suitable | ✅ Better               |
| Complexity                                | Simple           | Slightly more complex  |
| Example                                   | A → B → C        | A → A → A → A → B → C  |

---

## ⭐ Easy Way to Remember

```text
ROUND ROBIN
    ↓
Equal traffic
    ↓
A → B → C → A → B → C

WEIGHTED ROUND ROBIN
    ↓
Traffic based on capacity
    ↓
A → A → A → A → B → C
```

> **Round Robin = Everyone gets an equal share.**

> **Weighted Round Robin = More capable servers get a larger share.**

```
```
