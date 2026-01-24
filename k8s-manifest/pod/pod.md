# what is POD?

  A POD is a smallest deployable unit in kubernetes that runs one or more containers together.
- k8s not run containers directly
- it always runs containers inside
- Run containers together
- Share network
- Share storage
- Be managed as one unit

# Think of a Pod like this
  ```
Pod
 ├── Container 1 (App)
 ├── Container 2 (Helper / Sidecar)
 ├── Shared Network (same IP, same port space)
 └── Shared Storage (Volumes)
```


# How to Create POD's:
POD's can be created in two ways 
# 1. Imparative-way:
```
kubectl run nginx-cli-pod --image=nginx  #create a pod
kubectl get pods  #list the pods
kubectl describe pod nginx-cli-pod #this is discribe pods
kubectl exec -it nginx-cli-pod -- /bin/bash  #login/access pods by login into bin
kubectl delete pod nginx-cli-pod  #delete pod
```

# 2. Declarative-way:
```
vim nginx-yaml-pod.yaml
```

```bash
apiVersion: v1
kind: pod
metadata:
  name: mypod
  labels:
    app: myapp
  spec:
    containers:
    - name: mycontainer
      image: myimage:latest
      ports:
      - containerPort: 8080
      env:
      - name: ENV_VAR
        value: "value"
      volumeMounts:
      - name: myvolume
        mountPath: /data
    volumes:
    - name: myvolume
      emptyDir: {}
```

```
kubectl apply -f nginx-yaml-pod.yaml        # Create the Pod using the YAML manifest
kubectl get pods                            # List all Pods in the current namespace
kubectl describe pod nginx-yaml-pod         # Show detailed information and events for the Pod
kubectl delete -f nginx-yaml-pod.yaml       # Delete the Pod using the same YAML file

```

The pod created here can not accessable from the  internet and it will be only accessable inside the cluster only. we can access them by take login into nodes and take a curl/ssh to the ip of pod. if we want to access them from internet we need to expose the pods, that we can do by following the below commands

```bash
kubectl expose pod nginx-yaml-pod \
  --type=NodePort \
  --port=80

kubectl expose pod nginx-yaml-pod \
  --type=LoadBalancer \
  --port=80

kubectl get svc
```







  
