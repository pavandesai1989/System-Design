
IP       → Which machine/network?
Port     → Which service/program?
TCP      → Reliable, ordered byte stream
UDP      → Independent datagrams, no delivery/order guarantee
QUIC     → Modern transport over UDP
```

### Recommended final GitHub note

````markdown
# TCP vs UDP

> TCP and UDP are transport-layer protocols used to move data between applications.

IP gets packets to the destination machine.
TCP/UDP use **ports** to deliver data to the correct service on that machine.

```text
Client
   |
   | IP → destination machine
   | Port → destination service
   ↓
Server
````

The key difference is **what happens when data is lost, delayed, duplicated, or arrives out of order.**

---

# 1. Transport Layer

The transport layer provides communication between applications running on different machines.

Common responsibilities include:

* **Ports** → identify the destination service
* **Segmentation** → split data into smaller pieces
* **Reassembly** → reconstruct received data
* **Reliability** → detect and recover from lost data
* **Ordering** → deliver data in the correct order
* **Flow control** → prevent a fast sender from overwhelming a slow receiver
* **Congestion control** → reduce sending rate when the network is congested

TCP provides most of these features automatically.

UDP provides a much simpler transport and leaves reliability, ordering, and recovery to the application or another protocol such as QUIC.

---

# 2. TCP

> **TCP (Transmission Control Protocol)** provides a reliable, ordered byte stream.

TCP is **connection-oriented**.

Before exchanging normal application data, the client and server establish a connection.

```text
Client                         Server
   |                              |
   | -------- SYN --------------> |
   | <------ SYN-ACK ------------ |
   | -------- ACK --------------> |
   |                              |
   |       Connection ready       |
```

This is called the **TCP three-way handshake**.

The handshake adds network round-trip latency before normal application data can flow.

## What TCP Provides

### 1. Reliable Delivery

If data is lost, TCP retransmits it.

```text
Sender                    Receiver

Segment 1  ------------->   ✓
Segment 2  ----X            lost
Segment 3  ------------->   buffered

             ↓

Segment 2  ------------->   ✓

             ↓

Application receives:
Segment 1 → Segment 2 → Segment 3
```

### 2. Ordered Delivery

TCP delivers bytes to the application in the correct order.

### 3. Duplicate Detection

TCP uses sequence numbers to detect duplicate data.

### 4. Flow Control

TCP prevents a fast sender from overwhelming a slow receiver.

### 5. Congestion Control

TCP adjusts its sending rate when the network becomes congested.

---

# 3. TCP Is a Byte Stream

An important property of TCP:

> **TCP is a stream, not a message protocol.**

Suppose the application sends:

```text
Message A
Message B
Message C
```

TCP does not preserve these message boundaries.

The receiver might read:

```text
Message A + Message B
```

or:

```text
Message A
Message B + Message C
```

or even:

```text
Part of A
Remaining A + B
Part of C
```

TCP guarantees the **order of bytes**, not the boundaries of application messages.

Therefore, application protocols need a way to identify message boundaries.

Common approaches:

* Length prefix
* Delimiter
* Structured protocol format

---

# 4. TCP Head-of-Line Blocking

TCP guarantees ordered delivery.

Suppose:

```text
Segment 1 ✓
Segment 2 ✗  ← lost
Segment 3 ✓
Segment 4 ✓
```

Segments 3 and 4 may already have arrived at the receiver.

But the application cannot consume them until Segment 2 is recovered.

```text
Segment 1 → deliver
Segment 2 → waiting...
Segment 3 → waiting...
Segment 4 → waiting...
```

This is **head-of-line (HOL) blocking** at the TCP stream level.

For many applications this behavior is desirable because incomplete or out-of-order data is not useful.

Examples:

* Database results
* File downloads
* HTTP responses
* Most backend APIs

---

# 5. Where TCP Is Used

TCP is commonly used for:

* HTTP/1.1
* HTTP/2
* HTTPS
* SSH
* SMTP
* PostgreSQL
* MySQL
* Many message brokers
* Most traditional service-to-service communication
* Standard gRPC over HTTP/2

For most backend systems:

> **TCP is the default choice.**

---

# 6. UDP

> **UDP (User Datagram Protocol)** provides simple, connectionless datagram communication.

UDP does not establish a transport connection before sending data.

```text
Application
    |
    ↓
   UDP
    |
    ↓
   IP
    |
    ↓
Network
```

UDP does **not** provide:

* Guaranteed delivery
* Ordering
* Retransmission
* Flow control
* Congestion control

A UDP datagram can:

* Arrive successfully
* Be lost
* Arrive late
* Arrive out of order
* Be duplicated

The application decides what to do about these situations.

---

# 7. UDP Preserves Message Boundaries

Unlike TCP, UDP preserves datagram boundaries.

If the application sends:

```text
Datagram A
Datagram B
Datagram C
```

the receiver receives them as separate datagrams.

```text
A → A
B → B
C → C
```

A datagram can still be lost:

```text
A → A
B → X
C → C
```

UDP does not automatically retransmit B.

---

# 8. Why Use UDP?

UDP is useful when:

> **Fresh data is more valuable than old data.**

