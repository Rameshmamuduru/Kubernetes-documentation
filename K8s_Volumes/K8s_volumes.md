---
# Kubernetes Voules:
---
  Kubernetes Volumes are used to store data for Pods so that data is not lost when containers restart and can be shared between containers in the same Pod.
# Why volumes are needed in K8s
- Containers are ephemeral (data inside container FS is lost on restart)
- Apps like DBs, logs, uploads need persistent storage
- Multiple containers in a Pod may need shared data

Below is a **COMPLETE + CLEAR list of Kubernetes volume types**, each with:

* **What it is (simple explanation)**
* **When to use**
* **Sample YAML snippet**

This is **production-oriented**, **interview-ready**, and **easy to remember**.

---

# Kubernetes Volume Types – Full List with Examples

---

## 1️⃣ emptyDir

### What it is

Temporary storage created when the Pod starts and deleted when the Pod is removed.

### When to use

Cache, temp files, sharing data between containers in same Pod.

### Sample YAML

```yaml
volumes:
- name: temp-vol
  emptyDir: {}
```

---

## 2️⃣ hostPath (⚠️ Not recommended for apps)

### What it is

Mounts a directory from the Kubernetes node into the Pod.

### When to use

Node-level logs, monitoring agents.

### Sample YAML

```yaml
volumes:
- name: host-vol
  hostPath:
    path: /var/log
```

---

## 3️⃣ ConfigMap Volume

### What it is

Mounts configuration data as files.

### When to use

Application configs (nginx.conf, app.properties).

### Sample YAML

```yaml
volumes:
- name: config-vol
  configMap:
    name: app-config
```

---

## 4️⃣ Secret Volume

### What it is

Mounts sensitive data securely.

### When to use

Passwords, tokens, certificates.

### Sample YAML

```yaml
volumes:
- name: secret-vol
  secret:
    secretName: db-secret
```

---

## 5️⃣ PersistentVolumeClaim (PVC) ⭐⭐⭐ (MOST IMPORTANT)

### What it is

Persistent storage that survives Pod restarts.

### When to use

Databases, Jenkins, uploads, PROD workloads.

### Sample YAML (PVC)

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: app-pvc
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
```

### Pod usage

```yaml
volumes:
- name: data-vol
  persistentVolumeClaim:
    claimName: app-pvc
```

---

## 6️⃣ NFS

### What it is

Shared network file storage.

### When to use

Multiple Pods accessing same data.

### Sample YAML

```yaml
volumes:
- name: nfs-vol
  nfs:
    server: 10.0.0.5
    path: /exports
```

---

## 7️⃣ projected

### What it is

Combines multiple volume sources into one directory.

### When to use

App needs ConfigMap + Secret in same path.

### Sample YAML

```yaml
volumes:
- name: projected-vol
  projected:
    sources:
    - configMap:
        name: app-config
    - secret:
        name: app-secret
```

---

## 8️⃣ downwardAPI

### What it is

Exposes Pod metadata to containers.

### When to use

Logging, monitoring, debugging.

### Sample YAML

```yaml
volumes:
- name: pod-info
  downwardAPI:
    items:
    - path: "pod_name"
      fieldRef:
        fieldPath: metadata.name
```

---

## 9️⃣ CSI (Container Storage Interface) ⭐

### What it is

Modern standard storage plugin system.

### When to use

Cloud storage (AWS EBS, EFS, Azure Disk).

### Sample YAML (StorageClass)

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: ebs-sc
provisioner: ebs.csi.aws.com
```

---

## Volume Type Summary Table

| Volume      | Persistent | Multi-Pod | PROD |
| ----------- | ---------- | --------- | ---- |
| emptyDir    | ❌          | ❌         | ❌    |
| hostPath    | ❌          | ❌         | ❌    |
| ConfigMap   | ✅          | ✅         | ✅    |
| Secret      | ✅          | ✅         | ✅    |
| PVC         | ✅          | Depends   | ✅⭐   |
| NFS         | ✅          | ✅         | ✅    |
| projected   | ✅          | ✅         | ✅    |
| downwardAPI | ❌          | ❌         | ⚠️   |
| CSI         | ✅          | Depends   | ✅⭐   |

---

## Golden Rule (Remember this)

> **In real production → Always use PVC + StorageClass (CSI)**

