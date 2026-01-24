
# what is it and why?
K8s controllers handle Pod creation, deletion, updates, and scaling.
This solves all “limitations” we discussed earlier (ephemeral Pods, HA, rolling updates, scaling, etc.).

# What is Reconciliation in Kubernetes?
Reconciliation is the process by which Kubernetes continuously ensures that the actual state of the cluster matches the desired state you defined.
- Desired state” → What you define in YAML (Pod, Deployment, Service, etc.)
- Actual state” → What’s currently running in the cluster
Kubernetes controllers keep comparing these and fix any differences automatically.

# Types of Controllers
- Replication Controller
- Replica Set
- Deployment
- DaemonSet
- StatefulSet

# Replication Controller:
An older Kubernetes controller that ensures a specified number of Pods are running at any time.
# Key points:
- Introduced in early Kubernetes versions (v1.0)
- Ensures desired number of Pods are running
- Uses labels to track Pods
- No support for new features like set-based label selectors

```
apiVersion: v1
kind: ReplicationController
metadata:
  name: nginx-rc
spec:
  replicas: 3
  selector:
    app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
```

# Replicaset:
The modern version of ReplicationController with all the same functionality plus extra features.
# Key points:
- Introduced in Kubernetes v1.2+
- Ensures desired number of Pods are running
- Uses labels to track Pods
- Supports set-based selectors (in, notin, etc.)
- Managed by Deployments in production

```
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-rs
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
```

K8s will also help us us to expose RC and RA by creating a service manifest. here is the service manifest will be look like

```
apiVersion: v1
kind: Service
metadata:
  name: pod-1
spec:
  selector:
    app: myapp
  type: LoadBalancer
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
```

Above service can be slect the POD by looking the lables and selectors.
      
