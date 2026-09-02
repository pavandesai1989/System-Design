
# Availability

> **Availability = the percentage of time a system is operational and accessible to users.**

A highly available system is designed to **continue operating even when some components fail**.

---

## Measuring Availability

```text
Availability =
Uptime / Total Time × 100
````

Common availability levels:

| Availability | Approx. Downtime / Year |
| ------------ | ----------------------: |
| **99.9%**    |              8.76 hours |
| **99.99%**   |            52.6 minutes |
| **99.999%**  |            5.26 minutes |

Each additional **9** means significantly less allowed downtime.

---

# How to Achieve High Availability

The key idea is **redundancy**:

> **Don't depend on a single component.**

Instead of:

```text
User
  ↓
Server 1 ❌
  ↓
System Down
```

Use:

```text
              Load Balancer
             /             \
            ↓               ↓
        Server 1         Server 2
            ❌               ✅
                            ↓
                           User
```

If Server 1 fails, Server 2 can continue serving requests.

---

## Important Techniques

### 1. Redundancy

Use multiple servers or components instead of relying on one.

### 2. Load Balancer

Distributes traffic across healthy servers.

### 3. Health Checks

Detect failed or unhealthy servers and stop sending traffic to them.

### 4. Database Replication

Maintain multiple copies of data so a database failure does not necessarily make the system unavailable.

### 5. Failover

A backup component takes over when the primary fails.

### 6. Multiple Availability Zones

Deploy components across multiple AZs to protect against failure of an entire AZ.

### 7. Eliminate SPOFs

Avoid having a single critical component whose failure brings down the entire system.

---

# Active-Active vs Active-Passive

## Active-Active

All instances actively handle traffic.

```text
             Load Balancer
            /      |      \
           ↓       ↓       ↓
       Server 1 Server 2 Server 3
          ✅        ✅        ✅
```

If one server fails:

```text
             Load Balancer
            /      |      \
           ↓       ↓       X
       Server 1 Server 2 Server 3
          ✅        ✅        ❌
```

The remaining servers continue serving traffic.

---

## Active-Passive

One instance is active while another waits as a backup.

```text
Primary  → Active  ✅
Backup   → Standby
```

If the primary fails:

```text
Primary  → Failed  ❌
Backup   → Active  ✅
```

The backup takes over.

---

# Availability vs Scalability

> **Scalability = Can the system handle more load?**

> **Availability = Can the system keep working when components fail?**

Example:

```text
Scalability
    ↓
Add more servers
    ↓
Handle more traffic


Availability
    ↓
Add redundant servers
    ↓
Continue working when one fails
```

---

## ⭐ Most Important Interview Point

> **High availability is achieved through redundancy, failover, and eliminating Single Points of Failure (SPOF).**

### Remember

```text
Availability
      ↓
Redundancy
      +
Failover
      +
No SPOF
```

**That's enough for the fundamentals.**

Move next to **Reliability** and later connect:

```text
Scalability  → Handle more load
Availability  → Keep serving during failures
Reliability   → Work correctly and consistently
Fault Tolerance → Continue operating despite failures
```

```

One important distinction to keep in mind for the next section: **availability and reliability are related but not the same**. A system can be highly available yet still return incorrect results, which is why reliability deserves its own section.
```
