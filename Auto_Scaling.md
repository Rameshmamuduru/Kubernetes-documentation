# What is Auto-scaling:
Auto scaling is a concept that can help us to scale the pods/resources based on the load on the server. kubernetes can help us to scale 3 things
# 1. Horizontal Auto scaling
# 2. Vertical Auto scaling
# 3. Cluster Auto scaling

## Horizontal Auto scaling:
it can help us to increse/decrese no.of pod replicas based on the cpu, memory and other metrics
```
Traffic ↑ → CPU ↑ → HPA adds pods
Traffic ↓ → CPU ↓ → HPA removes pods
```

```
Metrics Server is the pre-requerement
```

