---

# Architecture Decision Matrix (ADM)

# Purpose

This document captures all significant architectural decisions made during the design of the Distributed URL Management System.

Each decision includes:

* The chosen approach
* Alternatives considered
* Rationale
* Trade-offs
* Whether the decision is final or expected to evolve

Architectural decisions should only change through a new ADR (Architecture Decision Record).

---

# Decision Matrix

## 1. System Architecture

| Decision              | Choice                        |
| --------------------- | ----------------------------- |
| Architecture Style    | Microservices                 |
| Design Style          | Domain-Driven Modularization  |
| Service Communication | REST + Event-Driven Messaging |
| Deployment            | Containerized                 |
| Cloud Native          | Yes                           |

### Alternatives

- Monolith
- Modular Monolith
- SOA

### Why?

This project is intended to demonstrate distributed systems, independent deployment, scalability, resilience, and inter-service communication.

**Status:** Frozen

---

## 2. Programming Stack

| Decision          | Choice             |
| ----------------- | ------------------ |
| Language          | Java 17            |
| Framework         | Spring Boot 3.x    |
| Build Tool        | Maven              |
| Project Structure | Multi-module Maven |

### Alternatives

- Gradle
- Kotlin
- Quarkus
- Micronaut

### Why?

Java + Spring Boot remains one of the most common enterprise backend stacks and aligns with the project's learning objectives.

**Status:** Frozen

---

## 3. API Style

| Decision               | Choice            |
| ---------------------- | ----------------- |
| External APIs          | REST              |
| Internal Communication | REST              |
| Data Format            | JSON              |
| API Documentation      | OpenAPI / Swagger |

### Alternatives

- GraphQL
- gRPC

### Why?

REST is widely adopted, easy to test, and sufficient for the current scope. We may discuss gRPC as a future enhancement for latency-sensitive internal communication.

**Status:** Frozen

---

## 4. Service Discovery

| Decision          | Choice                       |
| ----------------- | ---------------------------- |
| Local Development | Docker Compose Service Names |
| Kubernetes        | Kubernetes DNS               |
| Registry          | None                         |

### Alternatives

- Eureka
- Consul
- ZooKeeper

### Why?

Modern Kubernetes deployments rely on native service discovery. Introducing Eureka would add complexity without meaningful benefit for this project.

**Status:** Frozen

---

## 5. API Gateway

| Decision       | Choice               |
| -------------- | -------------------- |
| Gateway        | Spring Cloud Gateway |
| Authentication | JWT Validation       |
| Routing        | Dynamic Routes       |
| Rate Limiting  | Gateway + Redis      |

### Alternatives

- NGINX Only
- Kong
- Traefik
- Envoy

### Why?

Spring Cloud Gateway integrates well with Spring Boot, supports filters, rate limiting, and security, making it a strong fit for this ecosystem.

**Status:** Frozen

---

## 6. Load Balancing

| Decision   | Choice                             |
| ---------- | ---------------------------------- |
| Local      | NGINX                              |
| Kubernetes | Ingress Controller                 |
| Cloud      | External Load Balancer (e.g., ALB) |

### Alternatives

- HAProxy
- Traefik

### Why?

NGINX is simple and widely understood for local environments. Kubernetes Ingress represents the production deployment model.

**Status:** Frozen

---

## 7. Database

| Decision           | Choice                                    |
| ------------------ | ----------------------------------------- |
| Primary Database   | PostgreSQL                                |
| Database Model     | Relational                                |
| Database Ownership | Database per Service (logical separation) |

### Alternatives

- MySQL
- MongoDB
- Cassandra

### Why?

The project requires transactional consistency, indexing, constraints, and mature tooling. PostgreSQL is an excellent fit.

**Status:** Frozen

---

## 8. Cache

| Decision | Choice                            |
| -------- | --------------------------------- |
| Cache    | Redis                             |
| Usage    | URL Cache, Rate Limiter, Sessions |

### Alternatives

