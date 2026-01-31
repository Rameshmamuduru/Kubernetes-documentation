```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-volume
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /mnt/pv-volume
    type: DirectoryOrCreate
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-volume
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 500Mi

---

apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod-with-pvc
spec:
  containers:
  - name: nginx
    image: nginx:latest
    volumeMounts:
    - name: vol2
      mountPath: /usr/share/nginx/html

  volumes:
  - name: vol2
    persistentVolumeClaim:
      claimName: pvc-volume
```

- PersistentVolume (PV) represents existing storage available to the Kubernetes cluster (such as node disk, EBS, EFS, or NFS). It does not create storage by itself; it only defines and exposes that storage as a Kubernetes resource.
- PersistentVolumeClaim (PVC) is a request for storage made by an application. Kubernetes matches the PVC with a suitable PersistentVolume based on size and access mode.
- In the Pod (or Deployment/StatefulSet) manifest, we reference the PVC in the volumes section and mount it using volumeMounts. This allows the Pod to use the storage that was claimed through the PVC.

