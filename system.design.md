# system.design.md

## 🏗 System Design: Aegis API Gateway

This document describes the overall **system design** for the Aegis API Gateway, including its functional and non-functional goals, core architecture decisions, data & control flows, and deployment strategy.

---

## 🎯 Objectives

* Serve as a **reverse proxy gateway** that routes requests to backend microservices based on path or header matching.
* Central point for **TLS termination**, **rate limiting**, **caching**, **header manipulation**, and **distributed tracing injection**.
* Designed to be **cloud-native**, deployed on **AWS EKS (Kubernetes)**.

---

## ✅ Functional Overview

Refer to the [Function Requirements](./functional-requirements.md) document.

---

## 🚀 High-Level Architecture

Refer to the [High-Level Architecture](./architecture/high-level-diagram.md) document.

---

## 🧩 Internal Component Flow

1. **SSL/TLS Handler**

   * Accepts HTTPS connections, offloads TLS from backend.

2. **Router**

   * Looks up path/header rules to decide backend target.

3. **Rate Limiter & Throttler**

   * Applies per-route / per-client rate policies.

4. **Cache**

   * If enabled, checks for cached response to avoid backend hit.

5. **Header Manipulator**

   * Adds required headers (forwarded-for, tracing IDs, etc).

6. **Tracer Injector**

   * Propagates or starts new trace IDs.

7. **Dispatcher**

   * Makes HTTP request to backend, handles connection pooling.

8. **Observability Hooks**

   * Logs request, duration, cache hits, errors.

---

## ⚙️ Non-Functional Requirements

| Requirement         | Target / Strategy                                |
| ------------------- | ------------------------------------------------ |
| **Performance**     | <5ms overhead added by gateway (avg)             |
| **Availability**    | >=99.9%, multi-zone EKS deployment               |
| **Scalability**     | HPA on CPU/latency in Kubernetes                 |
| **Security**        | TLS 1.2+, minimal public surface, config secrets |
| **Observability**   | 100% request tracing, logs                       |
| **Configurability** | YAML-driven, hot-reload without downtime         |

---

## 🚢 Deployment Design

Refer the [Deployment Strategy](./decisions/ADR-004-deployment-strategy.md) document.

## 🔄 Sequence Flow Example

### Request Handling

```
Client
  |
  | HTTPS Request
  v
SSL/TLS Handler
  |
  v
Router (YAML Rules)
  |
  +--> Rate Limiter
  |
  +--> Cache Check
  |     |
  |     +-- HIT --> Return Response
  |
  +--> Header Manipulator & Tracer Injector
  |
  +--> Dispatch to Backend
  |
  +--> Cache Store if needed
  |
  +--> Log, Metrics, Trace
  |
  v
Client
```

---

## ⚡️ Error & Retry Handling

* Gateway itself will not perform retries unless specifically configured (to avoid masking failures).
