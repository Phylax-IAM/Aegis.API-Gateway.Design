# ADR-001: Routing Strategy for Aegis API Gateway (MVP)

## Status

Accepted

## Context

The Aegis API Gateway must efficiently route incoming client HTTP requests to the appropriate upstream services.
In designing the MVP, we evaluated several routing strategies:

* **Path-based routing:** matching request paths like `/auth/**` or `/static/**`.
* **Header-based routing:** routing based on custom HTTP headers (e.g., `X-Service: profile`).
* **Regex-based or expression-based routing:** providing very granular, dynamic matching capabilities.

For the MVP, our primary goals are **simplicity, performance, and predictability**, so we needed to balance flexibility with speed and ease of implementation.

## Decision

For the MVP, we decided on:

* ✅ **Path-based routing** as the primary routing strategy.
  This allows straightforward mapping of URL paths to backend services (e.g., `/auth/**` to `auth-service`).

* ✅ **Limited header-based routing** to handle select cases where routing by HTTP headers is essential.
  This remains optional and explicitly defined in configuration.

* ❌ **No regex-based or complex expression routing** in the MVP.
  This avoids performance overhead and reduces implementation complexity. Such capabilities can be considered for future releases as plugins or advanced match rules.

## Consequences

* The MVP implementation will be **simple, fast, and easy to configure** via YAML.
* Developers and operators will clearly see routing rules based on path or simple header matches.
* The absence of regex and dynamic expression matching means **some advanced routing scenarios won’t be possible in the MVP**, but this simplifies early maintenance and observability.
* Future ADRs can revisit introducing more sophisticated routing strategies as customer needs evolve.
