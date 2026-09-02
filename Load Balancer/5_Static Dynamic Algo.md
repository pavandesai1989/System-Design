

````markdown
## Static vs. Dynamic Load-Balancing Algorithms

Load-balancing algorithms can be classified based on whether they consider the **current state of backend servers**.

### 1. Static Algorithms

Static algorithms make routing decisions using a **fixed rule**.

They do **not consider the current load or state** of the servers.

### Example: Round Robin

```text
              Load Balancer
                    │
        ┌───────────┼───────────┐
        ↓           ↓           ↓
       S1          S2          S3
        ↑                       │
        └───────────────────────┘

Request 1 → S1
Request 2 → S2
Request 3 → S3
Request 4 → S1
Request 5 → S2
Request 6 → S3
````

The LB simply follows a predefined sequence:

```text
S1 → S2 → S3 → S1 → S2 → S3 → ...
```

### Characteristics

* Simple
* Low overhead
* Easy to implement
* Does not consider real-time server load
* Does not automatically choose the least-loaded server

### Common Examples

* Round Robin
* Weighted Round Robin

> **Static = Follow a fixed rule.**

---

### 2. Dynamic Algorithms

Dynamic algorithms consider the **current state of backend servers** when making routing decisions.

The LB can consider information such as:

* Active connections
* Server load
* Response time
* Server health

### Example: Least Connections

```text
              Load Balancer
                    │
        ┌───────────┼───────────┐
        ↓           ↓           ↓
       S1          S2          S3
      10 conn      3 conn      7 conn
                    ↑
              Selected
```

The LB sends the new request to **S2** because it currently has the fewest active connections.

```text
Request → S2
```

### Characteristics

* Considers current server state
* Better for uneven or changing workloads
* Can avoid overloaded servers
* Requires monitoring and additional overhead
* More complex than static algorithms

### Common Examples

* Least Connections
* Least Response Time
* Weighted Least Connections

> **Dynamic = Look at the current state before deciding.**

---

## Static vs. Dynamic — Quick Comparison

|                         | Static                        | Dynamic                      |
| ----------------------- | ----------------------------- | ---------------------------- |
| Decision based on       | Fixed rule                    | Current server state         |
| Server load considered? | ❌ No                          | ✅ Yes                        |
| Complexity              | Low                           | Higher                       |
| Overhead                | Low                           | Higher                       |
| Example                 | Round Robin                   | Least Connections            |
| Best for                | Simple, predictable workloads | Changing or uneven workloads |

---

## ⭐ Easy Way to Remember

```text
STATIC
   │
   └── "Follow the rule"
            ↓
       Round Robin

DYNAMIC
   │
   └── "Check the servers"
            ↓
       Least Connections
```

> **Static → Fixed decision**
> **Dynamic → State-based decision**

```
```

[1]: https://www.educative.io/courses/grokking-the-system-design-interview/advanced-details-of-load-balancers "Advanced Details of Load Balancers"
