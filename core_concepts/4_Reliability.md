
# Reliability

> **Reliability = the ability of a system to perform its intended function correctly and consistently over time.**

---

## Availability vs Reliability

> **Availability → Is the system working/responding?**

> **Reliability → Is the system doing the right thing correctly?**

Example:

```text
Payment system responds
        ↓
Available ✅

Payment system charges customer twice
        ↓
Not reliable ❌
````

So:

> **A system can be available but not reliable.**

---

# Measuring Reliability

For now, understand these **3 metrics**:

### 1. MTBF — Mean Time Between Failures

> **How often does the system fail?**

Higher MTBF → fewer failures.

### 2. MTTR — Mean Time To Recovery

> **How quickly can we recover after a failure?**

Lower MTTR → faster recovery.

### 3. Error Rate

> **Percentage of requests that fail.**

```text
Error Rate =
Failed Requests / Total Requests × 100
```

You **don't need to memorize MTBF/MTTR formulas yet**.

---

# How to Build a Reliable System

Remember these **5 important techniques**:

## 1. Redundancy

Have multiple components instead of relying on one.

```text
Server 1 ❌
Server 2 ✅
Server 3 ✅
```

If one fails, others can continue serving requests.

---

## 2. Replication

Keep multiple copies of important data.

```text
Primary DB
    ↓
Replica DB
```

If the primary fails, a replica can potentially be used.

---

## 3. Failover

Automatically switch to a backup when the primary fails.

```text
Primary ❌
    ↓
Backup  ✅
```

---

## 4. Graceful Degradation

If an **optional component fails**, keep the core functionality working.

Example:

```text
Recommendations ❌
       ↓
Shopping / Checkout ✅
```

The user may lose recommendations but can still complete the purchase.

---

## 5. Circuit Breaker

If another service is repeatedly failing, **stop calling it temporarily** instead of allowing the failure to spread.

```text
Service A
    ↓
Service B ❌
    ↓
Circuit Breaker
    ↓
Stop repeated calls
```

This prevents repeated failures from cascading through the system.

---

# Important: Idempotency

Idempotency is especially important for **payments and other operations that may be retried**.

Without idempotency:

```text
Request 1 → Pay ₹10,000
Request 2 → Pay ₹10,000
             ↓
        Customer charged twice ❌
```

> **Idempotency ensures that repeating the same request does not produce an unintended duplicate effect.**

You'll study this properly later under **API Design**.

---

# Reliability vs Availability

|               | **Availability**       | **Reliability**                |
| ------------- | ---------------------- | ------------------------------ |
| Main question | Is it working?         | Is it working correctly?       |
| Focus         | Accessibility / uptime | Correctness + consistency      |
| Example       | Server responds        | Payment is processed correctly |

---

## ⭐ Interview Point

> **Availability means the system is accessible; reliability means the system performs its intended function correctly and consistently.**

---

## Reliability Techniques

```text
Reliability
    │
    ├── Redundancy
    │
    ├── Replication
    │
    ├── Failover
    │
    ├── Graceful Degradation
    │
    └── Circuit Breaker

Additional API technique:
    └── Idempotency
```

### Remember

```text
Availability
    → "Can I access the system?"

Reliability
    → "Does the system do the right thing?"
```

**That's enough for the fundamentals.**

You can move on. Idempotency, retries, timeouts, circuit breakers, replication, etc. can be explored in depth when you reach those specific system-design topics.

````

This gives you a clean progression:

```text
Scalability
    ↓
Availability
    ↓
Reliability
    ↓
Fault Tolerance
````

And you don't need to turn Reliability into a huge networking/distributed-systems chapter at this stage.
