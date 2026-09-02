

````markdown
# Monolith Architecture

A **monolith** is an application where all major functionality is built and deployed as a **single deployable unit**.

For example:

```text
my-application/
├── users/
├── posts/
├── feed/
├── media/
├── notifications/
├── search/
└── shared/
````

All modules are part of the same application and are typically deployed together.

---

## How It Works

```text
                    Client
                       │
                       ▼
                ┌──────────────┐
                │   Monolith   │
                │              │
                │ Users        │
                │ Posts        │
                │ Feed         │
                │ Media        │
                │ Search       │
                │ Notifications│
                └──────┬───────┘
                       │
                       ▼
                   Database
```

The modules usually share:

* Same codebase
* Same deployment lifecycle
* Same application process
* Same database
* In-process communication between modules

### Important

A monolith **can run on multiple servers**:

```text
                  Load Balancer
                 /      |      \
                ▼       ▼       ▼
           Monolith  Monolith  Monolith
           Instance  Instance  Instance
                \       |       /
                 \      |      /
                    Database
```

It is still a monolith because all instances run the **same deployable application**.

---

## Advantages

| Advantage                      | Explanation                                                         |
| ------------------------------ | ------------------------------------------------------------------- |
| **Simple development**         | One codebase and one build process                                  |
| **Simple deployment**          | Deploy one application                                              |
| **Easy debugging**             | Modules are in the same process                                     |
| **Easy testing**               | End-to-end testing is simpler                                       |
| **Low communication overhead** | Modules communicate through function calls instead of network calls |
| **Simple transactions**        | A shared database can support transactions across modules           |

---

## When Is a Monolith a Good Choice?

A monolith is often a good choice when:

* The product is still evolving
* The team is relatively small
* Requirements are changing frequently
* Simplicity is more important than independent scaling
* There is no strong need to deploy services independently

A monolith is **not automatically a bad architecture**. Many large systems successfully operate as monoliths.

---

## Limitations

As the application grows, a monolith can become difficult to manage.

### 1. Large Codebase

```text
Users
Posts
Feed
Payments
Search
Media
Notifications
       ↓
  One Codebase
```

Changes in one area can affect other areas.

### 2. Independent Scaling Is Difficult

If the **Feed** module needs more resources but **Users** does not:

```text
Monolith
   │
   ├── Users
   ├── Feed  ← needs more capacity
   └── Search
```

You may have to scale the **entire application**, rather than only the Feed functionality.

### 3. Deployment Coupling

A change to one module usually requires deploying the entire application.

### 4. Technology Coupling

The modules typically share the same runtime and technology stack, making it harder to use different technologies for different components.

---

## Monolith → Microservices

A common evolution is:

```text
Monolith
    │
    │ Application grows
    ▼
Modular Monolith
    │
    │ Some modules need
    │ independent scaling
    ▼
Microservices
```

But **do not split into microservices just because the application is growing**.

The decision should be based on requirements such as:

* Independent scaling
* Independent deployment
* Team ownership
* Different technology requirements
* Strong service boundaries

### Key Idea

> **Monolith = one deployable application containing multiple modules.**

> **Monolith does not mean one server.**

```

This is enough for your notes. The **most important correction** is to separate the concepts:

**Monolith = deployment boundary**  
**Single server = infrastructure/hosting choice**

That distinction is commonly tested in system-design interviews.
```
