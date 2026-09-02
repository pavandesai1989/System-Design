

````markdown id="r8c4m1"
# IP Addresses and Ports

Once DNS gives us an IP address, the client needs:

- **IP Address** → Which destination?
- **Port** → Which application/service?

```text
IP Address + Port
       │
       ▼
Which destination + Which service?
````

---

## IP Address

An IP address is a network address used to identify a destination on a network.

### IPv4

Example:

```text
142.250.80.46
```

IPv4 uses **32 bits (4 bytes)**.

### IPv6

Example:

```text
2607:f8b0:4004:800::200e
```

IPv6 uses **128 bits (16 bytes)** and provides a much larger address space.

### IPv4 vs IPv6

|               | IPv4           | IPv6            |
| ------------- | -------------- | --------------- |
| Address size  | 32 bits        | 128 bits        |
| Example       | `192.168.1.10` | `2001:db8::1`   |
| Address space | Smaller        | Extremely large |

---

# Port Number

A single machine can run multiple network services.

A **port number identifies the application/service** that should receive the connection.

```text
                    Server
                       │
        ┌──────────────┼──────────────┐
        ▼              ▼              ▼
     Port 80        Port 443       Port 5432
       HTTP          HTTPS        PostgreSQL
```

### Common Ports

|      Port | Protocol / Service | Purpose                      |
| --------: | ------------------ | ---------------------------- |
|    **80** | HTTP               | Web traffic                  |
|   **443** | HTTPS              | Secure web traffic           |
|    **22** | SSH                | Remote server access         |
|  **5432** | PostgreSQL         | Database connections         |
|  **3306** | MySQL              | Database connections         |
|  **6379** | Redis              | Cache/data-store connections |
| **27017** | MongoDB            | Database connections         |

---

# IP Address + Port

Think of:

> **IP address = Building address**
>
> **Port = Apartment number**

For example:

```text
142.250.80.46:443
^^^^^^^^^^^^^ ^^^
     │         │
     │         └── Port
     └──────────── IP address
```

The combination identifies the **network destination and service** to connect to.

---

# Putting It Together

A simplified web request:

```text
1. Client asks DNS:
   
   example.com → 142.250.80.46

2. Client connects to:

   142.250.80.46:443

                   │
                   ▼
              ┌─────────┐
              │ Server  │
              │         │
              │ :443    │ → HTTPS
              │ :5432   │ → PostgreSQL
              │ :6379   │ → Redis
              └─────────┘
```

### Key Idea

> **DNS gives you the destination address.**

> **IP address identifies the network destination.**

> **Port identifies the service/application to connect to.**

```text
Domain
   │
   │ DNS
   ▼
IP Address
   │
   │ + Port
   ▼
Specific Network Service
```

### System Design Relevance

In architecture diagrams, you may see services listening on different ports:

```text
Application Server
       │
       ├── :8080 → Application
       ├── :5432 → PostgreSQL
       └── :6379 → Redis
```

The port tells the operating system **which service should receive the network connection**.

```

This is **enough for the IP/Port section**. You don't need to go into subnetting, NAT, private/public IPs, CIDR, or ephemeral ports yet; those can be introduced only if a later system-design topic requires them.
```
