# 1. Pre-Requerements:

- k8s clutser
- Domine
- helm installed

# 2. nginx Ingress Controller setup:
```
| Method            | Environment | PROD |
| ----------------- | ----------- | ---- |
| Minikube addon    | Local       | ❌    |
| kubectl apply URL | Generic     | ⚠️   |
| Helm chart        | Any         | ✅    |
| Cloud-managed     | Cloud       | ✅    |
| Custom manifests  | Advanced    | ⚠️   |
```
# we can install nginx controller using heml

- installation of helm:
```
curl -fsSL https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
helm version
```
# Add ingress-nginx Helm repository
```
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update
helm repo list
```

# Install NGINX Ingress Controller (PROD)
```
helm install ingress-nginx ingress-nginx/ingress-nginx \
  --namespace ingress-nginx \
  --create-namespace \
  --set controller.replicaCount=2 \
  --set controller.service.type=LoadBalancer
```

# Verify:
## Verify installation
```
kubectl get pods -n ingress-nginx
```
## You should see:
```
ingress-nginx-controller-xxxxx   Running
```
## Check service (VERY IMPORTANT)
```
kubectl get svc -n ingress-nginx
```
# Deploy an application:
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app1-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: app1
  template:
    metadata:
      labels:
        app: app1
    spec:
      containers:
        - name: app1
          image: kastrov/app1-image:latest
          ports: 
            - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: app1-service  #this service name will be referenced in the ingress yml file
spec:
  selector:
    app: app2
  ports:
    - port: 80
      targetPort: 80
---

apiVersion: apps/v1
kind: Deployment
metadata:
  name: app2-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: app2
  template:
    metadata:
      labels:
        app: app2
    spec:
      containers:
        - name: app2
          image: kastrov/app1-image:latest
          ports: 
            - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: app2-service  #this service name will be referenced in the ingress yml file
spec:
  selector:
    app: app1
  ports:
    - port: 80
      targetPort: 80
---

apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: "nginx-ingress"
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
    cert-manager.io/cluster-issuer: letsencrypt-prod 
    nginx.ingress.kubernetes.io/ssl-redirect: "true" 
    nginx.ingress.kubernetes.io/proxy-body-size: "10m" 
    nginx.ingress.kubernetes.io/proxy-read-timeout: "60"
spec:
  ingressClassName: "nginx"
  rules:
  - http:
      paths:
        - path: /app1
          pathType: Prefix
          backend:
            service:
              name: "app1-service"
              port:
                number: 80

        - path: /app2
          pathType: Prefix
          backend:
            service:
              name: "app2-service"
              port:
                number: 80

        - path: /
          pathType: Prefix
          backend:
            service:
              name: "app1-service"
              port:
                number: 80
```

Perfect — now we move from **Minikube demo → real production on EKS**.
I’ll show you **how to secure your app with HTTPS on EKS using NGINX Ingress Controller** with **cert-manager + Let’s Encrypt**, which is the PROD way.

---

# HTTPS Setup on EKS (PROD)

## Prerequisites

1. **EKS cluster running**
2. **kubectl + Helm installed**
3. **NGINX Ingress Controller installed via Helm** (LoadBalancer type)
4. **Your domain** (e.g., `demo.example.com`) pointing to your EKS ELB

Check ELB URL:

```bash
kubectl get svc -n ingress-nginx
```

You should see something like:

```
ingress-nginx-controller   LoadBalancer   a1b2c3.elb.amazonaws.com
```

---

# Install cert-manager
Cert-manager handles automatic TLS certificates.

```bash
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.15.0/cert-manager.yaml
```

Check pods:

```bash
kubectl get pods -n cert-manager
```

You should see:

```
cert-manager-xxxxx    Running
cert-manager-webhook-xxxxx   Running
cert-manager-cainjector-xxxxx  Running
```

---
# Create ClusterIssuer (Let's Encrypt)

```yaml
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: letsencrypt-prod
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: your-email@example.com   # Use your email
    privateKeySecretRef:
      name: letsencrypt-prod
    solvers:
    - http01:
        ingress:
          class: nginx
```

Apply:

```bash
kubectl apply -f clusterissuer.yaml
```

Check:

```bash
kubectl get clusterissuer
```

---

# Create your Ingress with TLS

Example `ingress.yaml`:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: demo-ingress
  namespace: default
  annotations:
    cert-manager.io/cluster-issuer: letsencrypt-prod
spec:
  ingressClassName: nginx
  tls:
  - hosts:
    - demo.example.com
    secretName: demo-tls
  rules:
  - host: demo.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: demo-service
            port:
              number: 80
```

Apply:

```bash
kubectl apply -f ingress.yaml
```

---

## STEP 4️⃣ Check the TLS secret

Cert-manager will automatically create the secret:

```bash
kubectl get secret demo-tls
```

It should be of type:

```
kubernetes.io/tls
```

---

## STEP 5️⃣ Update DNS

* Point your domain `demo.example.com` → your **ELB DNS**
* Wait a few minutes for DNS propagation

---

## STEP 6️⃣ Test HTTPS

Browser:

```
https://demo.example.com
```

✅ Let’s Encrypt issues a valid certificate automatically
✅ NGINX Ingress handles HTTPS termination
✅ Your app remains HTTP internally

---

## 🔑 PROD BEST PRACTICES

1. Always use **cert-manager + Let's Encrypt**
2. Force HTTP → HTTPS redirect in annotations:

```yaml
nginx.ingress.kubernetes.io/force-ssl-redirect: "true"
```

3. Use **multiple replicas** for ingress controller
4. Monitor cert-manager logs for renewal issues

---

## 🧠 How traffic flows (PROD)

```
Browser
   ↓ HTTPS
AWS ELB (Ingress LoadBalancer)
   ↓ forwards to NGINX Ingress Controller
       TLS terminates here
   ↓ Routes based on host/path
Service (ClusterIP)
   ↓
Pod
```

---

## 🎯 Interview-ready statement

> In EKS, HTTPS is handled at the Ingress Controller. We use cert-manager with a ClusterIssuer (Let’s Encrypt) to automatically issue and renew certificates. The ELB routes HTTPS traffic to NGINX, which terminates TLS and forwards requests to services.

---

If you want, I can create a **full working YAML demo for EKS** with:

* Deployment + Service + Ingress + cert-manager TLS → ready to copy & apply.

Do you want me to do that next?
