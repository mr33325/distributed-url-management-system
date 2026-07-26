# ADR-002: Backend Maven Module Hierarchy

- **Status:** Accepted
- **Date:** 2026-07-26

## Context

The backend consists of multiple Spring Boot microservices and reusable Java libraries.

The build system should:

- Centralize dependency versions.
- Support reusable platform libraries.
- Minimize duplication.
- Scale as additional modules are introduced.
- Keep module relationships easy to understand.

## Decision

The backend is organized as a Maven multi-module project.

```
backend/

pom.xml

common/

platform/
    pom.xml

services/
    pom.xml

testing/
    pom.xml
```

The backend parent POM is the root of all Java modules.

The following module types are used:

### Parent Modules

- backend
- platform
- services
- testing

Packaging:

```
pom
```

### Library Modules

Examples:

- common
- logging-starter
- jwt-starter
- redis-starter

Packaging:

```
jar
```

### Application Modules

Examples:

- gateway-service
- auth-service
- url-service

Packaging:

```
jar
```

with Spring Boot support.

## Consequences

### Advantages

- Centralized dependency management.
- Simplified builds.
- Clean inheritance hierarchy.
- Easy module expansion.
- Minimal duplication.

### Disadvantages

- More POM files than a single-module project.
- Requires understanding Maven inheritance.

## Alternatives Considered

### Single Spring Boot application

Rejected because it would not demonstrate a microservice architecture.

### Flat Maven module hierarchy

```
backend/

common/
gateway-service/
url-service/
logging-starter/
```

Rejected because logical grouping into platform, services and testing improves maintainability as the project grows.
