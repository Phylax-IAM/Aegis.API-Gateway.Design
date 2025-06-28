# ✅ ADR-004: Deployment Strategy on AWS EKS

## 📜 Context

We are designing the **Aegis API Gateway**, which must be deployed as a containerized service in a **production-grade, cloud-native environment**.
The gateway itself is responsible for **TLS/SSL termination**, which mandates that **certificates are mounted into the container**, and traffic is forwarded as raw TCP from the AWS Load Balancer.

---

## 💡 Decision

We will:

✅ Deploy the gateway on **AWS EKS**,
✅ Mount **TLS certificates** into the pod and perform **SSL termination inside the gateway**,
✅ Use an **NLB in TCP mode** to forward traffic to the gateway pods,
✅ Manage certificates using **cert-manager** and **Kubernetes Secrets**,
✅ Use Kubernetes-native objects like `Deployment`, `Service`, `HPA`, `ConfigMap`, etc.

---

## 🔐 TLS Termination Strategy

| Component         | Role in TLS                         |
| ----------------- | ----------------------------------- |
| **Load Balancer** | TCP passthrough (no TLS handling)   |
| **Gateway**       | Performs full TLS/SSL termination   |
| **cert-manager**  | Manages certificates & auto-renewal |
| **Secrets**       | Stores cert/key pair                |

* Ingress or Load Balancer uses **TCP (Layer 4) routing** on port `443` to target your gateway pod directly.
* TLS certificates are mounted to the gateway as Kubernetes **Secrets**, injected into the container at runtime.

---

## 🔄 Implications

| Benefit                                | Explanation                                      |
| -------------------------------------- | ------------------------------------------------ |
| Full control over SSL logic            | You can support mTLS, custom cipher suites, etc. |
| Cert reload without downtime           | Cert-manager + file watchers inside the app      |
| Easier to inspect TLS requests in logs | Since decryption happens at gateway layer        |

---

## 🛠 Sample Kubernetes Deployment with TLS Mount

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aegis-api-gateway
  labels:
    app: aegis-gateway
spec:
  replicas: 3
  selector:
    matchLabels:
      app: aegis-gateway
  template:
    metadata:
      labels:
        app: aegis-gateway
    spec:
      containers:
        - name: gateway
          image: your-dockerhub/aegis-gateway:latest
          ports:
            - containerPort: 8443  # HTTPS
            - containerPort: 8080  # HTTP fallback
          volumeMounts:
            - name: tls-certs
              mountPath: "/etc/ssl/certs"
              readOnly: true
          env:
            - name: SSL_CERT_PATH
              value: "/etc/ssl/certs/tls.crt"
            - name: SSL_KEY_PATH
              value: "/etc/ssl/certs/tls.key"
      volumes:
        - name: tls-certs
          secret:
            secretName: aegis-tls-secret  # Created by cert-manager
```

---

## 🧰 Supporting Services

```yaml
apiVersion: v1
kind: Service
metadata:
  name: aegis-gateway-service
  annotations:
    service.beta.kubernetes.io/aws-load-balancer-type: "nlb"
    service.beta.kubernetes.io/aws-load-balancer-backend-protocol: "tcp"
spec:
  type: LoadBalancer
  ports:
    - name: https
      port: 443
      targetPort: 8443
    - name: http
      port: 80
      targetPort: 8080
  selector:
    app: aegis-gateway
```

---

## 🎯 Summary

✅ The API Gateway is in full control of SSL.
✅ AWS NLB forwards encrypted traffic directly to the container. 
✅ This setup allows advanced TLS features like mTLS, custom cert rotation, and header injection. 
