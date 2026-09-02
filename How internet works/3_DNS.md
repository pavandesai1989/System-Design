

````markdown id="d7k3p1"
# DNS (Domain Name System)

DNS stands for **Domain Name System**.

DNS translates a **domain name** into an IP address so that a client can connect to the destination.

```text
Domain Name              IP Address

youtube.com       ───►   142.x.x.x
example.com        ───►   93.x.x.x
````

### Why DNS?

Humans use domain names:

```text
youtube.com
```

Machines communicate using IP addresses:

```text
142.x.x.x
```

DNS connects the two.

### Analogy

> **DNS is like the internet's phone book.**

You know the name, and DNS helps you find the address.

---

# Why Not Use IP Addresses Directly?

| Reason                      | Explanation                                            |
| --------------------------- | ------------------------------------------------------ |
| **Human readability**       | `youtube.com` is easier to remember than an IP address |
| **IP addresses can change** | Infrastructure can change without changing the domain  |
| **Multiple IP addresses**   | A domain can resolve to multiple IP addresses          |
| **Traffic routing**         | DNS can direct users to different regions or endpoints |

---

# Common DNS Record Types

| Record    | Purpose                              |
| --------- | ------------------------------------ |
| **A**     | Maps a domain to an IPv4 address     |
| **AAAA**  | Maps a domain to an IPv6 address     |
| **CNAME** | Maps a domain to another domain      |
| **MX**    | Specifies mail servers               |
| **NS**    | Specifies authoritative name servers |

Example:

```text
example.com
     │
     ├── A     → IPv4 address
     ├── AAAA  → IPv6 address
     └── CNAME → another domain
```

---

# DNS Resolution

When a client needs the IP address for `example.com`, DNS resolution typically involves several levels.

```text
Browser Cache
     │
     ▼
OS DNS Cache
     │
     ▼
Recursive DNS Resolver
     │
     ▼
Root DNS Server
     │
     ▼
.com TLD Server
     │
     ▼
Authoritative DNS Server
     │
     ▼
IP Address
```

### Simplified Flow

1. Browser checks its DNS cache.
2. OS checks its DNS cache.
3. Request goes to a **recursive DNS resolver**.
4. Resolver queries the **Root DNS server** if needed.
5. Root directs it to the appropriate **TLD server** such as `.com`.
6. TLD server directs it to the **authoritative DNS server**.
7. Authoritative server returns the IP address.
8. The result is cached according to its **TTL**.

> In practice, many of these steps are skipped because DNS information is cached.

---

# DNS and TTL

**TTL (Time To Live)** specifies how long a DNS response can be cached.

```text
DNS Response
     │
     ▼
TTL = 300 seconds
     │
     ▼
Cache for up to 5 minutes
```

### TTL Trade-off

**Lower TTL**

* Changes take effect sooner
* More DNS queries

**Higher TTL**

* Fewer DNS queries
* Changes may take longer to take effect

---

# DNS in System Design

DNS becomes important when designing distributed systems.

## 1. Global Traffic Routing

DNS can direct users to different regions or endpoints.

```text
                 example.com
                      │
                DNS / Traffic
                  Routing
                      │
          ┌───────────┼───────────┐
          ▼           ▼           ▼
       India        USA        Europe
       Region       Region      Region
```

The same domain can resolve to different IP addresses depending on the configured routing policy.

Common policies include:

* Geographic routing
* Latency-based routing
* Weighted routing
* Failover routing

---

## 2. Failover

DNS can be used to route traffic away from an unhealthy endpoint.

```text
Normal:

DNS
 │
 ▼
Primary Region ✓


Failure:

DNS
 │
 ▼
Primary Region ✗
 │
 ▼
Backup Region ✓
```

However, DNS-based failover is **not instantaneous** because cached DNS responses may continue to be used until their TTL expires.

For faster failure detection and routing changes, systems commonly use **health checks and load balancers** as additional layers.

---

## 3. CDN Integration

DNS is commonly used to direct users toward a CDN.

```text
Client
   │
   ▼
DNS
   │
   ▼
CDN Edge Server
   │
   │ Cache miss
   ▼
Origin Server
```

The CDN can serve cached content from an edge location closer to the user.

---

# DNS vs Load Balancer

They operate at different levels.

```text
Client
  │
  │ "Where should I connect?"
  ▼
DNS
  │
  │ Returns an IP / endpoint
  ▼
Load Balancer
  │
  │ "Which server should handle this request?"
  ▼
Application Servers
```

**DNS → helps determine where to send traffic**

**Load Balancer → distributes traffic among servers**

### Key Idea

> **DNS translates domain names into IP addresses and can also participate in global traffic routing and failover.**

> **DNS is cached, so DNS-based routing changes are not instantaneous.**

```

### For your interview prep, remember this mental model

**DNS = WHERE?**  
**Load Balancer = WHICH SERVER?**

That distinction will connect very nicely with the Load Balancer notes you already made.
```
