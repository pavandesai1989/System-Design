

````markdown id="q4m7x2"
# Client-Server Model

The **client-server model** is the foundation of most networked systems.

- **Client** → Initiates a request
- **Server** → Receives the request, processes it, and sends a response

```text
Client
   │
   │ Request
   ▼
Server
   │
   │ Response
   ▼
Client
````

---

## Examples of Clients

* Web browsers — Chrome, Safari, Firefox
* Mobile apps — Instagram, WhatsApp
* Desktop applications — Slack, VS Code
* Another server calling an API

A client is simply the **request initiator**.

---

## Examples of Servers

* **Web Server** — Nginx, Apache
* **Application Server** — Node.js, Django, Spring Boot
* **Database Server** — PostgreSQL, MySQL

A server is simply a system that **receives and processes requests**.

---

## One Server, Many Clients

The simplest architecture looks like:

```text
        Client 1 ──┐
        Client 2 ──┤
        Client 3 ──┤
        Client 4 ──┤
                   ▼
               ┌────────┐
               │ Server │
               └───┬────┘
                   │
                   ▼
               Database
```

Multiple clients can communicate with the same server.

---

## What Happens as Traffic Grows?

A single server has limited resources.

```text
Small Scale

Clients
  │
  ▼
┌────────┐
│ Server │
└────────┘


Large Scale

Clients
  │
  ▼
┌───────────────┐
│ Load Balancer │
└───────┬───────┘
        │
    ┌───┼───┐
    ▼   ▼   ▼
   S1  S2  S3
```

As traffic increases, we can add more servers and distribute requests using a **load balancer**.

Other building blocks help solve different scaling problems:

* **Load Balancer** → distributes traffic across servers
* **Cache** → reduces repeated database access
* **Database** → stores persistent data
* **CDN** → serves content closer to users
* **Object Storage** → stores large files such as images and videos

---

## Important Point

The client does not need to know how many servers exist behind the system.

```text
Client
   │
   │ Request
   ▼
Load Balancer
   │
   ├──► Server A
   ├──► Server B
   └──► Server C
```

From the client's perspective, it is still communicating with **one logical service**.

### Key Idea

> **Client → sends request**
>
> **Server → processes request and sends response**

> **System design focuses on how to scale this simple model to handle large traffic reliably and efficiently.**

