
# IP Addresses

> **An IP address is a network address used to identify the destination of packets.**

Routers use the **destination IP address** to decide where to forward a packet.

```text
Client
  |
  | Source IP
  | Destination IP
  ↓
Router
  |
  | Forward based on destination IP
  ↓
Server
````

### Important

An IP address is **not a permanent identity of a machine or user**.

It can:

* Change over time
* Be assigned to a different machine
* Be shared by many users through NAT
* Represent a load balancer, proxy, or CDN

> **Think of an IP address as a routing address, not an identity.**

---

# IPv4

**IPv4** uses **32-bit addresses**.

Example:

```text
192.168.1.10
```

An IPv4 address contains four numbers called **octets**:

```text
192 . 168 . 1 . 10
```

Each octet ranges from:

```text
0 → 255
```

IPv4 provides:

```text
2^32 ≈ 4.3 billion addresses
```

Because IPv4 addresses are limited, techniques such as **private addresses and NAT** are widely used.

---

# IPv6

**IPv6** uses **128-bit addresses**.

Example:

```text
2001:db8::1
```

IPv6 provides a vastly larger address space:

```text
2^128 addresses
```

### IPv4 vs IPv6

|               | IPv4           | IPv6                                  |
| ------------- | -------------- | ------------------------------------- |
| Address size  | 32 bits        | 128 bits                              |
| Example       | `192.168.1.10` | `2001:db8::1`                         |
| Address space | ~4.3 billion   | Extremely large                       |
| NAT           | Common         | Not required for address conservation |
| Broadcast     | Supported      | No broadcast                          |

Modern systems may support both IPv4 and IPv6 using **dual stack**.

---

# CIDR and Subnets

> **CIDR (Classless Inter-Domain Routing) is the standard way to describe IP address ranges.**

Example:

```text
192.168.1.0/24
```

The `/24` means:

```text
24 bits → Network portion
 8 bits → Host portion
```

Therefore:

```text
2^8 = 256 addresses
```

For a traditional IPv4 subnet:

```text
192.168.1.0   → Network address
192.168.1.1   → Host
...
192.168.1.254 → Host
192.168.1.255 → Broadcast
```

So a `/24` traditionally provides **254 usable host addresses**.

---

## Common CIDR Sizes

| CIDR  | Total IPv4 Addresses | Common Use            |
| ----- | -------------------: | --------------------- |
| `/8`  |           16,777,216 | Very large network    |
| `/16` |               65,536 | VPC / large network   |
| `/20` |                4,096 | Medium subnet         |
| `/24` |                  256 | Small subnet          |
| `/28` |                   16 | Small network segment |
| `/32` |                    1 | Single IP             |

### Example

```text
10.0.0.0/16
```

can be divided into smaller subnets:

```text
10.0.0.0/20      → Application
10.0.16.0/20     → Database
10.0.32.0/20     → Other services
```

This is common in cloud networking.

---

# Public vs Private IP

## Public IP

A **public IP** can be routed on the public Internet.

Common uses:

* Internet-facing load balancers
* Public servers
* VPN endpoints
* NAT gateways
* CDN/edge infrastructure

> **Public does not mean secure.**

Security still depends on firewalls, authentication, authorization, and other controls.

---

## Private IP

Private IPv4 addresses are used inside internal networks.

The three RFC 1918 private ranges are:

| Range                           | CIDR             |
| ------------------------------- | ---------------- |
| `10.0.0.0 – 10.255.255.255`     | `10.0.0.0/8`     |
| `172.16.0.0 – 172.31.255.255`   | `172.16.0.0/12`  |
| `192.168.0.0 – 192.168.255.255` | `192.168.0.0/16` |

Private addresses are **not routed directly over the public Internet**.

Example:

```text
Internet
    |
Public IP
    |
  Router
    |
Private Network
    |
10.0.0.10
10.0.0.11
10.0.0.12
```

Different private networks can reuse the same ranges.

---

# NAT

> **NAT (Network Address Translation) translates private IP addresses to public IP addresses and vice versa.**

It allows many private devices to share a public IPv4 address.

```text
Private Network

10.0.0.10 ──┐
10.0.0.11 ──┼──→ NAT ──→ Public IP ──→ Internet
10.0.0.12 ──┘
```

Example:

```text
Laptop
10.0.0.10
    |
    ↓
NAT
203.0.113.5
    |
    ↓
Internet
```

The external server sees:

```text
203.0.113.5
```

rather than:

```text
10.0.0.10
```

### Why NAT is Used

* Conserves IPv4 addresses
* Allows private networks to access the Internet
* Provides a boundary between private and public networks

### NAT Considerations

NAT introduces additional complexity:

* Inbound connections need special handling.
* NAT maintains connection state.
* NAT can become a scaling/capacity dependency.
* Many users may appear to come from the same public IP.

---

# Routing

Once a packet has a destination IP, routers determine where to send it.

```text
Client
   |
   v
Router 1
   |
   v
Router 2
   |
   v
Server
```

Routing is **hop-by-hop**.

Each router determines the **next hop** rather than needing to know the entire path.

---

## Longest Prefix Match

Routers choose the **most specific matching route**.

Example:

```text
10.0.0.0/8
10.0.1.0/24
```

For:

```text
10.0.1.50
```

both routes match.

The router chooses:

```text
10.0.1.0/24
```

because `/24` is more specific than `/8`.

> **Longest prefix match = most specific matching route wins.**

---

# IP Address in System Design

IP addressing appears in many system-design components:

```text
              Internet
                  |
              Public IP
                  |
          Load Balancer
                  |
          Private Network
          /      |       \
         /       |        \
      App 1     App 2     App 3
         \       |        /
          \      |       /
             Database
```

You will encounter IP addresses in:

* Load balancers
* DNS
* Firewalls
* VPCs
* Subnets
* NAT gateways
* Database access rules
* Kubernetes networking
* Service-to-service communication

---

# Important Design Principles

### 1. Don't Hard-Code IPs

Prefer:

```text
api.example.com
```

over:

```text
10.0.1.25
```

IP addresses can change because of scaling, failover, deployment, or infrastructure changes.

### 2. Avoid Overlapping CIDRs

For example:

```text
VPC A → 10.0.0.0/16
VPC B → 10.0.0.0/16
```

Connecting these networks becomes difficult because their address ranges overlap.

### 3. Plan for Growth

Leave enough IP space for:

* More servers
* More subnets
* More containers/pods
* More regions
* Future network connections

---

# ⭐ Remember

```text
IP Address
    ↓
Where should the packet go?

IPv4
    ↓
32-bit address

IPv6
    ↓
128-bit address

CIDR
    ↓
Defines IP ranges/subnets

Public IP
    ↓
Internet-routable

Private IP
    ↓
Internal network

NAT
    ↓
Private IP ↔ Public IP

Router
    ↓
Forwards packets toward destination
```

### Interview Tip

The most important concepts to know are:

```text
IP
 ↓
IPv4 / IPv6
 ↓
CIDR / Subnet
 ↓
Public / Private IP
 ↓
NAT
 ↓
Routing
```

> **IP = network address**

> **CIDR = defines the network range**

> **NAT = translates addresses**

> **Router = forwards packets**

```

**Verdict: this is enough for your system-design fundamentals.** You can later learn subnetting calculations, VPC routing tables, Kubernetes networking, BGP, and IPv6 details when those topics actually appear in an architecture problem.
```
