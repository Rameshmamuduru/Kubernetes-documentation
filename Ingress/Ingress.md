# What is Ingress:
- Ingress is a Kubernetes API object that manages external HTTP/HTTPS access to services inside a cluster.
In simple words:
- Ingress exposes your applications to users using a single entry point (DNS + HTTP/HTTPS routing).

# Without Ingress (basic way)
- You expose apps using:
NodePort & LoadBalancer
# Example:
```
User → NodeIP:30007 → Service → Pod
```
# Problems:
- Random ports
- One LoadBalancer per service (costly in cloud)
- No URL-based routing
- No TLS management

# With Ingress:
```
User
 ↓
Ingress Controller (NGINX / ALB / Traefik)
 ↓
Ingress Rules (host/path based)
 ↓
Service
 ↓
Pod
```
# Why Ingress is Used (Very Important)
- Single entry point for multiple apps
```
app1.example.com → frontend service
app2.example.com → backend service
```
- One LoadBalancer
- Multiple applications

- Path-based routing
```
example.com/api → api-service
example.com/ui  → ui-service
```
- Host-based routing (DNS-based)
```
shop.example.com → shop service
admin.example.com → admin service
```
- TLS / HTTPS termination
Ingress can:
```
Handle HTTPS
Manage TLS certs (via cert-manager)
Terminate SSL at ingress
```
- Secure
- Centralized

- Cloud cost optimization 
- Without Ingress:
10 services = 10 LoadBalancers
- With Ingress:
10 services = 1 LoadBalancer

- What is an Ingress Controller?
Ingress alone does NOTHING, You must install an Ingress Controller.
- Popular controllers:
```
Controller	Used in
NGINX Ingress	Generic / On-prem
AWS ALB Ingress	EKS
Traefik	Lightweight
HAProxy	High performance
```
