

````markdown
# Servers and Application Architecture

## What is a Server?

A server is a computer that **listens for requests, processes them, and sends responses**.

There is nothing special about the hardware. Any computer can act as a server.

What makes a machine a **server** is:

- The software running on it
- The role it plays in the architecture

---

## Single Server Architecture

The simplest architecture runs the entire application on **one machine**.

```text
                    Client
                       │
                       ▼
              ┌─────────────────┐
              │     Server      │
              │                 │
              │  Web Server     │
              │      ↓          │
              │ Application     │
              │      ↓          │
              │   Database      │
              └─────────────────┘
````

### What Runs on the Server?

**1. Web Server**

Examples: Nginx, Apache

* Receives HTTP/HTTPS requests
* Serves static files
* Forwards dynamic requests to the application

**2. Application**

Examples: Node.js, Django, Spring Boot

* Business logic
* Authentication
* API endpoints

**3. Database**

Examples: PostgreSQL, MySQL

* Stores persistent data
* Data is stored on the server's disk

---

## Advantages

* Simple to build
* Easy to deploy
* Low cost
* Easy to manage for small applications

---

## Problems with a Single Server

As traffic and data grow, the single server can become a bottleneck.

```text
                    Client
                       │
                       ▼
                ┌─────────────┐
                │   Server    │
                │             │
                │ Web Server  │
                │ Application │
                │ Database    │
                └─────────────┘
                       │
                 ❌ Server fails
                       │
                       ▼
                  Entire system
                     is down
```

### Common Problems

| Problem                       | Impact                                 |
| ----------------------------- | -------------------------------------- |
| CPU becomes overloaded        | Requests become slow                   |
| Memory becomes exhausted      | Applications may crash                 |
| Disk I/O becomes a bottleneck | Database operations become slow        |
| Network becomes saturated     | Requests may timeout                   |
| Server failure                | Entire application becomes unavailable |
| Limited scalability           | One machine has limited resources      |

The biggest architectural problem is:

> **The entire system depends on a single machine.**

This creates a **Single Point of Failure (SPOF)**.

---

## Next Step: Separate the Database

When the application grows, a common first step is to separate the database from the application server.

```text
              Client
                 │
                 ▼
          Application Server
                 │
                 ▼
             Database
```

Now application and database resources can be scaled independently.

> **Single Server → Separate Application and Database → Add Load Balancer → Scale Horizontally**

```

This is **enough for your Server section** at this stage. The scaling details can stay in your later **Scaling** section rather than being repeated here.
```
