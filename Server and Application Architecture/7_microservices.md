

````markdown id="k7p4m2"
# Microservices Architecture

A **microservices architecture** splits an application into multiple **independently deployable services**.

Each service owns a specific **business capability** and is responsible for its data.

```text
                         Client
                            │
                            ▼
                     ┌──────────────┐
                     │ API Gateway  │
                     └──────┬───────┘
                            │
              ┌─────────────┼─────────────┐
              ▼             ▼             ▼
        User Service   Post Service   Feed Service
              │             │             │
              ▼             ▼             ▼
         User Data      Post Data      Feed Cache
````

Examples of services:

* **User Service** → users, profiles, authentication
* **Post Service** → posts, likes, comments
* **Feed Service** → feed generation and ranking
* **Media Service** → image/video processing

---

## How Services Communicate

Services communicate over the network.

### 1. Synchronous Communication

The caller waits for a response.

Examples:

* REST
* gRPC

```text
Feed Service
     │
     │ Request
     ▼
User Service
     │
     │ Response
     ▼
Feed Service
```

Use when the caller **needs the result immediately**.

---

### 2. Asynchronous Communication

The caller sends a message/event and does not wait for the processing to finish.

Examples:

* Message queues
* Kafka
* SQS

```text
Post Service
     │
     │ Event
     ▼
 Message Queue
     │
     ├──────► Feed Service
     │
     └──────► Notification Service
```

Use when the work **can happen later**.

---

### 3. Event-Driven Communication

A service publishes an event, and multiple services independently react to it.

```text
                 Post Created
                      │
                      ▼
                Event / Topic
                /     |      \
               ▼      ▼       ▼
            Feed   Notification Analytics
           Service    Service    Service
```

This reduces direct coupling between services.

---

# Database Per Service

A key principle of microservices is:

> **Each service owns its data.**

```text
User Service  ─────► User Data
Post Service  ─────► Post Data
Feed Service  ─────► Feed Cache
```

Other services should **not directly access another service's database**.

For example:

```text
Feed Service
      │
      │ ✓ API call
      ▼
User Service
      │
      ▼
User Data
```

Not:

```text
Feed Service ──────X──────► User Database
```

This keeps services **loosely coupled** and allows them to evolve independently.

---

# Monolith vs Microservices

| Dimension                  | Monolith                         | Microservices                     |
| -------------------------- | -------------------------------- | --------------------------------- |
| **Codebase**               | One application                  | Multiple services                 |
| **Deployment**             | Deploy entire application        | Deploy services independently     |
| **Scaling**                | Usually scale entire application | Scale individual services         |
| **Failure isolation**      | Lower                            | Better                            |
| **Data consistency**       | Easier                           | More complex                      |
| **Communication**          | In-process calls                 | Network calls                     |
| **Debugging**              | Simpler                          | Distributed tracing required      |
| **Technology choice**      | Usually shared stack             | Services can use different stacks |
| **Operational complexity** | Lower                            | Higher                            |
| **Team ownership**         | Shared application               | Teams can own individual services |

---

# Why Use Microservices?

Microservices become useful when the application has requirements such as:

### 1. Independent Scaling

```text
Feed Service  → High traffic → 20 instances

User Service  → Lower traffic → 3 instances
```

Only the services that need more capacity are scaled.

### 2. Independent Deployment

A team can deploy the Feed Service without deploying the entire application.

### 3. Fault Isolation

A failure in one service does not necessarily bring down the entire system.

```text
Feed Service ❌

User Service ✓
Post Service ✓
Payment Service ✓
```

### 4. Team Ownership

Different teams can own different services and work independently.

### 5. Technology Flexibility

Different services can use technologies that fit their requirements.

---

# When Should You Choose Microservices?

Choose microservices when there is a **clear need for independent services**.

Typical signals:

* Independent scaling requirements
* Independent deployment requirements
* Multiple teams need clear ownership
* Strong service boundaries exist
* Fault isolation is important
* Different services have different technology requirements

---

# When Should You Choose a Monolith?

A monolith is often better when:

* The product is new
* Requirements are still changing
* The team is relatively small
* The domain is simple
* Fast development is more important than independent scaling
* Service boundaries are not yet clear

A good approach is:

```text
Start Simple
     │
     ▼
  Monolith
     │
     │ Growth / clear boundaries
     ▼
Modular Monolith
     │
     │ Need independent scaling/deployment
     ▼
Microservices
```

### Key Idea

> **Monolith → Simpler to build and operate**

> **Microservices → Better independent scaling, deployment, and ownership, but much higher operational complexity**

### Interview Tip

Do not say:

> "Microservices are better than monoliths."

Instead say:

> **"I would start with a monolith unless there is a clear requirement for independent scaling, deployment, team ownership, or fault isolation."**

```

### One thing I'd emphasize

For your SDE3 interview notes, the **most important concept in this entire section is not the list of technologies**. It's this:

**Monolith:** modules communicate in-process and deploy together.  
**Microservices:** services communicate over the network and deploy independently.

And then the trade-off:

**Independent scaling/deployment ↔ distributed-system complexity.**

That is the core interview discussion.
```
