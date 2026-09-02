
````markdown
# Reverse Proxy

A **reverse proxy** sits between clients and backend servers.

The client sends requests to the reverse proxy, and the reverse proxy forwards them to the appropriate backend server.

```text
Client
   │
   │ Request
   ▼
┌─────────────────┐
│  Reverse Proxy  │
│     Nginx       │
└────────┬────────┘
         │
     ┌───┼────┐
     ▼   ▼    ▼
    App1 App2 App3
````

The client **does not communicate directly with the backend servers**.

---

## What Does a Reverse Proxy Do?

| Function            | Purpose                                                |
| ------------------- | ------------------------------------------------------ |
| **Request Routing** | Routes requests to the appropriate backend             |
| **Load Balancing**  | Distributes traffic across multiple backend servers    |
| **TLS Termination** | Handles HTTPS encryption/decryption                    |
| **Caching**         | Caches responses to reduce backend load                |
| **Compression**     | Compresses responses before sending them to clients    |
| **Rate Limiting**   | Limits excessive requests                              |
| **Security**        | Hides backend servers and provides a security boundary |

---

## Reverse Proxy vs Forward Proxy

The key difference is **who the proxy represents**.

### Forward Proxy

Used on the **client side**.

```text
Client → Forward Proxy → Internet → Server
```

The proxy represents/hides the **client**.

Examples:

* Corporate proxy
* Some VPN/proxy setups

### Reverse Proxy

Used on the **server side**.

```text
Client → Internet → Reverse Proxy → Backend Servers
```

The proxy represents/hides the **servers**.

Example:

* Nginx in front of application servers

---

## Common Reverse Proxy Technologies

* **Nginx**
* **HAProxy**
* **Envoy**
* **Traefik**
* **Cloudflare** (reverse proxy + CDN services)

---

## Reverse Proxy vs Load Balancer

These concepts overlap, but they are not exactly the same.

```text
                    ┌─────────────────┐
Client ────────────►│  Reverse Proxy  │
                    │                 │
                    │ Routing         │
                    │ TLS             │
                    │ Caching         │
                    │ Rate Limiting   │
                    │ Load Balancing │
                    └────────┬────────┘
                             │
                       ┌─────┼─────┐
                       ▼     ▼     ▼
                      App1  App2  App3
```

A **reverse proxy** can provide many functions.

A **load balancer** primarily focuses on distributing traffic across servers.

For example, **Nginx can act as both a reverse proxy and a load balancer**.

### Key Idea

> **Reverse Proxy = Gateway between clients and backend servers**

It can provide **routing, security, TLS termination, caching, rate limiting, and load balancing**.
