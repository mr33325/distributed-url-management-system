# Context View

## Purpose

The Context View presents the Distributed URL Management System as a single software system and illustrates how it interacts with users and external systems.

This view intentionally hides all internal implementation details such as microservices, databases, caches, and messaging infrastructure.

## Actors

### Anonymous User

Creates and accesses shortened URLs.

### Authenticated User

Manages URLs and views analytics.

### Administrator

Monitors the platform and manages users.

## External Systems

### React Web Application

Provides the web-based user interface.

### Email Provider

Used for email verification and future notifications.

### Grafana

Provides operational dashboards and system monitoring.

## Scope

This view corresponds to **C4 Level 1 (System Context)**.

Detailed service interactions are documented in the Container View.
