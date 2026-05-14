# Mendis - AI-Enhanced API Gateway & Developer Platform

---

## Document Information
- **Project Name:** Mentis
- **Timeline:** 8 Weeks (6 weeks development + 2 weeks integration & AI tuning)
- **Technology Stack:** Java 17, Spring Boot 3.x, Spring Cloud Gateway (Reactive/WebFlux), Redis, PostgreSQL, Docker

---

## 1. Project Overview

### 1.1 Purpose
NexusGate is a production-grade internal API Management Platform designed to centralize security, traffic control, and observability for microservices. It acts as a resilient "entry point" that protects internal services from surges while providing developers with AI-automated documentation and real-time system metrics.

### 1.2 Problem Statement
As internal service ecosystems grow, organizations face fragmented infrastructure:
- **Security Inconsistency:** Each service manually implements its own auth, leading to leaks.
- **Service Fragility:** No centralized way to throttle traffic, leading to cascading failures.
- **Stale Documentation:** API docs are rarely updated, causing integration delays.
- **Monitoring Gaps:** No unified view of latency (p99) or throughput across different team services.

### 1.3 Solution
NexusGate provides a high-performance **Reactive Gateway** that:
- **Centralizes Auth:** Validates JWTs and API Keys at the edge.
- **Ensures Resilience:** Implements Redis-backed distributed rate limiting.
- **Automates Docs:** Uses AI to infer API schemas from live traffic samples.
- **Provides Observability:** Delivers a dashboard for system metrics and async request logging.

### 1.4 Target Users
- **Admin:** Manages service registration, global rate limits, and views aggregate team metrics.
- **Developer:** Registers their team's services, generates AI documentation, and monitors their specific API health.
- **Viewer:** Stakeholders who need to view system uptime and usage reports without modification rights.

---

## 2. Technical Stack

### 2.1 Core Infrastructure
- **Framework:** Spring Boot 3.x with **Spring Cloud Gateway**.
- **Engine:** **Project Reactor (WebFlux)** for non-blocking, high-concurrency request handling.
- **Build Tool:** Gradle.

### 2.2 Data & Performance
- **Primary Database:** PostgreSQL (Metadata, Service Registry, Teams).
- **Cache & Rate Limiting:** **Redis** (Distributed `RedisRateLimiter` using the Token Bucket algorithm).
- **AI Integration:** **Claude API** for semantic analysis of request/response payloads.

### 2.3 DevOps & Observability
- **Deployment:** Docker & Docker-Compose.
- **Logging:** Async pipeline using an in-memory executor for non-blocking persistence.
- **API Docs:** Swagger/OpenAPI.

---

## 3. System Architecture

### 3.1 Reactive Gateway Flow
Client Request → Gateway Filter Chain → JWT/API Key Auth → Redis Rate Limiter → Health Check Filter → Target Microservice

### 3.2 Async Logging Pipeline
1. **Gateway:** Captures request/response metadata.
2. **Event:** Publishes an internal async event.
3. **Consumer:** In-memory async executor picks up the event.
4. **Persistence:** Batch writes to PostgreSQL.

---

## 4. Entity Design & Database Schema

### 4.1 Key Entities
- **User:** Stores credentials and **Team-based RBAC** roles (Admin, Developer, Viewer).
- **ServiceInstance:** Stores `service-name`, `base-url`, and `health-endpoint`.
- **ApiKey:** Linked to a team/user with specific scopes and rate-limit metadata.
- **ApiLog:** Stores request/response samples for AI analysis (Async persistence).
- **MetricSnapshot:** Aggregated data for p95/p99 latency, throughput, and error rates.

---

## 5. Feature Specifications

### 5.1 Redis-Backed Rate Limiting
- **Implementation:** Utilize `Spring Cloud Gateway` native `RedisRateLimiter`.
- **Strategy:** Token Bucket algorithm for distributed environments.
- **Configuration:** Users define `replenishRate` and `burstCapacity` per API Key in the dashboard.

### 5.2 Semi-Static Service Registry
- **Management:** Admins manually input service details (Name, URL, Health Path).
- **Health Check:** Background reactive task pings the `health-endpoint` every 30 seconds.
- **Routing:** Gateway dynamically enables/disables routes based on health status.

### 5.3 AI Documentation Generator
- **Trigger:** Manual trigger by Admin/Developer in the management dashboard.
- **Input:** System fetches the last $N$ successful request/response samples for the specific service.
- **Output:** AI summarizes endpoints, infers data types, and generates OpenAPI-style documentation.

---

## 6. API Specifications (Baseline)

| Method | Endpoint | Description | Role |
|---|---|---|---|
| POST | `/api/auth/login` | Returns JWT for Dashboard access | Public |
| POST | `/api/admin/services` | Register a new internal service | Admin |
| GET | `/api/admin/health` | View status of all registered services | Admin/Dev |
| POST | `/api/keys/generate` | Issue a new API Key for a service | Admin/Dev |
| GET | `/api/metrics/{serviceId}` | Get p99 latency and throughput data | All |
| POST | `/api/ai/generate-docs/{id}` | Trigger AI documentation generation | Admin/Dev |

---

## 7. Implementation Plan

- **Week 1-2:** Project initialization, Docker setup, and Reactive Gateway Security filters.
- **Week 3-4:** Redis Rate Limiting integration and Semi-Static Service Registry with health checks.
- **Week 5-6:** Async Logging pipeline and AI Documentation Generator integration.
- **Week 7-8:** Integration Testing (Testcontainers) and Performance Hardening (Latency < 30ms).

---

## 8. Success Metrics (Production Grade)
- **Performance:** Gateway adds < 30ms latency to the total request time.
- **Reliability:** 100% of rate-limit violations correctly blocked at the edge.
- **Observability:** 95%+ of requests are logged asynchronously without dropping events.
- **Automation:** AI correctly identifies 80% of API fields from traffic samples.
  
