

````markdown
# TCP vs UDP

TCP and UDP are **Transport Layer (Layer 4) protocols**.

Once we know the **IP address and port**, the transport protocol determines how data is delivered between the client and server.

---

## TCP — Transmission Control Protocol

TCP is **connection-oriented and reliable**.

Before exchanging data, TCP establishes a connection using a **three-way handshake**:

```text
Client                    Server
  |                         |
  |-------- SYN ----------> |
  | <----- SYN + ACK -------|
  |-------- ACK ----------> |
  |                         |
  |====== Data Transfer ===>|
````

### TCP Guarantees

* **Reliable delivery** — lost data is retransmitted.
* **Ordered delivery** — data is delivered in the order it was sent.
* **Error detection** — corrupted data is detected.
* **Flow control** — prevents overwhelming the receiver.
* **Congestion control** — adapts transmission to network congestion.

### Cost of TCP

These guarantees introduce additional overhead and can increase latency because of:

* Connection establishment
* Acknowledgments
* Retransmissions
* Flow and congestion control

### Where TCP Is Used

* HTTP/HTTPS
* REST APIs
* gRPC
* Database connections
* File transfers

**Key idea:**

> TCP prioritizes **reliability and correctness** over minimum latency.

---

# UDP — User Datagram Protocol

UDP is **connectionless** and does not guarantee delivery or ordering.

There is no connection setup handshake:

```text
Client                    Server
  |                         |
  |-------- Data ---------->|
  |-------- Data ---------->|
  |-------- Data ---------->|
  |                         |
```

If a packet is lost:

```text
Client                    Server

  |-------- Packet 1 ------>|
  |-------- Packet 2 --X    |  ← Lost
  |-------- Packet 3 ------>|
```

UDP does not automatically retransmit the lost packet.

If packets arrive out of order, the **application must decide how to handle them**.

### UDP Characteristics

* No connection setup
* No delivery guarantee
* No ordering guarantee
* No built-in retransmission
* Lower protocol overhead
* Suitable for latency-sensitive applications

**Key idea:**

> UDP prioritizes **low latency and simplicity** over guaranteed delivery.

---

# TCP vs UDP

| Feature            | TCP                         | UDP                     |
| ------------------ | --------------------------- | ----------------------- |
| Connection         | Connection-oriented         | Connectionless          |
| Delivery guarantee | Yes                         | No                      |
| Ordering           | Yes                         | No                      |
| Retransmission     | Yes                         | No                      |
| Flow control       | Yes                         | No                      |
| Congestion control | Yes                         | No                      |
| Overhead           | Higher                      | Lower                   |
| Typical use        | APIs, web, databases, files | Real-time communication |

---

# Choosing TCP vs UDP

The choice depends on what the application values more:

```text
Do we need every piece of data?
        |
   +----+----+
   |         |
  YES       NO
   |         |
  TCP       UDP
   |         |
Correctness  Low latency
```

### TCP

Use TCP when **data must arrive correctly and completely**.

Examples:

```text
HTTP/HTTPS
REST APIs
gRPC
Database queries
File transfers
```

### UDP

Use UDP when **low latency is more important than delivering every packet**.

Examples:

```text
Online gaming
Real-time voice/video
Live telemetry
Some real-time streaming protocols
```

---

# Important Example: Video

Not all video systems use UDP.

### Video Streaming

Services such as video-on-demand typically use **HTTP-based streaming**.

```text
Video
  ↓
Small chunks
  ↓
HTTP
  ↓
TCP
  ↓
Client buffer
  ↓
Playback
```

The client can buffer data, so occasional retransmissions are acceptable.

### Real-Time Video Call

Video calls are interactive and latency-sensitive.

```text
Camera
  ↓
Video frames
  ↓
Network
  ↓
Receiver
  ↓
Screen
```

If an old frame is delayed, displaying it later may be less useful than dropping it and showing a newer frame.

Therefore, real-time communication often uses **UDP-based transports**.

---

# TCP/UDP in System Design

In most system design interviews, you will primarily use **TCP-based communication**:

```text
Client
   ↓
HTTP/HTTPS
   ↓
TCP
   ↓
Load Balancer
   ↓
Application Server
   ↓
Database
```

Mention UDP when the system is **latency-sensitive**, such as:

* Real-time gaming
* Voice/video communication
* Real-time telemetry

---

## Mental Model

```text
TCP → "Make sure the data arrives correctly."

UDP → "Send it quickly; the application can decide what to do if it is lost."
```

### Interview Tip

For a normal web/API system:

> **Use TCP.**

For a real-time system where stale data is often worse than lost data:

> **Consider UDP.**

```

This is **enough for your TCP/UDP section**. I would not add packet structure, TCP sequence numbers, sliding windows, congestion algorithms, or detailed handshake internals yet. Those are useful networking topics, but they will make your system-design fundamentals notes unnecessarily heavy.
```
