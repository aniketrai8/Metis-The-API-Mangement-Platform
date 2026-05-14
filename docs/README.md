# Mentis — The API Mangement System

Mentis is a production-grade internal API management platform designed to centralize authentication, traffic management, observability, and AI-assisted developer tooling for distributed backend services.

The platform acts as a resilient gateway layer between clients and internal microservices, helping engineering teams improve security, system reliability, monitoring, and API maintainability.

---

## Why Mentis?

As backend ecosystems grow, organizations often face recurring operational problems:

- Inconsistent authentication across services
- Lack of centralized traffic control
- Cascading failures during traffic spikes
- Poor observability into API performance
- Outdated or incomplete API documentation
- Fragmented monitoring across teams

NexusGate aims to solve these issues through a centralized, production-oriented gateway architecture.

---

## Core Features

### API Gateway & Routing
- Centralized request routing using Spring Cloud Gateway
- Reactive non-blocking request handling with WebFlux
- Dynamic route management for internal services

### Authentication & Security
- JWT validation at the gateway layer
- API Key-based access control
- Team-based RBAC (Admin / Developer / Viewer)

### Distributed Rate Limiting
- Redis-backed token bucket rate limiting
- Protection against traffic surges and abusive consumers
- Configurable replenish rates and burst capacities

### Observability & Monitoring
- Real-time API metrics collection
- p95 / p99 latency tracking
- Throughput and error rate monitoring
- Async request logging pipeline

### AI-Assisted Documentation
- AI-generated API documentation from real request/response traffic
- Automatic endpoint summarization
- Payload structure inference for faster integrations

---

## Tech Stack

| Layer | Technology |
|---|---|
| Backend | Java 17, Spring Boot 3 |
| Gateway | Spring Cloud Gateway (WebFlux) |
| Database | PostgreSQL |
| Cache / Rate Limiting | Redis |
| AI Integration | Ollama / Claude API |
| Frontend | React + TailwindCSS |
| Monitoring | Prometheus + Grafana |
| Containerization | Docker & Docker Compose |
| Build Tool | Gradle |

---

## High-Level Architecture

```text
Client Applications
        ↓
    NexusGate
(API Gateway Layer)
        ↓
 ┌───────────────┐
 │ Auth Filters  │
 │ Rate Limiter  │
 │ Logging Layer │
 │ AI Doc Engine │
 └───────────────┘
        ↓
 Internal Microservices
