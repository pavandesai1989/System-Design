

````markdown
# Web Servers, Application Servers, and Database Servers

In a production system, different servers usually handle different responsibilities.

This separation allows each layer to be **optimized, scaled, and managed independently**.

---

## 1. Web Server

Examples: **Nginx, Apache, Caddy**

### Role

Handles HTTP/HTTPS connections and forwards application requests to the application server.

### Responsibilities

- Accept incoming HTTP/HTTPS connections
- Serve static files such as HTML, CSS, JS, and images
- Forward dynamic requests to the application server
- TLS termination
- Compression
- Connection management

```text
Client
   │
   │ HTTPS
   ▼
┌──────────────┐
│  Web Server  │
│    Nginx     │
└──────┬───────┘
       │
       │ HTTP / HTTPS
       ▼
Application Server
````

---

## 2. Application Server

Examples: **Node.js, Django, Spring Boot, ASP.NET**

### Role

Executes the application's **business logic**.

### Responsibilities

* Route requests to the correct handler
* Validate input
* Execute business logic
* Authenticate and authorize users
* Query databases and caches
* Build the response

```text
Web Server
     │
     ▼
┌────────────────────┐
│ Application Server │
│                    │
│ Business Logic     │
│ Authentication     │
│ Validation         │
│ API Processing     │
└─────────┬──────────┘
          │
          ▼
     Database/Cache
```

---

## 3. Database Server

Examples: **PostgreSQL, MySQL, MongoDB**

### Role

Stores and retrieves application data.

### Responsibilities

* Execute queries
* Store and retrieve data
* Maintain data integrity
* Manage indexes
* Handle concurrent access
* Transactions
* Replication and backups

---

## How They Work Together

Example request:

```text
GET /api/users/123
```

```text
Client
  │
  │ HTTPS
  ▼
┌──────────────┐
│ Web Server   │
│   Nginx      │
└──────┬───────┘
       │
       │ Forward request
       ▼
┌──────────────────┐
│ Application      │
│ Server            │
│                  │
│ Validate request │
│ Check auth       │
│ Business logic   │
└────────┬─────────┘
         │
         │ Query
         ▼
┌──────────────────┐
│ Database         │
│ PostgreSQL       │
└────────┬─────────┘
         │
         │ User data
         ▼
Application Server
         │
         │ JSON response
         ▼
Web Server
         │
         │ HTTPS
         ▼
       Client
```

### Request Flow

1. **Web Server** receives the HTTPS request.
2. **Web Server** forwards the request to the application server.
3. **Application Server** authenticates the request and executes business logic.
4. **Application Server** queries the database if required.
5. **Database** returns the requested data.
6. **Application Server** creates the response.
7. **Web Server** sends the response back to the client.

---

## Why Separate These Layers?

Each layer can be **scaled independently**.

```text
                 Load Balancer
                       │
             ┌─────────┼─────────┐
             ▼         ▼         ▼
          Web/App    Web/App    Web/App
          Server     Server     Server
             │         │         │
             └─────────┼─────────┘
                       ▼
                    Database
```

For example:

* More application traffic → add application servers
* More static content traffic → scale web servers/CDN
* More database load → optimize or scale the database

### Key Idea

> **Web Server → handles HTTP traffic**
>
> **Application Server → handles business logic**
>
> **Database Server → stores and retrieves data**

This separation is a fundamental building block of production architectures.

```

**One important distinction to remember:** a web server and application server *can* run on the same machine, especially in smaller systems. Separating them is an architectural choice, not a requirement.
```