- Hazelcast
- Caffeine
- Memcached

### Why?

Redis provides low-latency access, supports distributed data structures, and is ideal for caching and rate limiting.

**Status:** Frozen

---

## 9. Messaging

| Decision      | Choice                           |
| ------------- | -------------------------------- |
| Broker        | Apache Kafka                     |
| Communication | Event-Driven                     |
| Consumers     | Analytics, Notification (future) |

### Alternatives

- RabbitMQ
- ActiveMQ

### Why?

Kafka is designed for high-throughput event streaming and aligns with modern event-driven architectures.

**Status:** Frozen

---

## 10. Security

| Decision           | Choice                            |
| ------------------ | --------------------------------- |
| Authentication     | JWT                               |
| Authorization      | Role-Based Access Control (RBAC)  |
| Password Storage   | BCrypt                            |
| Transport Security | HTTPS (outside local development) |

### Alternatives

- OAuth2 Only
- Session-Based Authentication

### Why?

JWT enables stateless authentication and scales well across multiple services.

**Status:** Frozen

---

## 11. Observability

| Decision   | Choice                         |
| ---------- | ------------------------------ |
| Metrics    | Prometheus                     |
| Dashboards | Grafana                        |
| Logging    | Loki                           |
| Tracing    | OpenTelemetry + Tempo (future) |

### Alternatives

- ELK Stack
- Zipkin

### Why?

The Prometheus/Grafana/Loki ecosystem integrates well with Kubernetes and provides comprehensive observability.

**Status:** Evolving (tracing to be added later)

---

## 12. CI/CD

| Decision         | Choice             |
| ---------------- | ------------------ |
| Public CI        | GitHub Actions     |
| Enterprise CI    | Jenkins            |
| Containerization | Docker             |
| Orchestration    | Kubernetes (later) |

### Alternatives

- GitLab CI
- Azure DevOps

### Why?

GitHub Actions is ideal for repository-level validation, while Jenkins showcases enterprise CI/CD workflows.

**Status:** Frozen

---

## 13. Frontend

| Decision       | Choice |
| -------------- | ------ |
| Framework      | React  |
| Communication  | REST   |
| Authentication | JWT    |

### Alternatives

- Angular
- Vue.js

### Why?

React is widely adopted, pairs well with REST APIs, and keeps the frontend lightweight.

**Status:** Frozen

---

## 14. Deployment

| Decision       | Choice          |
| -------------- | --------------- |
| Local          | Docker Compose  |
| Production     | Kubernetes      |
| Infrastructure | Container-Based |

### Alternatives

- Virtual Machines
- Bare Metal

### Why?

This progression reflects common enterprise deployment practices while keeping local development straightforward.

**Status:** Frozen

---

# One Decision I Think We Should Refine

There's one architectural decision I'd like to make more precise before we freeze the document:

## Database per Service

Many examples simply state:

```text
Auth → PostgreSQL
URL → PostgreSQL
Analytics → PostgreSQL
```

I'd recommend documenting it more explicitly as:

```text
One PostgreSQL server
        │
        ├── auth_db
        ├── url_db
        ├── analytics_db
        └── admin_db
```

### Why?

For a portfolio project, this offers a good balance:

- Demonstrates **database-per-service ownership**.
- Keeps local development simple with a single PostgreSQL container.
- Makes it easy to evolve to separate database instances in Kubernetes or cloud deployments if needed.

This mirrors how many teams develop locally while preserving service boundaries.

---

# Service Boundary (Frozen)

I also suggest we officially freeze our service landscape:

```text
Internet
        │
        ▼
External Load Balancer
        │
        ▼
API Gateway
        │
        ▼
────────────────────────────────────────
Authentication Service
URL Service
Analytics Service
Admin Service
────────────────────────────────────────
        │
        ├── PostgreSQL
        ├── Redis
        └── Kafka
```

We'll defer the **Notification Service** until the phase where we introduce Kafka consumers, ensuring every service we add has a clear, active responsibility.

---