For example, imagine a game:

```text
Player position:
100,100
101,100
102,100
103,100
```

If the `101,100` update is lost, the next update may make it irrelevant.

Waiting to retransmit old data could be worse than simply using the latest update.

UDP is therefore commonly used for:

* Real-time voice
* Real-time video
* Online gaming
* WebRTC
* DNS
* QUIC
* Some telemetry and discovery protocols

---

# 9. TCP vs UDP

| Feature               | TCP                      | UDP                         |
| --------------------- | ------------------------ | --------------------------- |
| Communication model   | Connection-oriented      | Connectionless              |
| Data model            | Byte stream              | Datagram                    |
| Delivery              | Reliable                 | Best effort                 |
| Ordering              | Guaranteed               | Not guaranteed              |
| Retransmission        | Built in                 | Not built in                |
| Message boundaries    | Not preserved            | Preserved                   |
| Flow control          | Built in                 | Not built in                |
| Congestion control    | Built in                 | Not built in                |
| Head-of-line blocking | Yes, at TCP stream level | Not at UDP layer            |
| Typical use           | APIs, databases, files   | Games, real-time media, DNS |

### Simple Rule

```text
Need reliable + ordered data?
        ↓
       TCP

Need independent datagrams and can tolerate loss?
        ↓
       UDP
```

Do **not** use the shortcut:

> TCP = slow, UDP = fast

That is incorrect.

TCP can perform very well on healthy networks.

UDP simply provides fewer guarantees, which can be useful for latency-sensitive applications.

---

# 10. QUIC

Modern networking adds another important protocol:

> **QUIC is a transport protocol built on top of UDP.**

QUIC provides features such as:

* Reliable delivery
* Ordering within streams
* Congestion control
* Flow control
* Encryption using TLS 1.3
* Multiple independent streams
* Connection migration

```text
HTTP/3
   ↓
 QUIC
   ↓
 UDP
   ↓
 IP
```

### HTTP Versions

```text
HTTP/1.1 → TCP
HTTP/2   → TCP
HTTP/3   → QUIC → UDP
```

One important advantage of QUIC is that it supports multiple independent streams.

With HTTP/2 over TCP:

```text
TCP connection
       |
       +-- Stream A
       +-- Stream B
       +-- Stream C

TCP packet loss
       ↓
Can block all streams
```

With QUIC:

```text
QUIC connection
       |
       +-- Stream A
       +-- Stream B
       +-- Stream C

Loss on Stream A
       ↓
Other streams can continue
```

This reduces the impact of TCP-level head-of-line blocking.

---

# 11. TCP vs UDP vs QUIC

```text
TCP
 ↓
Reliable
Ordered
Byte stream

UDP
 ↓
Simple
Datagrams
No reliability/order guarantee

QUIC
 ↓
Runs over UDP
Reliable
Encrypted
Multiplexed streams
Congestion control
```

---

# 12. Important: Transport Reliability ≠ Business Reliability

TCP guarantees reliable delivery of bytes.

It does **not** guarantee that a business operation succeeded.

Example:

```text
Client
  |
  | POST /payment
  ↓
Server
  |
  | Payment successful
  ↓
Database
  |
  | Transaction committed
  |
  X  Connection fails
```

The client may not know whether the payment succeeded.

If the client retries:

```text
Retry payment
      ↓
Could charge twice
```

Therefore, real systems may need:

* Timeouts
* Retries
* Idempotency keys
* Request IDs
* Duplicate detection

This is an **application-level reliability problem**, not something TCP solves.

---

# 13. Choosing TCP or UDP

Ask:

> **What should the application do when data is lost or delayed?**

### Choose TCP when:

* Every byte matters
* Data must arrive in order
* Retransmission is desirable
* The application uses a stream-based protocol

Examples:

```text
Database
HTTP API
File transfer
SSH
gRPC
```

### Choose UDP when:

* Low latency matters
* Some data loss is acceptable
* Old data becomes useless quickly
* Datagram boundaries are useful
* The application/protocol handles recovery itself

Examples:

```text
Online games
Real-time voice/video
WebRTC
DNS
```

### Choose QUIC / HTTP/3 when:

* You want HTTP over QUIC
* Fast connection establishment is valuable
* Multiple independent streams are useful
* Connection migration is useful
* The environment supports UDP reliably

---

# ⭐ Interview Summary

```text
IP
 ↓
Which machine/network?

Port
 ↓
Which service?

TCP
 ↓
Reliable + ordered byte stream

UDP
 ↓
Independent datagrams
No delivery/order guarantee

QUIC
 ↓
Reliable transport over UDP
+
TLS
+
Streams
+
Congestion control
```

### Remember

**TCP → "Make sure the bytes arrive correctly and in order."**

**UDP → "Send datagrams; the application decides what to do about loss."**

**QUIC → "Modern reliable transport built on UDP."**

The key interview question is:

> **What should the application do when data is late, lost, duplicated, or out of order?**

```

**Verdict:** this version is much better for your repository. It covers everything you actually need to explain **TCP/UDP confidently in a system-design interview** without turning the fundamentals section into a networking textbook.
```
