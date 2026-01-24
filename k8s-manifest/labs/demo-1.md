# Initial/installation setup:

- create a IAM Role and attach the below policies to it and add the IAM role while launching an instance. or you can create a IAM user with an access keys and take a ssh to ec2 and do aws configure so that you an able to communicate with aws resources through CLI
  

  <img width="1342" height="520" alt="image" src="https://github.com/user-attachments/assets/06a91612-a54e-4396-88a6-cdbd76033324" />


- start/launch a ec2 instance
- take a ssh and run below commands
```
sudo apt update
sudo vim setup.sh
```
and update the below commands in the file and save and exit (press esc :wq and enter)

```
#Install AWS CLI latest version 
sudo apt install unzip
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
aws --version

#Install kubectl 
curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.19.6/2021-01-05/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin
kubectl version --short --client

#Install eksctl
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin
eksctl version

```
```
sudo chmod +x setup.sh
./ setup.sh
```
Now your eksctl,kubectl and aws cli setup has been done

# Create a EKS cluster by running the below commands.
```
eksctl create cluster --name demo-cluster --region us-east-1 --node-type t2.medium  --zones us-east-1a,us-east-1b
```




