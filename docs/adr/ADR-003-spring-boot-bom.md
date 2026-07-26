# ADR-003: Spring Boot Dependency Management

- **Status:** Accepted
- **Date:** 2026-07-26

## Context

Every backend module depends on Spring Boot libraries.

The project needs a consistent way to manage dependency versions without duplicating version numbers across modules.

Two common approaches exist:

1. Inherit from `spring-boot-starter-parent`
2. Import the Spring Boot BOM using `dependencyManagement`

## Decision

The project imports the Spring Boot BOM.

```xml
<dependencyManagement>

    <dependencies>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-dependencies</artifactId>
            <version>${spring.boot.version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>

    </dependencies>

</dependencyManagement>
```

The project owns the parent POM while Spring Boot manages dependency versions.

## Consequences

### Advantages

- Full control over the parent POM.
- Centralized dependency versions.
- Easier integration with additional BOMs.
- Suitable for enterprise multi-module projects.
- Cleaner inheritance hierarchy.

### Disadvantages

- Slightly more Maven knowledge required.
- Requires explicit plugin management.

## Alternatives Considered

### spring-boot-starter-parent

Advantages:

- Simpler.
- Ideal for small applications.

Rejected because the project uses a production-style multi-module architecture and benefits from owning the parent POM.

### Manual dependency versions

Rejected because version duplication increases maintenance cost and the risk of inconsistencies across modules.
