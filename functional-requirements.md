# 📜 Functional Requirements (MVP) — Aegis API Gateway

---

## 1️⃣ Routing

* The gateway **should be able to route requests based on HTTP path patterns**.
* It **shall support additional routing based on HTTP headers (e.g., `X-Feature-Flag`)**.
* It **shall forward requests to the configured upstream service based on routing rules**.

---

## 2️⃣ TLS / SSL Termination

* The gateway **shall terminate incoming HTTPS connections**, decrypting traffic at the gateway layer.
* It **shall use configurable TLS certificates and private keys from specified file paths**.

---

## 3️⃣ Rate Limiting & Throttling

* The gateway **shall enforce a maximum number of requests per second (rate limiting)** to protect backend services.
* It **shall also limit the total number of concurrent requests (throttling)** to avoid overloading upstreams.
* It **shall return HTTP 429 (Too Many Requests) when limits are exceeded.**

---

## 4️⃣ Caching

* The gateway **shall cache HTTP GET responses in an in-memory store**.
* It **shall allow configuring a default time-to-live (TTL) for cached entries**.
* It **shall allow enabling or disabling caching on a per-route basis**.

---

## 5️⃣ Header Modification

* The gateway **shall support adding specified HTTP headers to requests before forwarding**.
* It **shall support removing specified HTTP headers from incoming requests.**

---

## 6️⃣ Tracer Injection

* The gateway **shall inject a unique trace ID header into each request**, enabling end-to-end request tracing across microservices.
* The header name **shall be configurable (e.g., `X-Trace-ID`)**.

---

## ✅ MVP Acceptance Summary

| Capability          | Minimum Requirement                |
| ------------------- | ---------------------------------- |
| Routing             | Path & header-based forwarding     |
| TLS                 | HTTPS termination with cert config |
| Rate limiting       | Global RPS + burst control         |
| Throttling          | Max concurrent request control     |
| Caching             | In-memory GET response caching     |
| Header modification | Add/remove request headers         |
| Tracing             | Inject `X-Trace-ID` or similar     |

