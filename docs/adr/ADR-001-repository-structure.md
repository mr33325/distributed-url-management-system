# ADR-001: Repository Structure

- **Status:** Accepted
- **Date:** 2026-07-26

## Context

The Distributed URL Management System is a long-term project intended to demonstrate production-grade backend engineering practices. The repository will contain multiple backend microservices, reusable platform libraries, infrastructure configuration, monitoring setup, load-testing scripts, project documentation, and a React-based frontend.

The repository structure should:

- Separate frontend and backend technologies.
- Support independent build systems.
- Scale as new services and libraries are added.
- Be easy to navigate for contributors and interviewers.
- Avoid future restructuring.

## Decision

The repository follows a monorepo structure.

```
distributed-url-management-system/

├── backend/
├── frontend/
├── infrastructure/
├── docs/
├── monitoring/
├── load-tests/
├── scripts/
└── .github/
```

Backend contains all Java-related code.

Frontend contains the React application.

Infrastructure contains Docker, Kubernetes, Terraform, NGINX and deployment-related assets.

Documentation, monitoring configuration and load-testing assets are isolated into dedicated directories.

## Consequences

### Advantages

- Clear separation of concerns.
- Independent frontend/backend development.
- Easier CI/CD configuration.
- Scales well as the project grows.
- Easy for new developers to understand.

### Disadvantages

- Slightly deeper directory hierarchy.
- Requires understanding of multiple build systems (Maven and npm).

## Alternatives Considered

### Flat repository

```
gateway-service/
auth-service/
frontend/
docker/
```

Rejected because the root becomes cluttered as the number of services grows.

### Multiple repositories

Each microservice in its own Git repository.

Rejected because this project aims to demonstrate a production-style monorepo with shared libraries and centralized infrastructure.
