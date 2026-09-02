
# Stateful vs Stateless Architecture

> **Stateful = the server remembers client/session state locally.**

> **Stateless = the application server does not keep client/session state locally; state is stored externally.**

> **Stateless architecture makes horizontal scaling easier because any server can handle any request.**

---

## What is State?

**State** is information that a system needs to remember between requests.

Examples:

- Login session
- Shopping cart
- Temporary session information

---

# Stateful Architecture

A **stateful service** stores client/session state **locally on the server**.

### Example — Shopping Cart

```text
User
  ↓
Load Balancer
  ↓
Server 1
  └── Cart
       └── iPhone
````

If the next request goes to Server 1:

```text
User → Server 1 → Cart found ✅
```

If it goes to Server 2:

```text
User → Server 2 → Cart not found ❌
```

Server 2 does not have the state stored by Server 1.

---

## Sticky Sessions

The load balancer can keep sending the same user to the same server.

```text
User A → Server 1
User A → Server 1
User A → Server 1
```

This is called **sticky sessions** or **session affinity**.

### Problems

* **Uneven load** — Some servers may receive more users.
* **Server failure** — The user's locally stored session may be lost.
* **Scaling becomes harder** — Requests are tied to particular servers.

---

# Stateless Architecture

A **stateless service does not store client/session state locally on the application server**.

Instead, state is stored in **shared external storage**, such as a database or Redis.

```text
                    Load Balancer
                   /             \
                  ↓               ↓
             Server 1         Server 2
                  \               /
                   \             /
                    ↓           ↓
                    DB / Redis
                        ↓
                       Cart
                     iPhone
```

Now:

```text
Request 1 → Server 1 → DB/Redis → Cart ✅

Request 2 → Server 2 → DB/Redis → Cart ✅
```

**Any server can handle any request.**

---

# Why Stateless Helps Horizontal Scaling

```text
                  Load Balancer
                /      |      |      \
               ↓       ↓      ↓       ↓
           Server 1 Server 2 Server 3 Server 4
                \       |      |       /
                 \      |      |      /
                    DB / Redis
```

As traffic increases:

```text
2 servers → 5 servers → 20 servers
```

New application servers can be added without moving user sessions between servers.

> **Stateless architecture makes horizontal scaling easier.**

---

# Stateful vs Stateless

|                    | **Stateful**              | **Stateless**      |
| ------------------ | ------------------------- | ------------------ |
| State              | Stored locally on server  | Stored externally  |
| Request            | May need same server      | Any server         |
| Sticky session     | May be needed             | Usually not needed |
| Horizontal scaling | More difficult            | Easier             |
| Server failure     | Local session may be lost | Easier to recover  |
| Complexity         | Simpler initially         | More distributed   |

---

# Important Clarification

**Stateless does NOT mean the system has no state.**

It means the **application server does not keep client/session state locally**.

For example:

```text
Order #123
Product: iPhone
Amount: ₹70,000
Status: Shipped
```

This is **persistent business data** and belongs in the database regardless of whether the application is stateful or stateless.

The stateful/stateless discussion is mainly about **client/session state**, such as:

```text
Login session
Shopping cart
Temporary session information
```

---

## ⭐ Interview Definition

> **A stateless service does not store client/session state locally on the application server. State is stored externally, allowing any server instance to handle any request. This makes horizontal scaling easier.**

---

## Mental Model

### STATEFUL

```text
User
  ↓
Server 1
  ↓
"Server remembers me"
```

### STATELESS

```text
User
  ↓
Any Server
  ↓
DB / Redis
  ↓
"Shared storage remembers me"
```

### ⭐ Remember

> **Stateful → Server remembers**

> **Stateless → Shared storage remembers**

> **Stateless → Any server can handle the request → Easier horizontal scaling**

```

**Stop here.** You don't need separate deep sections on JWT, session replication, distributed sessions, etc. yet. Those can be introduced naturally when they become relevant in an actual system-design problem.
```
