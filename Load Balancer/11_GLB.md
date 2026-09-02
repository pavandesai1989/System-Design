

````markdown id="74126"
## Global Server Load Balancing (GSLB)

GSLB distributes user traffic across **data centers in different geographic regions**.

A regional Load Balancer decides:

```text
Which server?
````

GSLB decides:

```text
Which region / data center?
```

### How GSLB Works

```text
                    GSLB / DNS
                   /     |      \
                  ▼      ▼       ▼
              Mumbai   London   Virginia
                DC       DC        DC
```

Example:

```text
Mumbai user   → Mumbai DC
London user   → London DC
US user       → US DC
```

The goal is to route users to an appropriate/nearby healthy data center, reducing latency.

### Common Routing Policies

| Policy        | How it routes                                |
| ------------- | -------------------------------------------- |
| Geographic    | Routes based on user location                |
| Latency-based | Routes to the region with lower latency      |
| Weighted      | Sends a percentage of traffic to each region |
| Failover      | Uses a backup region when the primary fails  |

### GSLB Failover

If a data center becomes unavailable:

```text
Normal:

User → GSLB → Mumbai DC
                ✓


Mumbai DC fails:

User → GSLB → Singapore DC
                ✓
```

GSLB detects the failed region and routes new users to another healthy region.

### Key Idea

> **GSLB operates at the global level and decides which data center should handle the user.**

```text
GSLB
  ↓
Which REGION?

Regional Load Balancer
  ↓
Which SERVER?
```

```

**This is the important distinction to remember:**  
**GSLB → region** | **Regional LB → server**.
```
