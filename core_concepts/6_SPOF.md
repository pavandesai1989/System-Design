
# Single Point of Failure (SPOF)

> **A Single Point of Failure (SPOF) is a component or dependency whose failure can take down the system or a critical user flow because there is no working alternative.**

A SPOF does not have to be a server.

Examples:
- Load Balancer
- Database
- DNS provider
- Message broker
- Cache
- Network component
- Configuration service
- Secrets manager

---

# What Makes Something a SPOF?

A component is a SPOF when:

1. A **critical path depends on it**.
2. There is **no working alternative** if it fails.
3. Its failure causes **unacceptable impact**.

### Example

```text
              Load Balancer
                  ❌
                   ↓
             Cannot reach
             application
                   ↓
              System Down
````

Even if the application servers are healthy, the **load balancer is a SPOF** because users have no alternative path to reach them.

---

# Example: Removing a SPOF

### Before

```text
Clients
   |
   v
Load Balancer ❌  ← SPOF
   |
   +--------+--------+
   |                 |
App Server 1     App Server 2
```

If the load balancer fails:

```text
Clients
   X
   |
Load Balancer ❌
   |
App Servers
   |
  DOWN
```

### After

Use redundant load balancers:

```text
             Clients
                |
          +-----+-----+
          |           |
          v           v
        LB 1        LB 2
          |           |
          +-----+-----+
                |
          +-----+-----+
          |           |
          v           v
       Server 1    Server 2
```

Now, if one load balancer fails, the other can continue routing traffic.

---

# Common SPOFs

| Area              | Example SPOF                    | Failure Impact                    |
| ----------------- | ------------------------------- | --------------------------------- |
| **Traffic Entry** | One Load Balancer               | Users cannot reach the service    |
| **Compute**       | One application server          | Requests fail                     |
| **Data**          | One database without failover   | Reads/writes may stop             |
| **Cache**         | One cache with no fallback      | May cause database overload       |
| **Messaging**     | One broker/queue                | Producers or consumers may stop   |
| **Network**       | One firewall/NAT gateway        | Connectivity may be lost          |
| **Configuration** | One config service              | Services may fail to start/update |
| **Storage**       | One storage system              | Data/files may become unavailable |
| **Operations**    | One deployment/recovery process | Recovery may be blocked           |

---

# SPOF vs Redundancy

```text
SPOF
  ↓
One critical component
  ↓
Failure → System/critical flow affected


Redundancy
  ↓
Multiple components
  ↓
One fails → Another can continue
```

### Example

```text
Single Database
       ↓
    Failure
       ↓
  System Down


Primary DB
     |
     +---- Replica
     |
Primary fails
     ↓
Failover
     ↓
Replica takes over
```

---

# Important: Redundancy Must Be Real

Having multiple components does **not automatically eliminate a SPOF**.

Example:

```text
        Load Balancer
             |
       +-----+-----+
       |           |
    Server 1    Server 2
```

The application servers are redundant.

But if both depend on:

```text
       One Database
            ❌
```

the database is still a SPOF.

Similarly, if both servers depend on the same network component:

```text
Server 1 ──┐
           ├── One Firewall ❌
Server 2 ──┘
```

the firewall becomes a SPOF.

> **Always look for shared dependencies, not just duplicate servers.**

---

# Cascading Failure Example

A component does not always have to fail directly to cause an outage.

```text
Cache ❌
   ↓
More requests reach Database
   ↓
Database overloaded
   ↓
Database ❌
   ↓
Application unavailable
```

The cache may not be a direct SPOF, but its failure can trigger a **cascading failure**.

---

# SPOF Analysis

When reviewing an architecture, ask:

```text
"If this component fails,
what happens to the system?"
```

Then ask:

```text
"Is there another path or component
that can continue serving the request?"
```

If the answer is **No**, you may have found a SPOF.

---

## ⭐ Interview Tip

> **The goal is not to eliminate every possible SPOF. The goal is to identify critical SPOFs, reduce their impact, and provide redundancy or failover where required.**

### Remember

```text
SPOF
→ One critical dependency
→ No alternative
→ Failure causes unacceptable impact
```

> **Redundancy + Failover = Reduced SPOF risk**

```

This is **enough for your fundamentals**. The deeper points about control planes, deployment pipelines, human experts, AI/ML dependencies, etc. are useful later but would make this section unnecessarily heavy.
```
