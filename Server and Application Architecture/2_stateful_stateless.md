
````markdown
# Stateful vs Stateless Servers

## Stateful Server

A **stateful server** stores client-specific state in its own memory.

```text
Request 1 → Server A
            ↓
        Session stored
        in Server A memory

Request 2 → Server A
            ↓
        Session found ✓

Request 2 → Server B
            ↓
        Session not found ✗
````

The server must remember the client between requests.

### Problem

If a load balancer sends the next request to another server, that server does not have the client's state.

This often requires **Sticky Sessions (Session Affinity)**.

---

## Stateless Server

A **stateless server does not store client-specific state locally**.

State is stored in a shared external location such as:

* Redis / Memcached → session or temporary data
* Database → persistent data
* Client → tokens such as JWT

```text
                    ┌─────────────┐
                    │   Server A  │
                    └──────┬──────┘
                           │
Client → Load Balancer ────┼────→ Server B
                           │
                    ┌──────▼──────┐
                    │    Redis    │
                    │ Shared State│
                    └─────────────┘
```

Any server can handle any request because all servers can access the same state.

---

## Stateful vs Stateless

| Property           | Stateful                        | Stateless                         |
| ------------------ | ------------------------------- | --------------------------------- |
| State location     | Server memory                   | External/shared storage           |
| Load balancing     | Usually needs sticky sessions   | Any server can handle request     |
| Horizontal scaling | More difficult                  | Simple                            |
| Server failure     | Session may be lost             | Another server can handle request |
| Adding servers     | More complex                    | Easy                              |
| Deployment         | Active sessions may be affected | Easier                            |

---

## Why Stateless Servers Matter

Stateless servers make **horizontal scaling** easier.

```text
                    Load Balancer
                   /      |      \
                  ▼       ▼       ▼
              Server A Server B Server C
                  \       |       /
                   \      |      /
                    ▼     ▼     ▼
                  Shared State
               Redis / Database
```

If Server B fails:

```text
Client → Load Balancer → Server A
                         Server C
```

The request can be handled by another server because the application state is not tied to Server B.

### Key Idea

> **Stateless → Any server can handle any request → Easy horizontal scaling**

### Interview Tip

When adding multiple application servers, prefer **stateless application servers** whenever possible.

If the interviewer asks:

**"What happens if Server B goes down?"**

Answer:

> "The load balancer routes the request to another healthy server. Since application state is stored externally, the new server can handle the request without depending on Server B's memory."

