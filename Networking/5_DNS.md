
DNS
├── What DNS does
├── How DNS resolution works
├── DNS components
├── Common record types
├── DNS caching + TTL
├── DNS-based traffic routing
├── DNS vs Load Balancer
└── DNS failure / design considerations
```

Here is the **clean version I'd put in your repository**:

````markdown id="4k7p2m"
# DNS (Domain Name System)

> **DNS translates human-readable domain names into information such as IP addresses that clients can use to connect to services.**

Example:

```text
api.example.com
       ↓
     DNS
       ↓
203.0.113.10
````

Without DNS, users would need to remember IP addresses instead of names.

---

# 1. Why DNS Is Needed

Humans prefer names:

```text
api.example.com
```

Networks use IP addresses:

```text
203.0.113.10
```

DNS provides the mapping:

```text
Domain Name
     ↓
    DNS
     ↓
IP Address
```

DNS is also used for:

* Traffic routing
* Failover
* CDN integration
* Service discovery
* Mail routing

---

# 2. How DNS Resolution Works

When a client wants to access:

```text
https://api.example.com
```

it first needs the IP address.

High-level flow:

```text
Client
   |
   | "What is the IP of api.example.com?"
   ↓
Recursive DNS Resolver
   |
   ↓
Authoritative DNS
   |
   | "203.0.113.10"
   ↓
Client
```

The client can then connect to:

```text
203.0.113.10:443
```

---

# 3. DNS Resolution Step by Step

If the recursive resolver does not already have the answer cached:

```text
Client
  |
  ↓
Recursive Resolver
  |
  ↓
Root DNS
  |
  ↓
.com TLD DNS
  |
  ↓
Authoritative DNS
  |
  ↓
IP Address
```

### Step 1 — Client

The application asks the local DNS resolver:

```text
What is the IP of api.example.com?
```

### Step 2 — Recursive Resolver

The recursive resolver checks its cache.

```text
Cache HIT
   ↓
Return answer
```

If there is no cached answer:

```text
Cache MISS
   ↓
Perform DNS lookup
```

### Step 3 — Root DNS

The root tells the resolver where to find the `.com` servers.

### Step 4 — TLD DNS

The `.com` server tells the resolver which authoritative servers manage `example.com`.

### Step 5 — Authoritative DNS

The authoritative DNS server returns the actual record:

```text
api.example.com
       ↓
203.0.113.10
```

### Step 6 — Cache

The recursive resolver caches the answer according to its TTL and returns it to the client.

---

# 4. DNS Components

```text
Application
     ↓
Stub Resolver
     ↓
Recursive Resolver
     ↓
Root DNS
     ↓
TLD DNS
     ↓
Authoritative DNS
```

### Stub Resolver

Usually part of the operating system or runtime.

It sends DNS queries to a configured recursive resolver.

### Recursive Resolver

Performs DNS lookups on behalf of clients and caches answers.

Examples:

* ISP DNS
* Enterprise DNS
* Cloud DNS resolver
* Public DNS resolver

### Root DNS

The top of the DNS hierarchy.

It directs queries toward the appropriate TLD servers.

### TLD DNS

TLD = Top-Level Domain.

Examples:

```text
.com
.org
.net
.in
```

TLD servers point the resolver toward the authoritative DNS servers for a domain.

### Authoritative DNS

Contains the actual DNS records for a domain.

Example:

```text
example.com

api.example.com → 203.0.113.10
```

---

# 5. DNS Record Types

DNS supports different types of records.

| Record  | Purpose                         | Example                          |
| ------- | ------------------------------- | -------------------------------- |
| `A`     | Maps name to IPv4 address       | `api.example.com → 203.0.113.10` |
| `AAAA`  | Maps name to IPv6 address       | `api.example.com → 2001:db8::10` |
| `CNAME` | Points one name to another name | `www.example.com → example.com`  |
| `MX`    | Mail server                     | `example.com → mail.example.com` |
| `TXT`   | Text/verification information   | Domain verification              |
| `NS`    | Authoritative name servers      | `example.com → ns1.provider.com` |

For system design, focus mainly on:

```text
A     → IPv4
AAAA  → IPv6
CNAME → another domain name
```

---

# 6. DNS Caching

DNS responses are cached to avoid performing the full lookup every time.

```text
First request:

Client
  ↓
Recursive Resolver
  ↓
DNS hierarchy
  ↓
IP address

Later request:

Client
  ↓
Recursive Resolver
  ↓
Cached IP
```

Caching makes DNS:

* Faster
* More scalable
* Less expensive

---

# 7. TTL

> **TTL (Time To Live) defines how long a DNS answer can be cached.**

Example:

```text
api.example.com
       ↓
203.0.113.10
TTL = 300 seconds
```

The resolver can reuse that answer for up to 300 seconds before it needs to refresh it.

### TTL Trade-off

```text
Low TTL
   ↓
Changes take effect sooner
   ↓
More DNS lookups

High TTL
   ↓
Fewer DNS lookups
   ↓
Changes take longer to propagate
```

