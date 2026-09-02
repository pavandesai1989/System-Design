

````markdown
# API Gateway

An **API Gateway** is a specialized reverse proxy designed for **API traffic**.

It sits between clients and backend services and handles common API-level concerns.

```text
Client
   │
   │ API Request
   ▼
┌─────────────────┐
│   API Gateway   │
│                 │
│ Authentication  │
│ Rate Limiting   │
│ Routing         │
│ TLS             │
│ Logging         │
└────────┬────────┘
         │
    ┌────┼─────┐
    ▼    ▼     ▼
  User  Order  Payment
 Service Service Service
````

---

## What Does an API Gateway Do?

Common responsibilities:

| Function                   | Purpose                                         |
| -------------------------- | ----------------------------------------------- |
| **Request Routing**        | Routes requests to the correct service          |
| **Authentication**         | Validates JWT, API keys, OAuth tokens, etc.     |
| **Rate Limiting**          | Controls how many requests a client can make    |
| **TLS Termination**        | Handles HTTPS                                   |
| **Request Transformation** | Modifies headers, parameters, or request format |
| **API Versioning**         | Routes `/v1` and `/v2` to different services    |
| **Logging & Monitoring**   | Collects API-level logs and metrics             |
| **Load Balancing**         | Distributes traffic across service instances    |

---

## Example

Client sends:

```text
GET /api/orders/123
Authorization: Bearer <token>
```

### API Gateway

```text
1. Receive request
       ↓
2. Validate authentication
       ↓
3. Check rate limit
       ↓
4. Route request to Order Service
       ↓
5. Add required internal information
       ↓
6. Forward request
```

```text
Client
   │
   ▼
API Gateway
   │
   │ Authentication ✓
   │ Rate Limit ✓
   │ Routing
   ▼
Order Service
```

The gateway handles common concerns so that backend services can focus mainly on **business logic**.

---

## Reverse Proxy vs API Gateway

| Feature                | Reverse Proxy   | API Gateway |
| ---------------------- | --------------- | ----------- |
| HTTP/HTTPS proxying    | ✓               | ✓           |
| TLS termination        | ✓               | ✓           |
| Basic routing          | ✓               | ✓           |
| Load balancing         | ✓               | ✓           |
| Authentication         | Limited         | ✓           |
| Rate limiting          | Basic           | Advanced    |
| API versioning         | Usually limited | ✓           |
| Request transformation | Limited         | ✓           |
| API-level monitoring   | Basic           | ✓           |

### Important

The boundary is **not strict**.

A reverse proxy such as **Nginx** can perform some API Gateway functions, while an API Gateway is essentially a reverse proxy with additional API-specific capabilities.

---

## API Gateway in Microservices

Without an API Gateway:

```text
Client ──→ User Service
Client ──→ Order Service
Client ──→ Payment Service
```

The client needs to know about multiple services.

With an API Gateway:

```text
                 ┌──────────────┐
Client ─────────►│ API Gateway  │
                 └──────┬───────┘
                        │
              ┌─────────┼─────────┐
              ▼         ▼         ▼
            User      Order     Payment
           Service   Service    Service
```

The client interacts with **one entry point**.

The gateway handles common concerns such as:

* Authentication
* Rate limiting
* Routing
* TLS
* Logging

---

## Common API Gateway Technologies

* **Kong**
* **AWS API Gateway**
* **Azure API Management**
* **Google Apigee**
* **Traefik**

---

## API Gateway vs Load Balancer

They solve different problems.

```text
                 Client
                    │
                    ▼
              API Gateway
                    │
          ┌─────────┼─────────┐
          ▼         ▼         ▼
       User LB    Order LB  Payment LB
          │         │         │
        Servers   Servers   Servers
```

**API Gateway** → Which **service** should receive the request?

**Load Balancer** → Which **server instance** should handle the request?

### Key Idea

> **API Gateway → Routes and manages API requests at the service level.**

> **Load Balancer → Distributes requests across instances of a service.**

```

### One correction to your original slide

Don't say:

> “The Feed Service does not need to handle authentication itself.”

That's too absolute. In a real system, **defense in depth** is common, and services may still perform authorization or validate trusted identity information. For interview notes, simply say:

> **The gateway handles common authentication concerns, while services remain responsible for business-level authorization.**

That's a more accurate mental model.
```
