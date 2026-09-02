
# OSI Model

> **OSI (Open Systems Interconnection) model divides network communication into 7 layers, from physical signals to application protocols.**

It provides a common way to understand **how network communication works and where a failure occurs**.

---

# The 7 Layers

```text
┌──────────────────────────────┐
│ Layer 7 - Application        │  HTTP, DNS, SMTP, gRPC
├──────────────────────────────┤
│ Layer 6 - Presentation       │  Encryption, Compression, Encoding
├──────────────────────────────┤
│ Layer 5 - Session            │  Session Management
├──────────────────────────────┤
│ Layer 4 - Transport          │  TCP, UDP, Ports
├──────────────────────────────┤
│ Layer 3 - Network            │  IP, Routing
├──────────────────────────────┤
│ Layer 2 - Data Link          │  Ethernet, WiFi, MAC, Switch
├──────────────────────────────┤
│ Layer 1 - Physical           │  Cables, Signals, Radio
└──────────────────────────────┘
````

### Mnemonic

> **Please Do Not Throw Sausage Pizza Away**

```text
P → Physical
D → Data Link
N → Network
T → Transport
S → Session
P → Presentation
A → Application
```

---

# Layer 1 — Physical

> **Moves raw bits through the physical medium.**

Examples:

* Ethernet cable
* Fiber
* WiFi radio
* Cellular signals

```text
Computer
   ↓
Electrical / Optical / Radio Signals
   ↓
Network
```

Think:

> **"Can the signal physically travel?"**

---

# Layer 2 — Data Link

> **Handles communication between devices on the same local network.**

Examples:

* Ethernet
* WiFi
* MAC addresses
* Switches
* VLANs

```text
Computer A
    ↓
  Switch
    ↓
Computer B
```

Key concept:

> **MAC address = local network addressing**

Think:

> **"Can I reach the device on my local network?"**

---

# Layer 3 — Network

> **Moves packets between different networks.**

Main concepts:

* IP addresses
* Routers
* Routing
* IPv4 / IPv6
* ICMP

```text
Network A
   ↓
 Router
   ↓
Network B
```

Key concept:

> **IP address = network-level addressing**

Think:

> **"Can I reach the destination network/host?"**

---

# Layer 4 — Transport

> **Provides communication between applications/processes using ports.**

Main protocols:

* **TCP**
* **UDP**

Key concepts:

* Ports
* TCP connections
* Reliability
* Ordering
* Flow control
* Congestion control

Example:

```text
Client
192.168.1.10:50000
        |
        | TCP
        ↓
Server
10.0.0.5:443
```

Think:

> **"Can I establish communication with the correct service?"**

### Important

This is also where **Layer 4 Load Balancers** operate.

```text
Client
   ↓
L4 Load Balancer
   ↓
TCP/UDP
   ↓
Backend Servers
```

---

# Layer 5 — Session

> **Manages the lifecycle of communication sessions.**

Concepts:

* Session establishment
* Maintaining sessions
* Session termination
* Session recovery/resumption

In modern systems, session functionality is usually handled by **application protocols, libraries, or frameworks** rather than a separate Layer 5 implementation.

Think:

> **"How is the communication session maintained?"**

---

# Layer 6 — Presentation

> **Defines how data is represented, encoded, compressed, or encrypted.**

Examples:

* JSON
* Protocol Buffers
* UTF-8
* gzip / Brotli
* TLS encryption

Think:

> **"How is the data represented and protected?"**

---

# Layer 7 — Application

> **Defines the protocols that applications use to communicate.**

Examples:

* HTTP / HTTPS
* DNS
* SMTP
* SSH
* gRPC
* Database protocols

Example:

```text
Client
   |
   | GET /users/123
   ↓
HTTP Server
```

Think:

> **"What does this communication mean to the application?"**

---

# OSI Data Units

As data moves down the stack, each layer adds its own information.

```text
Application
     ↓
    Data
     ↓
Transport
     ↓
 TCP Segment / UDP Datagram
     ↓
Network
     ↓
    Packet
     ↓
Data Link
     ↓
    Frame
     ↓
Physical
     ↓
    Bits
```

Remember:

```text
Layer 7/6/5 → Data
Layer 4     → Segment / Datagram
Layer 3     → Packet
Layer 2     → Frame
Layer 1     → Bits
```

---

# OSI vs TCP/IP

The OSI model has **7 layers**, while the TCP/IP model is commonly represented using **4 layers**.

```text
OSI                         TCP/IP

Application      ┐
Presentation     ├──────→   Application
Session          ┘

Transport        ───────→   Transport

Network          ───────→   Internet

Data Link        ┐
Physical         ┘──────→   Network Access
```

The OSI model is mainly a **conceptual/reference model**.

The TCP/IP model is closer to how the Internet is actually implemented.

---

# How OSI Helps in Troubleshooting

Instead of saying:

> "The network is broken."

Ask:

> **"Which layer is failing?"**

Example:

```text
Cannot access API
      ↓
Is the cable/WiFi working?
      ↓
Layer 1

Can I reach the local network?
      ↓
Layer 2

Can I reach the destination IP?
      ↓
Layer 3

Can I connect to port 443?
      ↓
Layer 4

Does TLS work?
      ↓
Layer 6 / Application boundary

Does HTTP request work?
      ↓
Layer 7
```

### Example

If:

```text
ping server → works
TCP connection → works
TLS handshake → works
HTTP request → 503
```

The lower network layers are probably working.

The problem is more likely at the **application/service layer**.

---

# OSI in System Design

The most important layers for system design are usually:

```text
Layer 3 → IP / Routing

Layer 4 → TCP / UDP / Ports
           ↓
        L4 Load Balancer

Layer 7 → HTTP / HTTPS / APIs
           ↓
        L7 Load Balancer
        Reverse Proxy
        API Gateway
```

This connects directly to concepts you already studied:

```text
DNS
 ↓
IP Address
 ↓
TCP / UDP
 ↓
Load Balancer
 ↓
HTTP / HTTPS
 ↓
Application
```

---

# ⭐ Remember

```text
L1 → Physical signals
L2 → Local network / MAC
L3 → IP / Routing
L4 → TCP / UDP / Ports
L5 → Sessions
L6 → Data representation / Encryption
L7 → Application protocols
```

### Most Important for Interviews

> **Layer 3 → Where is the host?**

> **Layer 4 → Which service/port?**

> **Layer 7 → What does the request mean?**

```text
L3 → IP
L4 → Port
L7 → HTTP/API
```

### Interview Tip

You do **not** need to memorize every OSI detail.

For system design, understand especially:

**L3 → IP & routing**

**L4 → TCP/UDP & ports**

**L7 → HTTP/HTTPS & application protocols**

and know that **L4 and L7 load balancing operate at different levels of the stack.**

```

**Verdict: this version is enough.** Your original material is closer to a networking-engineering reference chapter; this version is better aligned with the level and sequence of your system-design notes.
```
