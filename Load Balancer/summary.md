

I would use:

````markdown
## Load Balancing — Section Summary

### Key Concepts

| Concept | Key Idea |
|---|---|
| Load Balancer | Distributes traffic across backend servers |
| Placement | Global → Region → Service |
| Round Robin | Equal distribution |
| Weighted Round Robin | Distribution based on server capacity |
| Least Connections | Routes to the least busy server |
| Least Response Time | Routes to the fastest responding server |
| Layer 4 | TCP/UDP-level routing |
| Layer 7 | HTTP-aware routing |
| Health Checks | Detect unhealthy servers and remove them from rotation |
| Redundancy | Prevents the LB from becoming a single point of failure |
| GSLB | Routes users to an appropriate data center |

### Algorithms

```text
Static
├── Round Robin
└── Weighted Round Robin

Dynamic
├── Least Connections
└── Least Response Time
````

### Load Balancer Hierarchy

```text
Client
   │
   ▼
GSLB
   │
   │ Which region?
   ▼
Regional Load Balancer
   │
   │ Which server?
   ▼
Backend Servers
```

### Key Takeaways

> **Load Balancer = Distribute + Detect Failures**

> **Static algorithms = Fixed rules**

> **Dynamic algorithms = Current server state**

> **L4 = Connection-level routing**

> **L7 = Application-level routing**

> **Redundancy = No single LB failure**

> **GSLB = Choose the region**

```

I would **remove the “Key Numbers to Remember” section entirely**. The exact health-check intervals, failover times, LB costs, and L4/L7 throughput numbers are implementation-dependent and aren't worth memorizing for your system-design notes.
```
