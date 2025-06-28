# 📄 ADR-002: SSL Termination Strategy for Aegis API Gateway (MVP)

## Status

Accepted

## Context

The Aegis API Gateway needs to handle client traffic securely.
We evaluated two common approaches for TLS (SSL) termination:

1. **Terminate TLS at the gateway layer.**
   The gateway accepts HTTPS connections, decrypts traffic, and forwards plain HTTP requests internally to backend services.

2. **Pass-through TLS termination.**
   The gateway simply forwards encrypted traffic, and upstream services terminate TLS.

For the MVP, the primary goals were **centralized security, simpler certificates management, and minimizing backend complexity**.

## Decision

We decided to:

* ✅ **Terminate TLS at the Aegis API Gateway layer.**
  The gateway listens on HTTPS (port 8443), decrypts incoming traffic, and forwards to upstream services over HTTP.

* The gateway uses configurable certificates and private keys, supporting **TLS 1.2 and 1.3** only.

* If both HTTP and HTTPS ports are configured, the gateway **gives precedence to HTTPS**, falling back to HTTP only if SSL is explicitly disabled.

## Consequences

* Ensures **consistent TLS enforcement** at the edge, with a single point for managing certs and TLS policies.
* Backend services can operate over internal HTTP, simplifying their configuration.
* Slight performance overhead at the gateway layer due to decryption, which is acceptable for MVP.
* Future ADRs can consider mutual TLS (mTLS) for upstream connections or offloading TLS termination to an ingress controller or service mesh.