# 📄 ADR-003: Caching Strategy for Aegis API Gateway (MVP)

## Status

Accepted

## Context

The Aegis API Gateway needs to reduce latency and backend load for repeated requests.
We evaluated different caching strategies:

* **In-memory caching at the gateway layer** using a simple TTL (time-to-live).
* **Distributed caching** (e.g., Redis) for shared state across replicas.
* **No caching**, letting upstreams or CDNs handle it.

For the MVP, we prioritized **simplicity, reduced operational overhead, and low latency**, without introducing external infrastructure.

## Decision

We decided to:

* ✅ Implement **basic in-memory caching** at the gateway layer.

* ✅ Support caching only for **GET requests on specified path patterns** (e.g., `/public/**`).

* ✅ Use a simple configurable **TTL (time-to-live)** per caching rule.

* ❌ Skip distributed caching (e.g., Redis) or advanced invalidation in the MVP to avoid external dependencies.

## Consequences

* Reduces response times for cacheable requests and protects backend services from repeated load.
* Simpler to implement, with no external cache system to manage.
* Limits scale-out caching: each gateway instance holds its own cache. In a multi-replica deployment, cache misses might be higher until consistent caching is introduced.
* Future ADRs can revisit introducing Redis or other shared caching backends, or support for ETag-based cache validation.

