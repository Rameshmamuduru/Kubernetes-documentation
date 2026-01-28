# what is emptyDir:
- emptyDir is a temporary folder created automatically by Kubernetes
- when a Pod starts, and deleted when the Pod is removed.
# Life cycle of emptyDir:
```
| Event              | Data             |
| ------------------ | ---------------- |
| Pod starts         | emptyDir created |
| Container crashes  | ✅ data stays     |
| Container restarts | ✅ data stays     |
| Pod restarts       | ❌ data lost      |
| Pod deleted        | ❌ data lost      |
| Node reboot        | ❌ data lost      |

```
# Where the data is stored:
This will be used a storage in nodes. so that data will be dependent on pods status.
by defaut it will use the Disk storage, but if we mentioned medium as memory it will use RAM. so data flow will be fast 

# When emptyDir IS USED in REAL-TIME (PRODUCTION)
# Cache Storage (VERY COMMON)

# Examples:
- App cache
- Build cache
- Package cache

Why?

Cache can be regenerated

No need to persist

# Sidecar Communication (VERY COMMON)

Example:

App writes logs

Sidecar reads logs & sends to ELK / Splunk

# Temporary Processing

Examples:

Image processing

File unzip → process → upload to S3

Data transformation jobs

# CI/CD Pods

Examples:

Jenkins agent pods

GitHub Actions runners

Build workspace

```
apiVersion: v1
kind: Pod
metadata:
  name: emptydir-realtime-demo
  labels:
    app: emptydir-demo
spec:
  volumes:
    - name: shared-logs
      emptyDir: {}     # Created automatically when Pod starts

  containers:
    # Main application container
    - name: app
      image: busybox
      command: ["/bin/sh", "-c"]
      args:
        - |
          while true; do
            echo "$(date) - Application log" >> /logs/app.log;
            sleep 5;
          done
      volumeMounts:
        - name: shared-logs
          mountPath: /logs

    # Sidecar container (log shipper)
    - name: log-shipper
      image: busybox
      command: ["/bin/sh", "-c"]
      args:
        - |
          while true; do
            tail -n +1 /logs/app.log;
            sleep 5;
          done
      volumeMounts:
        - name: shared-logs
          mountPath: /logs
```

if we need faster access them we need used memory

```
volumes:
- name: fast-cache
  emptyDir:
    medium: Memory
```