For example:

```text
Low TTL
→ Useful during traffic migration/failover

High TTL
→ Good for stable records
```

Important:

> **DNS changes are not necessarily instant because old answers may still exist in caches.**

---

# 8. DNS-Based Traffic Routing

DNS can return different answers depending on the routing policy.

For example:

```text
                 api.example.com
                        |
                       DNS
                    /       \
                   ↓         ↓
              US Region   EU Region
                   |         |
                   ↓         ↓
                 LB-US     LB-EU
```

DNS can use policies such as:

* Geographic routing
* Latency-based routing
* Weighted routing
* Failover routing

Example:

```text
US users
   ↓
api.example.com
   ↓
US Load Balancer

EU users
   ↓
api.example.com
   ↓
EU Load Balancer
```

This is useful for **global traffic distribution**.

---

# 9. DNS vs Load Balancer

These two are often confused.

### DNS

Answers:

> **Which IP/region should the client connect to?**

```text
api.example.com
       ↓
DNS
       ↓
US Load Balancer
```

### Load Balancer

Answers:

> **Which backend server should handle this request/connection?**

```text
US Load Balancer
      |
      +---- Server 1
      +---- Server 2
      +---- Server 3
```

### Combined

```text
Client
  |
  | api.example.com
  ↓
DNS
  |
  | Select region
  ↓
Regional Load Balancer
  |
  | Select server
  ↓
Server 1 / 2 / 3
```

### Remember

```text
DNS → Which REGION / ENTRY POINT?

Load Balancer → Which SERVER?
```

---

# 10. DNS and Failover

DNS can also help route traffic away from an unhealthy region.

Before:

```text
DNS
 |
 +---- US Region ✓
 |
 +---- EU Region ✓
```

If US fails:

```text
DNS
 |
 +---- US Region ✗
 |
 +---- EU Region ✓
```

DNS can stop returning the unhealthy destination.

However, failover is not instant because previously cached DNS answers may still be used until their TTL expires.

For faster, request-level failure handling, systems usually rely on load balancers and health checks inside the selected region.

---

# 11. DNS in a Distributed System

A typical global system may look like:

```text
                    DNS
                     |
          +----------+----------+
          |                     |
          ↓                     ↓
      US Region             EU Region
          |                     |
      Load Balancer         Load Balancer
          |                     |
      App Servers            App Servers
          |                     |
       Cache/DB              Cache/DB
```

DNS provides the **global entry point**.

The regional load balancer handles traffic distribution within the region.

---

# 12. DNS Failure

DNS can itself become a dependency.

If DNS resolution fails:

```text
Client
   |
   X
 DNS failure
   |
   ↓
Cannot resolve service
   |
   ↓
Application request cannot start
```

Common causes:

* DNS provider outage
* Incorrect DNS configuration
* Expired/incorrect records
* Recursive resolver failure
* Cached stale records
* Incorrect private DNS configuration

This is why DNS is an important part of system availability.

---

# 13. Public vs Private DNS

DNS can be used both publicly and internally.

### Public DNS

Used for internet-facing services:

```text
api.example.com
       ↓
Public IP / CDN / Load Balancer
```

### Private DNS

Used inside private networks:

```text
orders.internal
       ↓
Private IP
       ↓
Internal Service
```

Private DNS is commonly used in:

* Cloud networks
* Corporate networks
* Kubernetes
* Service discovery

---

# ⭐ DNS in System Design

A typical request path:

```text
User
  |
  | api.example.com
  ↓
DNS
  |
  | IP / Region
  ↓
Load Balancer
  |
  | Backend selection
  ↓
Application Server
  |
  ↓
Cache / Database
```

### Remember

**DNS → Converts names into network destinations**

**Recursive Resolver → Finds and caches DNS answers**

**Authoritative DNS → Provides the official records**

**TTL → Controls DNS caching duration**

**DNS Routing → Can choose region/entry point**

**Load Balancer → Chooses backend server**

### Interview Mental Model

```text
DNS
 ↓
WHERE should I connect?

Load Balancer
 ↓
WHICH server should handle it?

Application
 ↓
WHAT should happen?

Database
 ↓
WHERE should persistent data live?
```

# Key Topics to Remember

```text
DNS
├── Name → IP
├── Recursive Resolution
├── DNS Hierarchy
├── A / AAAA / CNAME
├── Caching
├── TTL
├── Global Routing
├── Failover
└── DNS vs Load Balancer
```

```

### One important correction to your original chapter

Don't teach **“DNS = domain name → IP address”** as the complete definition. DNS returns **resource records**, not just IPs. An `A`/`AAAA` record gives an address, but `MX`, `TXT`, `NS`, `CNAME`, etc. provide other information.

For your fundamentals notes, though, **Name → IP** is the right primary mental model.

And just like your IP/TCP/HTTP chapters, I'd **stop here**. DNSSEC, DoH/DoT, split-horizon DNS, `ndots`, CNAME flattening, negative caching, and detailed DNS troubleshooting can be separate advanced topics later.
```
