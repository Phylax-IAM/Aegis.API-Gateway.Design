# research.md

## 🔍 Research & Design Explorations: Aegis API Gateway

This document captures the **background research**, **tradeoff analyses**, and **key references** that informed the design of the Aegis API Gateway.

---

## 🚀 Why Build Our Own Gateway?

| Reason                   | Explanation                                                    |
| ------------------------ | -------------------------------------------------------------- |
| **Custom features**      | Needed custom header injection, tracing, flexible YAML config. |
| **Performance control**  | Wanted low-overhead, minimal hops, direct TLS termination.     |
| **Learning & ownership** | Build expertise in building robust, cloud-native infra.        |
| **Avoid vendor lock-in** | Avoid heavy reliance on closed-source or opinionated gateways. |

---

## 🔬 Comparison with Existing Solutions

| Product             | Pros                                 | Cons                                        | Reference                                                  |
| ------------------- | ------------------------------------ | ------------------------------------------- | ---------------------------------------------------------- |
| **NGINX**           | Very fast, proven, rich modules      | Lua/custom modules needed for tracing etc.  | [NGINX Docs](https://nginx.org/en/docs/)                   |
| **Envoy**           | gRPC, xDS config, deep observability | Steep learning curve, overkill for MVP.     | [Envoy Proxy](https://www.envoyproxy.io/docs)              |
| **Kong**            | Plugins for everything               | Heavier footprint, license split.           | [Kong Gateway](https://docs.konghq.com/gateway/latest/)    |
| **Traefik**         | Simple, dynamic routing, native k8s  | Plugin model immature for heavy features.   | [Traefik](https://doc.traefik.io/traefik/)                 |
| **AWS API Gateway** | Fully managed, direct auth/CORS etc. | Costly, limited for custom mTLS or caching. | [AWS API Gateway](https://docs.aws.amazon.com/apigateway/) |

➡️ **Conclusion:** None offered the exact balance of **flexible YAML config**, **internal TLS handling**, **per-route caching & header rewriting**, with **Spring Boot** integration for fast delivery.

---

## 🧩 Key Topics Explored

### 1. 🔒 TLS / SSL Termination

* Options reviewed:

  * [AWS ALB HTTPS Termination](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/create-https-listener.html)
  * [Kubernetes Ingress TLS](https://kubernetes.io/docs/concepts/services-networking/ingress/#tls)
  * In-pod termination using mounted certs
* 📌 **Decision:** TLS termination inside the Gateway, with certs managed by [cert-manager](https://cert-manager.io/docs/).

---

### 2. 🚦 Rate Limiting Strategies

* [Token Bucket Algorithm](https://en.wikipedia.org/wiki/Token_bucket)
* [Leaky Bucket](https://en.wikipedia.org/wiki/Leaky_bucket)
* [Resilience4j RateLimiter](https://resilience4j.readme.io/docs/ratelimiter)
* 📌 Decision: Token bucket with guava-style or bucket4j integration.

---

### 3. ⚡ Caching Strategies

* [Caffeine Cache](https://github.com/ben-manes/caffeine)
* [Spring Cache abstraction](https://docs.spring.io/spring-framework/reference/integration/cache.html)
* [Redis](https://redis.io/docs/latest/)
* Start with local LRU, migrate to Redis for multi-instance invalidation.

---

### 4. ✈️ Routing Configurations

* YAML vs JSON vs DB-backed dynamic routes:

  * [Spring Boot YAML config example](https://docs.spring.io/spring-boot/docs/current/reference/html/features.html#features.external-config)
* 📌 YAML preferred for human readability & GitOps-friendly.

---

### 5. 🕵️ Observability Stack

* [Prometheus](https://prometheus.io/docs/introduction/overview/)
* [Grafana](https://grafana.com/docs/)
* [Jaeger](https://www.jaegertracing.io/docs/)
* [EFK Stack](https://www.elastic.co/elastic-stack/) or [Loki](https://grafana.com/oss/loki/)

---

### 6. ⚙️ Java Threads vs Virtual Threads

* [Project Loom (JEP 444)](https://openjdk.org/jeps/444)
* Benchmarked against native thread pool; Loom still experimental for mission-critical prod workloads.

---

## 🌐 Cloud Deployment Research

| Option               | Pros                                | Cons                                    | Reference                                                                       |
| -------------------- | ----------------------------------- | --------------------------------------- | ------------------------------------------------------------------------------- |
| AWS ECS              | Simple, direct service, minimal k8s | Less portability                        | [ECS](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/Welcome.html) |
| AWS EKS              | Full Kubernetes ecosystem           | Slightly more ops complexity            | [EKS](https://docs.aws.amazon.com/eks/latest/userguide/what-is-eks.html)        |
| Lambda + API Gateway | Serverless simplicity               | Not ideal for caching / TLS termination | [AWS Lambda](https://docs.aws.amazon.com/lambda/)                               |

---

## 📝 Future Research / Backlog

* Service Meshes:

  * [Istio](https://istio.io/latest/docs/)
  * [Linkerd](https://linkerd.io/2.13/)
* Redis cluster with pub/sub for distributed cache invalidation.
* mTLS to backend services.
* Native gRPC proxy mode.

---

## 📚 Additional References & Inspiration

* [HTTP caching RFC 7234](https://httpwg.org/specs/rfc7234.html)
* [Distributed Tracing](https://opentracing.io/docs/overview/)
* [Kubernetes Networking Concepts](https://kubernetes.io/docs/concepts/services-networking/)
* [Best practices for secure HTTPS services](https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html)
* [Spring Cloud Gateway inspiration](https://spring.io/projects/spring-cloud-gateway) (even though building custom)
