# What is CSI Driver:
- A CSI driver (Container Storage Interface driver) allows Kubernetes to create, attach, and manage cloud volumes automatically.
- You don’t manually create PVs — Kubernetes + driver handles it.
# Examples:
- AWS EBS → ebs.csi.aws.com
- AWS EFS → efs.csi.aws.com
- GCP PD → pd.csi.storage.gke.io
- Azure Disk → disk.csi.azure.com

# Pre-Requerements:

- Kubernetes cluster running (EKS, GKE, etc.)
- CSI driver installed for your cloud storage
- For EKS, EBS CSI driver is often pre-installed; if not, install via Helm.
- IAM permissions for nodes to create cloud volumes
- Example: AWS worker nodes need ec2:CreateVolume, ec2:AttachVolume, etc.

# Installtion of AWS CLI DRIVER Install:

- Associate OIDC Driver
```
eksctl utils associate-iam-oidc-provider \
  --cluster demo-cluster \
  --region ap-south-1 \
  --approve
```
required for IRSA

- Create IAM policy:
```
eksctl create iamserviceaccount \
  --name ebs-csi-controller-sa \
  --namespace kube-system \
  --cluster demo-cluster \
  --region ap-south-1 \
  --attach-policy-arn arn:aws:iam::aws:policy/service-role/AmazonEBSCSIDriverPolicy \
  --approve \
  --role-name AmazonEKS_EBS_CSI_DriverRole
```
- Verify Installation:
```
kubectl get pods -n kube-system | grep ebs
```




# Demo:

# 1.Create Storage Class:
    This defines how k8s will provision voulmes directly.
  
```
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: gp2
provisioner: ebs.csi.aws.com
parameters:
  type: gp2
reclaimPolicy: Delete
volumeBindingMode: WaitForFirstConsumer
```

- provisioner: which CSI driver to use
- parameters: storage type (SSD/HDD)
- reclaimPolicy: Delete → volume deleted when PVC deleted
- volumeBindingMode: Wait until Pod is scheduled to pick the correct AZ

Apply:
```
kubectl apply -f storageclass.yaml
kubectl get sc
```
# 2. Create PVC Using storage class:
```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-dynamic
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
  storageClassName: gp2
```
Apply
```
kubectl apply -f pvc.yaml
kubectl get pvc
kubectl get pv
```

once we appied pvc k8s will creats volume dynamically using CSI driver. and creates a PV as well

# 3. now create a pod/deployment:
```
apiVersion: v1
kind: Pod
metadata:
  name: nginx-dynamic-pod
spec:
  containers:
  - name: nginx
    image: nginx:latest
    volumeMounts:
    - name: vol1
      mountPath: /usr/share/nginx/html
  volumes:
  - name: vol1
    persistentVolumeClaim:
      claimName: pvc-dynamic
```

- Pod uses PVC → Kubernetes mounts automatically provisioned volume.
- You never manually create PV
- PVC requests storage → driver provisions cloud volume → binds to PVC → Pod uses it

Apply:
```
kubectl apply -f pod.yaml
kubectl get pod
kubectl describe pod nginx-dynamic-pod
```

# Final Flow:
- Driver installed → lets Kubernetes create cloud storage automatically
- StorageClass → defines driver + parameters + reclaim policy
- PVC → requests storage from StorageClass
- Pod → mounts PVC
- Result → storage created dynamically in cloud, mounted to Pod
