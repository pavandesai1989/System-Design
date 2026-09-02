````markdown
# Load Balancer — Responsibilities

A Load Balancer is responsible for more than just distributing traffic.

```text
                    ┌─────────────────────────┐
                    │     LOAD BALANCER       │
                    │                         │
                    │  1. Traffic Distribution│
                    │  2. Health Monitoring   │
                    │  3. TLS Termination     │
                    │  4. Session Persistence │
                    │  5. Connection Management│
                    │  6. DDoS Protection    │
                    └────────────┬────────────┘
                                 │
                  ┌──────────────┼──────────────┐
                  ↓              ↓              ↓
              Server 1        Server 2        Server 3
              Healthy        Healthy         Healthy
````

---

## 1. Traffic Distribution

The LB receives incoming requests and decides **which backend server should handle each request**.

```text
Client
  │
  │ Request
  ↓
 LB
 ├────────→ Server 1
 ├────────→ Server 2
 └────────→ Server 3
```

The LB can use algorithms such as:

* **Round Robin**
* **Least Connections**
* **Weighted Routing**

> **Goal:** Prevent one server from receiving too much traffic.

---

## 2. Health Monitoring

The LB continuously checks whether backend servers are healthy.

```text
                 Load Balancer
                /      |      \
               ↓       ↓       ↓
             S1 🟢   S2 🔴   S3 🟢
            Healthy   Failed  Healthy
                       X
                  No traffic
```

If **S2 fails**, the LB temporarily removes it from the available server pool.

```text
Client
  │
  ↓
 LB ─────→ S1 🟢
  │
  └──────→ S3 🟢

          S2 🔴
       Removed from pool
```

> **Goal:** Don't send requests to failed servers.

---

## 3. TLS Termination

**TLS termination** means the **Load Balancer handles the HTTPS connection from the client** instead of the backend server handling it directly.

### Without TLS Termination

```text
Client
  │
  │ HTTPS 🔒
  ↓
Server
  │
  └── Handles TLS
      ├── Certificate
      ├── Encryption
      └── Decryption
```

The backend server is responsible for handling the TLS/HTTPS connection.

### With TLS Termination

```text
Client
  │
  │ HTTPS 🔒
  │ Encrypted
  ↓
┌──────────────────┐
│  Load Balancer   │
│                  │
│ TLS Termination  │
│       🔓         │
│  Decrypt HTTPS   │
└────────┬─────────┘
         │
         │ HTTP
         ↓
   Backend Server
```

### How It Works

#### 1. Client → Load Balancer

The client sends an HTTPS request.

```text
Client ── HTTPS 🔒 ──→ LB
```

The data is encrypted.

#### 2. Load Balancer Terminates TLS

The LB handles the TLS connection and **decrypts the request**.

```text
HTTPS 🔒
   ↓
   LB
   ↓
 Decrypt
```

The LB typically manages the TLS certificate and private key.

#### 3. Load Balancer → Backend

The LB forwards the request to the backend server.

```text
LB ── HTTP ──→ Backend
```

The backend does not need to perform TLS processing for that client connection.

### Why Use TLS Termination?

* **Centralized certificate management** — certificates can be managed at the LB.
* **Less TLS processing on backend servers** — backend servers don't handle the client-side TLS connection.
* **Simpler backend configuration** — servers can receive normal HTTP requests from the LB.

### Important

TLS termination does **not** mean communication between LB and backend must always be HTTP.

For stronger security, the LB can also establish HTTPS with the backend:

```text
Client
  │
  │ HTTPS 🔒
  ↓
┌────────────────┐
│ Load Balancer  │
│                │
│ TLS Termination│
└───────┬────────┘
        │
        │ HTTPS 🔒
        ↓
  Backend Server
```

The flow becomes:

```text
Client ── HTTPS ──→ LB ── HTTPS ──→ Backend
                    ↑
              TLS termination
              + new TLS connection
```

### Key Point

> **TLS termination = The Load Balancer terminates the client's HTTPS connection, decrypts the request, and forwards it to the backend.**

---

## 4. Session Persistence

Sometimes a user's requests need to go to the **same server**.

```text
User A
  │
  ├── Request 1 ──→ LB ──→ Server 1
  ├── Request 2 ──→ LB ──→ Server 1
  └── Request 3 ──→ LB ──→ Server 1
```

This is called **Sticky Session** or **Session Persistence**.

> Used when session state is stored locally on a backend server.

---

## 5. Connection Management

The LB can maintain and reuse connections to backend servers.

```text
Client
  │
  ↓
 LB
  │
  │ Persistent connection
  └────────────────→ Server
```

Instead of repeatedly establishing new backend connections, the LB can **reuse existing connections**.

> **Goal:** Reduce connection setup and handshake overhead.

---

## 6. DDoS Protection

A load balancer can help absorb or filter large numbers of unwanted connections before they reach application servers.

```text
Attack Traffic
 ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓
        │
        ↓
 ┌──────────────┐
 │Load Balancer │
 │    Filter    │
 └───────┬──────┘
         │
    Valid traffic
         ↓
 ┌──────────────┐
 │ App Servers  │
 └──────────────┘
```

> **Goal:** Protect backend servers from excessive unwanted traffic.

---

## ⭐ Remember the LB in 6 Words

```text
DISTRIBUTE → CHECK → DECRYPT → STICK → REUSE → PROTECT

Traffic → Health → TLS → Session → Connections → DDoS
```

```
```
