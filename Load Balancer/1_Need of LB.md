# Need for a Load Balancer

When an application receives a large number of requests, a **single server may not be enough** to handle the traffic. We can run multiple servers and distribute requests across them.

## 1. The Problem — Without a Load Balancer

```text
Client
   │
   ▼
Server 1
```

If the client directly connects to one server:

* **Single point of failure:** If the server goes down, the client cannot access the application.
* **Overload:** If the server receives too much traffic, requests become slow or may fail.
* **Difficult to scale:** Adding more servers requires clients to know which server to connect to.
* **Difficult server management:** Removing or replacing a server can affect clients.

## 2. The Solution — Load Balancer

Place a **Load Balancer (LB)** between the client and multiple backend servers.

```text
                         ┌──→ Server 1
                         │
Client ──→ Load Balancer ├──→ Server 2
                         │
                         └──→ Server 3
```

The client sends all requests to the **Load Balancer**.

The Load Balancer then:

1. Receives the incoming request.
2. Selects an appropriate backend server.
3. Forwards the request to that server.
4. Returns the server's response to the client.

The client does **not need to know which backend server handles the request**.

## 3. Benefits of a Load Balancer

### Single Entry Point

The client needs to know only the **Load Balancer's address**, not the addresses of individual servers.

### Traffic Distribution

The Load Balancer distributes incoming requests across multiple backend servers.

### Fault Tolerance

If a server fails, the Load Balancer can stop sending requests to that server and route traffic to healthy servers.

### Scalability

New servers can be added to the backend without changing the client.

### Server Removal

Servers can be removed, restarted, or taken down without directly affecting clients.

### Better Availability

The application can continue serving requests even when one or more backend servers fail.

## Key Idea

> **A Load Balancer provides a single entry point for clients and distributes incoming requests across multiple backend servers.**
