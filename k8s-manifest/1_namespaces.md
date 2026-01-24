# What & why namespce?

A Namespace is a virtual cluster inside a Kubernetes cluster. It provides logical isolation of resources (Pods, Services, Deployments, etc.) so that multiple teams or projects can share the same cluster safely.

# Why Use Namespaces?

```
| Reason                           | Explanation                                                   |
| -------------------------------- | ------------------------------------------------------------- |
| **Resource isolation**           | Resources in one namespace don’t clash with another namespace |
| **Access control**               | Can assign RBAC roles per namespace                           |
| **Organizing environments**      | Example: `dev`, `qa`, `prod` in same cluster                  |
| **Resource quotas**              | Limit CPU, memory per namespace                               |
| **Better monitoring & labeling** | Easier to track resources per team/project                    |
```

# Default Name spaces in K8s:

```
| Namespace         | Purpose                                                            |
| ----------------- | ------------------------------------------------------------------ |
| `default`         | Default namespace if none specified                                |
| `kube-system`     | Kubernetes system components (controller-manager, scheduler, etc.) |
| `kube-public`     | Public resources, readable by all users                            |
| `kube-node-lease` | Internal node heartbeat management                                 |
```

# How to create a Namespace?

```
vim my_namespace.yml
```
```
apiVersion: v1
kind: Namespace
metadata:
  name: dev-environment
```
```
kubectl apply -f namespace-dev.yaml
kubectl get ns
```

And let's go and create a resources for a name space

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: My_deployment
  namespace: dev-environment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my_app
  template:
    metadata:
      labels:
        app: my_app
    spec:
    containers:
      - name: my_container
        image: my_image:latest
        ports:
          - containerPort: 8080
```

- Pods, Services, and ReplicaSets will all belong to dev-environment
- Keeps them isolated from default or prod namespaces

```
kubectl get pods -n dev-environment  #will show the pods in dev-environment name space only
```

Let's create service for the perticular names

```
apiVersion: v1
kind: Service
metadata:
  name: nginx-svc
  namespace: dev-environment
spec:
  selector:
    app: nginx
  type: NodePort
  ports:
    - port: 80
      targetPort: 80
      nodePort: 31010
```
- Only targets Pods in dev-environment with label app=nginx
- Pods with same label in default namespace are not affected

# Resources Quata:
it was a k8s object that will limits how much resources can be used by a namespace

  # It controls:

- Number of Pods
- CPU usage
- Memory usage
- Object counts (Services, ConfigMaps, PVCs, etc.)

# Real Production Workflow

# Step 1: Platform team creates namespace
```
apiVersion: v1
kind: Namespace
metadata:
  name: dev-team-a
```

# Step 2: Applies ResourceQuota (ONE-TIME)

```
apiVersion: v1
kind: ResourceQuota
metadata:
  name: team-a-quota
  namespace: dev-team-a
spec:
  hard:
    pods: "20"
    requests.cpu: "10"
    requests.memory: 20Gi
    limits.cpu: "20"
    limits.memory: 40Gi
```

# Step 3: Platform team applies LimitRange (ONE-TIME)

```
apiVersion: v1
kind: LimitRange
metadata:
  name: team-a-limits
  namespace: dev-team-a
spec:
  limits:
  - default:
      cpu: "500m"
      memory: "512Mi"
    defaultRequest:
      cpu: "250m"
      memory: "256Mi"
    type: Container
```

# Step 4: Developers deploy apps DAILY

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app1
  namespace: dev-team-a
spec:
  replicas: 3
  template:
    spec:
      containers:
      - name: app
        image: myapp:v1
        resources:
          requests:
            cpu: "300m"
            memory: "256Mi"
          limits:
            cpu: "600m"
            memory: "512Mi"
```

if SRE/Platoform team need to edit/increase the resources 
```
kubectl edit resourcequota team-a-quota -n dev-team-a
```



