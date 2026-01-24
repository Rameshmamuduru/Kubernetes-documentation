# What is Deployment:

A Deployment is a Kubernetes controller that manages ReplicaSets and Pods for you. It provides declarative updates to your applications, like scaling, rolling updates, and rollback.
- It’s a higher-level abstraction over ReplicaSet
- Ensures desired state (number of replicas, container images, labels, etc.) is maintained
- Automatically manages self-healing, scaling, and updates

# Why Deployment can be used?

```
| Feature             | Why it matters                                                                 |
| ------------------- | ------------------------------------------------------------------------------ |
| **Self-healing**    | If a Pod crashes, Deployment ensures a new Pod is created automatically        |
| **Scaling**         | Easily scale number of Pods up/down using `replicas` or `kubectl scale`        |
| **Rolling Updates** | Deploy new versions of the app gradually, no downtime                          |
| **Rollbacks**       | If something goes wrong, you can rollback to previous ReplicaSet automatically |
| **Declarative**     | You declare desired state in YAML; Kubernetes makes reality match it           |
```

# Deployment YAML Example (nginx)

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment        # Name of the deployment
  labels:
    app: nginx
spec:
  replicas: 3                    # Desired number of Pods
  selector:
    matchLabels:
      app: nginx                 # Pods must have this label to be managed
  template:                      # Pod template
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.23        # Container image
        ports:
        - containerPort: 80      # Port inside the container
```

# Explanation of Each Field
```
apiVersion: apps/v1
```

- Deployment is part of apps API group (v1 stable)

```
kind: Deployment
```

- Defines resource type

```
metadata
```

- name → unique name for this Deployment
- labels → useful for grouping, selection, monitoring

```
spec.replicas
```

- Number of Pods you want running

- Example: 3 → 3 Pods running at all times

```
spec.selector
```

- Important! Links Deployment to Pods

- Deployment manages only Pods matching this label selector

```
spec.template
```

- Pod template: defines what each Pod should look like

- Inside template:

- metadata.labels → must match spec.selector

- spec.containers → define containers (name, image, ports)

# Apply Deployment
```
kubectl apply -f nginx-deployment.yaml
```

3 This will:

- Create Deployment object
- Deployment creates a ReplicaSet internally
- ReplicaSet creates 3 Pods (as per replicas)

# Check Status
```
kubectl get deployments       # Show Deployment status
kubectl get rs                # ReplicaSet created by Deployment
kubectl get pods              # Pods created by ReplicaSet
```

- Deployment ensures desired state = actual state
- If a Pod crashes → ReplicaSet will recreate → Deployment reconciles

# Scaling
```
kubectl scale deployment nginx-deployment --replicas=5
```

- Deployment automatically updates ReplicaSet
- Creates 2 additional Pods to reach 5

# Rolling Update Example
```
kubectl set image deployment/nginx-deployment nginx=nginx:1.25
```
- Deployment creates a new ReplicaSet for the new image
- Gradually replaces old Pods with new Pods (rolling update)
- Old ReplicaSet is kept for rollback if needed

# Rollback
```
kubectl rollout undo deployment/nginx-deployment
```

- Deployment rolls back to previous ReplicaSet
- No manual Pod manipulation required

# Key Takeaways
- Deployment = Pod + ReplicaSet manager
- Handles self-healing, scaling, rolling updates, rollback
- Declarative, production-ready, standard way to manage apps
- Always use Deployment instead of manually creating Pods/RS

# Mental Model Diagram

```
Deployment (nginx-deployment)
       │
       ▼
   ReplicaSet (auto-created)
       │
       ▼
   Pods (3 running replicas)
```

