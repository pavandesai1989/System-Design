

````markdown
## Health Checks and Failure Detection

A Load Balancer continuously checks backend servers to determine whether they are **healthy and able to handle requests**.

### How It Works

```text
                Health Check
                    │
                    ▼
              Load Balancer
              /      |      \
             ▼       ▼       ▼
           S1       S2       S3
          200 OK   200 OK   Timeout
           ✓         ✓         ✗
                             ↓
                      Remove S3
                      from rotation
````

The LB periodically sends a health-check request to each server.

Example:

```text
GET /health

S1 → 200 OK   ✓
S2 → 200 OK   ✓
S3 → Timeout  ✗
```

If a server fails the configured number of checks, the LB marks it **unhealthy** and stops sending traffic to it.

### Types of Health Checks

| Type | What it checks                             | Example                  |
| ---- | ------------------------------------------ | ------------------------ |
| TCP  | Can the server accept connections?         | Connect to port 8080     |
| HTTP | Can the application respond?               | `GET /health` → `200 OK` |
| Deep | Can the application and dependencies work? | DB, cache, disk          |

### Shallow vs Deep

**Shallow health check**

```text
GET /health → 200 OK
```

Checks mainly whether the application is running.

**Deep health check**

```text
Application
   ├── Database ✓
   ├── Cache    ✓
   └── Disk     ✓
        ↓
      200 OK
```

Checks whether the application is actually ready to serve requests.

> **Shallow = Is the process running?**
>
> **Deep = Can the application actually work?**

### Server Recovery

When an unhealthy server becomes healthy again:

```text
S3 → unhealthy
       ↓
   server fixed
       ↓
GET /health → 200 OK
GET /health → 200 OK
GET /health → 200 OK
       ↓
S3 → healthy
       ↓
Added back to rotation
```

Multiple consecutive successful checks help prevent **flapping**—repeatedly moving a server between healthy and unhealthy states.

### Key Idea

> **Health checks ensure that the Load Balancer sends traffic only to servers that are currently able to serve requests.**

```

This is the level I'd recommend for your **SDE3 system-design notes**. You don't need the 5-second/10-second intervals and detailed thresholds unless you're specifically discussing health-check configuration.
```
