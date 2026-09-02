````markdown
# How a Load Balancer Works

A **Load Balancer (LB)** sits between clients and backend servers.

It provides a **single entry point** for clients and routes each request to an appropriate backend server.

```text
                         INTERNET
                            │
                 ┌──────────┼──────────┐
                 │          │          │
              Client 1   Client 2   Client 3
                 │          │          │
                 └──────────┼──────────┘
                            ↓
                 ┌─────────────────────┐
                 │    LOAD BALANCER    │
                 │                     │
                 │  lb.youtube.com     │
                 │  203.0.113.1        │
                 └──────────┬──────────┘
                            │
                      Routes requests
                            │
             ┌──────────────┼──────────────┐
             ↓              ↓              ↓
         Server 1       Server 2       Server 3
         10.0.1.1       10.0.1.2       10.0.1.3
            🟢              🟢              🟢
          Healthy         Healthy         Healthy

                            │
                            ↓
                         Server 4
                         10.0.1.4
                            🔴
                         Unhealthy
````

---

## 1. Client's Perspective

The client knows **only the Load Balancer**.

```text
Client
  │
  │ Request
  ↓
lb.youtube.com
203.0.113.1
```

The client does **not need to know**:

* Server 1
* Server 2
* Server 3
* Server 4
* How many backend servers exist
* Which server will handle the request

The client simply sends the request to the Load Balancer.

---

## 2. Load Balancer's Perspective

The LB knows about the **backend server pool**.

```text
                 Load Balancer
                       │
          ┌────────────┼────────────┐
          ↓            ↓            ↓
       Server 1     Server 2     Server 3
          🟢           🟢           🟢
       Healthy       Healthy      Healthy

                       ✕
                       ↓
                   Server 4
                      🔴
                   Unhealthy
```

The LB typically:

1. Receives the request.
2. Checks the available backend servers.
3. Selects an appropriate healthy server.
4. Forwards the request to that server.
5. Receives the response.
6. Sends the response back to the client.

```text
Client
  │
  │ Request
  ↓
Load Balancer
  │
  │ Select server
  ↓
Backend Server
  │
  │ Response
  ↓
Load Balancer
  │
  │ Response
  ↓
Client
```

---

## 3. Example: Request Routing

Suppose three requests arrive at the Load Balancer.

```text
                    Load Balancer
                         │
              ┌──────────┼──────────┐
              ↓          ↓          ↓
             R1         R2         R3
              ↓          ↓          ↓
             S1         S2         S3
```

For example:

```text
Request 1 → Server 1
Request 2 → Server 2
Request 3 → Server 3
```

The exact server selected depends on the **load-balancing algorithm**.

Examples:

* Round Robin
* Least Connections
* Weighted Routing

> The client does not know which backend server handled its request.

---

## 4. What If a Server Fails?

Before failure:

```text
LB
├── S1 🟢
├── S2 🟢
├── S3 🟢
└── S4 🟢
```

Suppose **S4 fails**.

After the health check detects the failure:

```text
LB
├── S1 🟢  ← receives traffic
├── S2 🟢  ← receives traffic
├── S3 🟢  ← receives traffic
└── S4 🔴  ← no traffic
```

The LB removes **S4 from the available backend pool**.

Clients do not need to do anything differently.

```text
Client → LB → S1
          ├→ S2
          └→ S3

          S4 🔴
       Not selected
```

> **Goal:** Prevent requests from being sent to unhealthy servers.

---

## 5. What If We Add a New Server?

Existing backend pool:

```text
LB
├── S1
├── S2
└── S3
```

Add a new server:

```text
LB
├── S1
├── S2
├── S3
└── S4  ← New server
```

The clients continue using the **same Load Balancer address**.

```text
Client → lb.youtube.com → Load Balancer
```

The client does not need to know that **S4 was added**.

> **Goal:** Scale the backend without changing the client.

---

## 6. Complete Request Flow

A typical request flow looks like this:

```text
              Client
                │
                │ 1. Request
                ↓
        ┌─────────────────┐
        │ Load Balancer   │
        └────────┬────────┘
                 │
                 │ 2. Select healthy server
                 ↓
        ┌─────────────────┐
        │ Backend Server  │
        └────────┬────────┘
                 │
                 │ 3. Response
                 ↓
        ┌─────────────────┐
        │ Load Balancer   │
        └────────┬────────┘
                 │
                 │ 4. Response
                 ↓
              Client
```

---

# ⭐ Core Idea

```text
                         CLIENTS
                            │
                            │
                     "I need the service"
                            │
                            ↓
                  ┌─────────────────┐
                  │ LOAD BALANCER   │
                  │                 │
                  │ Decides where   │
                  │ the request goes│
                  └────────┬────────┘
                           │
                 ┌─────────┼─────────┐
                 ↓         ↓         ↓
                S1        S2        S3
                🟢        🟢        🟢
```

### Remember

> **Client knows the Load Balancer.**
> **Load Balancer knows the backend servers.**
> **Client does not need to know the backend servers.**

This is the key concept before learning:

* Load-Balancing Algorithms
* Health Checks
* Session Persistence
* TLS Termination
* Load Balancer Placement

---

# Common Load Balancer Technologies

| Technology                      | Type                   | Typical Use                               |
| ------------------------------- | ---------------------- | ----------------------------------------- |
| **Nginx**                       | Software (open source) | General-purpose HTTP/HTTPS load balancing |
| **HAProxy**                     | Software (open source) | High-performance TCP/HTTP load balancing  |
| **AWS ALB / NLB**               | Managed cloud service  | AWS applications                          |
| **Azure Load Balancer**         | Managed cloud service  | Azure applications                        |
| **Google Cloud Load Balancing** | Managed cloud service  | GCP applications                          |
| **F5 BIG-IP**                   | Hardware / software    | Enterprise and on-premise environments    |

### Load Balancer Categories

```text
Load Balancer
     │
     ├── Software LB
     │      ├── Nginx
     │      └── HAProxy
     │
     ├── Cloud / Managed LB
     │      ├── AWS ALB / NLB
     │      ├── Azure Load Balancer
     │      └── Google Cloud Load Balancing
     │
     └── Enterprise LB
            └── F5 BIG-IP
```

```
```
