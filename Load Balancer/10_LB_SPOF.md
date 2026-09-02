

I would keep it like this:

````markdown id="58321"
## Load Balancer Redundancy

A Load Balancer can itself become a **Single Point of Failure (SPOF)**.

If the LB fails:

```text
             ❌ Load Balancer
Client ────────X──────── Backend Servers
````

Even if all backend servers are healthy, clients cannot reach them.

### Solution: Multiple Load Balancers

```text
                 ┌──→ LB 1
Client ──────────┤
                 └──→ LB 2
                       │
                       ▼
                 Backend Servers
```

Common approaches:

### 1. Active-Passive

```text
        ┌──→ Active LB ──→ Servers
Client ─┤
        └──→ Passive LB
```

* Active LB handles traffic.
* Passive LB waits as a backup.
* If Active fails, Passive takes over.

### 2. Active-Active

```text
             ┌──→ LB 1 ──→ Servers
Client ──────┤
             └──→ LB 2 ──→ Servers
```

* Both LBs handle traffic.
* If one fails, the other continues handling traffic.
* Better resource utilization and availability.

### Key Idea

> **Never allow the Load Balancer itself to become a Single Point of Failure.**

In cloud environments, managed Load Balancers are typically deployed redundantly across multiple Availability Zones, so this redundancy is handled by the cloud provider.

```

For your notes, **don't include exact failover times, heartbeat intervals, AWS pricing, or specific implementation details**. Those are unnecessary at this stage.
```
