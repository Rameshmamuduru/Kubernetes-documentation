# 🟦 Kubernetes CONTROLLERS

> (Automation loops – they reconcile desired vs actual state)

These run inside *kube-controller-manager*.

### 🔹 Workload Controllers

| Controller             | Manages         |
| ---------------------- | --------------- |
| ReplicaSet Controller  | ReplicaSets     |
| Deployment Controller  | Deployments     |
| StatefulSet Controller | StatefulSets    |
| DaemonSet Controller   | DaemonSets      |
| Job Controller         | Jobs            |
| CronJob Controller     | CronJobs        |
| Replication Controller | Legacy replicas |

---

### 🔹 Infrastructure Controllers

| Controller               | Role                  |
| ------------------------ | --------------------- |
| Node Controller          | Node health           |
| Service Controller       | Cloud LoadBalancers   |
| EndpointSlice Controller | Service → Pod mapping |
| Namespace Controller     | Namespace lifecycle   |
| Garbage Collector        | Orphan cleanup        |
| TTL Controller           | Deletes finished Jobs |
| Pod GC Controller        | Cleans dead pods      |
| Lease Controller         | Leader election       |

---

### 🔹 Security & Access Controllers

| Controller                         | Role                     |
| ---------------------------------- | ------------------------ |
| ServiceAccount Controller          | Default service accounts |
| Token Controller                   | Issues tokens            |
| CSR Approving Controller           | Approves cert requests   |
| CSR Signing Controller             | Signs certs              |
| CertificateSigning Controller      | Handles certs            |
| ClusterRole Aggregation Controller | RBAC aggregation         |

---

### 🔹 Resource & Storage Controllers

| Controller                   | Role            |
| ---------------------------- | --------------- |
| ResourceQuota Controller     | Enforces quotas |
| LimitRange Controller        | Default limits  |
| PV Controller                | PV lifecycle    |
| PVC Protection Controller    | Protect PVC     |
| PV Protection Controller     | Protect PV      |
| Volume Attachment Controller | Attach volumes  |

---

# 🟩 Kubernetes SERVICES

> (Networking abstraction – stable access to pods)

### 🔹 Core Service Types

| Service Type     | Description                   |
| ---------------- | ----------------------------- |
| ClusterIP        | Internal access               |
| NodePort         | Exposes via node IP           |
| LoadBalancer     | Cloud LB                      |
| Headless Service | No ClusterIP (direct pod DNS) |
| ExternalName     | Maps to external DNS          |

---

# 🟨 Kubernetes DNS COMPONENTS

> (Name resolution, NOT controllers)

| Component          | Purpose            |
| ------------------ | ------------------ |
| CoreDNS            | Cluster DNS        |
| kube-dns           | Legacy DNS         |
| NodeLocal DNSCache | Node-level caching |
| ExternalDNS        | Syncs cloud DNS    |

---

# 🟥 NOT CONTROLLERS

These are often confused:

| Item               | What it is                           |
| ------------------ | ------------------------------------ |
| Headless Service   | Service mode                         |
| Ingress            | Routing rule                         |
| Ingress Controller | Separate controller (NGINX, Traefik) |
| kube-proxy         | Network proxy                        |
| CoreDNS            | DNS service                          |

---

If you want, next I can give you:
👉 *Scheduler types & policies*
👉 *kube-proxy modes*
👉 *All Ingress controllers list*
