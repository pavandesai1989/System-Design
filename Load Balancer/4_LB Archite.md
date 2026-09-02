

````markdown
# Load Balancers at Different Levels

In a large production system, traffic may be distributed at **multiple levels**.

For example, a company may have servers in **India and the US**, with multiple application servers and microservice instances in each region.

```text
                              USER
                                │
                                ▼
                    ┌─────────────────────┐
                    │   GLOBAL ROUTING    │
                    │                     │
                    │   "Which region?"   │
                    └──────────┬──────────┘
                               │
                       ┌───────┴───────┐
                       ▼               ▼
                    INDIA             US
                    REGION           REGION
                       │
                       ▼
              ┌─────────────────────┐
              │ REGIONAL LOAD       │
              │ BALANCER            │
              │                     │
              │ "Which app server?" │
              └──────────┬──────────┘
                         │
                  ┌──────┼──────┐
                  ▼      ▼      ▼
                App 1  App 2  App 3
                         │
                         ▼
              ┌─────────────────────┐
              │ INTERNAL LOAD       │
              │ BALANCING / SERVICE │
              │ DISCOVERY           │
              │                     │
              │ "Which instance?"   │
              └──────────┬──────────┘
                         │
                  ┌──────┼──────┐
                  ▼      ▼      ▼
                Post 1 Post 2 Post 3
````

---

## 1. Global Routing — Which Region?

Suppose the user is located in India.

```text
                         User
                          │
                          ▼
                Global Traffic Manager
                          │
              ┌───────────┼───────────┐
              ▼           ▼           ▼
           India          US        Europe
           Region        Region      Region
             ↑
          Selected
```

The global routing layer decides:

> **"Which region or data center should handle this user?"**

It does **not** normally decide which application server inside that region should handle the request.

It can use:

* **Latency**
* **Geographic location**
* **Health**
* **Traffic policies**
* **DNS-based routing**

Examples:

* AWS Route 53
* Cloudflare
* Azure Traffic Manager

### Key Idea

**Global routing = Choose the region.**

---

## 2. Regional Load Balancer — Which Application Server?

Once the request reaches the selected region, there may be hundreds of application servers.

```text
                    India Region
                         │
                         ▼
                Regional Load Balancer
                         │
              ┌──────────┼──────────┐
              ▼          ▼          ▼
           App 1       App 2       App 3
              │          │          │
             ...        ...        ...
              │
           App 100
```

The regional LB decides:

> **"Which healthy application server should handle this request?"**

For example:

```text
Request 1 → App 7
Request 2 → App 42
Request 3 → App 18
```

The decision can be based on algorithms such as:

* Round Robin
* Least Connections
* Weighted Routing
* Other load-based policies

This is the **most common Load Balancer** shown in system-design interviews.

Examples:

* Nginx
* HAProxy
* AWS Application Load Balancer (ALB)
* Azure Application Gateway

### Key Idea

**Regional LB = Choose the application server.**

---

## 3. Internal Load Balancing — Which Service Instance?

Suppose **App 7** needs to fetch the user's posts.

There may be multiple instances of the Post Service.

```text
App Server
    │
    │ Get Posts
    ▼
Internal Service Routing
    │
    ├────────→ Post Service 1
    ├────────→ Post Service 2
    └────────→ Post Service 3
```

The request is routed to an appropriate healthy instance.

For example:

```text
Request 1 → Post Service 2
Request 2 → Post Service 1
Request 3 → Post Service 3
```

This is commonly called:

* **Service-to-service load balancing**
* **Internal load balancing**
* **Service discovery + load balancing**

Technologies that can provide this functionality include:

* Envoy
* Service meshes such as Istio
* Internal cloud load balancers
* Kubernetes Services

### Key Idea

**Internal load balancing = Choose the service instance.**

---

# The Three Levels

The easiest way to remember this is through three questions:

```text
                         USER REQUEST
                              │
                              ▼
                 ┌────────────────────────┐
                 │ 1. WHICH REGION?       │
                 │    Global Routing      │
                 └────────────┬───────────┘
                              │
                              ▼
                 ┌────────────────────────┐
                 │ 2. WHICH APP SERVER?   │
                 │    Regional LB         │
                 └────────────┬───────────┘
                              │
                              ▼
                         App Server
                              │
                              ▼
                 ┌────────────────────────┐
                 │ 3. WHICH SERVICE       │
                 │    INSTANCE?           │
                 │    Internal Routing    │
                 └────────────┬───────────┘
                              │
                              ▼
                       Microservice
```

### ⭐ Remember

| Level        | Main Question           | Typical Technology                          |
| ------------ | ----------------------- | ------------------------------------------- |
| **Global**   | Which region?           | Route 53, Cloudflare, Azure Traffic Manager |
| **Regional** | Which app server?       | Nginx, HAProxy, AWS ALB                     |
| **Internal** | Which service instance? | Envoy, Kubernetes Service, Service Mesh     |

---

# Important Interview Point

> **Not every system needs all three levels.**

Start with the simplest architecture:

```text
Client
   │
   ▼
Load Balancer
   │
   ├──→ App Server 1
   ├──→ App Server 2
   └──→ App Server 3
```

Add **global routing** when the system needs multiple regions or data centers.

Add **internal load balancing/service discovery** when the system has multiple instances of backend services or microservices.

### Final Mental Model

```text
GLOBAL ROUTING
      ↓
Which REGION?
      ↓
REGIONAL LOAD BALANCER
      ↓
Which APP SERVER?
      ↓
INTERNAL SERVICE ROUTING
      ↓
Which SERVICE INSTANCE?
```

> **Global → Region**
> **Regional → Application Server**
> **Internal → Service Instance**

```

This version is better for interviews because it clearly separates **global traffic management** from the traditional **regional load balancer**, while still giving you the three-level mental model.
```
