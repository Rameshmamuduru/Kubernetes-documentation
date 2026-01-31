# Step 1: Open the correct IAM role
- AWS Console → IAM → Roles → k8s

# Step 2: Attach REQUIRED policies (minimum to unblock eksctl)
- Attach ALL of these:

 Mandatory
```
AmazonEKSClusterPolicy
AmazonEKSServicePolicy
CloudFormationFullAccess
```
Node + infra
```
AmazonEC2FullAccess
AmazonEKSWorkerNodePolicy
IAMFullAccess
```
Nice to have
```
AmazonSSMManagedInstanceCore
```
If you want fastest success right now:

