# eks-mahes

client machine creation 
=======================

login as login ubuntu 
sudo -i
sudo apt update

aws cli installation
====================
sudo apt install -y curl unzip
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
aws --version
or 
sudo apt update && sudo apt install -y curl unzip && curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip" && unzip awscliv2.zip && sudo ./aws/install && aws --version

kubectl installation
====================

curl -Lo kubectl https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
kubectl version --client
or
curl -Lo kubectl https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl && chmod +x ./kubectl && sudo mv ./kubectl /usr/local/bin/kubectl && kubectl version --client

Eksctl cluster creation 
==============================
curl --silent --location "https://github.com/eksctl-io/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp && sudo mv /tmp/eksctl /usr/local/bin
eksctl version
eksctl create cluster --name myekscluster2 --region ap-south-1 --node-type t2.medium --zones ap-south-1a,ap-south-1b

IAM role :-

Attach the following Policies:-
For Client machine attach --> EC2 to admin role
For Cluster automaticaly it will create and attach role and permission  --> AmazonEKSClusterPolicy/AmazonEKSVPCResourceController
For Worker node and master node eks will create and attach role and policies --> AmazonEKSClusterPolicy/AmazonEKSVPCResourceController
AmazonEC2ContainerRegistryReadOnly
AmazonEKS_CNI_Policy
AmazonEKSWorkerNodePolicy
AmazonSSMManagedInstanceCore

Cluster creation completed
Now check 
Node - kubectl get node
Resource - kubectl get pods 

**To list the cluster **
cmd = aws eks list-clusters
**Manually create an S3 bucket**
cmd = aws s3 mb s3://sathish-eks-state
cmd = export EKS_STATE_STORE=s3://sathish-eks-state
**Save cluster details**
cmd aws eks describe-cluster --name <cluster-name> --region <region> --output yaml > eks-cluster.yaml
aws s3 cp eks-cluster.yaml $EKS_STATE_STORE/
**Store Cluster Configurations in S3**
aws eks update-kubeconfig --name <cluster-name> --region <region>
aws s3 cp ~/.kube/config $EKS_STATE_STORE/kubeconfig

**Automating EKS Cluster State Storage**
create shell script file 
eks-backup.sh
chmod +x eks-backup.sh
#!/bin/bash
# Define EKS cluster name and region
CLUSTER_NAME="sathish-eks-cluster"
REGION="us-east-1"
S3_BUCKET="s3://sathish-eks-state"
# Get cluster details
aws eks describe-cluster --name $CLUSTER_NAME --region $REGION --output yaml > cluster-info.yaml
# Save to S3
aws s3 cp cluster-info.yaml $S3_BUCKET/cluster-info-$(date +%Y%m%d%H%M%S).yaml

crontab -e
* * * * * /path/to/eks-backup.sh
crontab -l 


Resource creation
=================
apiVersion: v1
kind: Service
metadata:
  name: load-dep-001
spec:
  type: LoadBalancer
  selector:
    app: myapp
  ports:
    - name: http
      protocol: TCP
      port: 80
---
apiVersion: apps/v1
kind: Deployment 
metadata:
  name: deployment-load1
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp

    spec:
      containers:
        - name: httpd
          image: httpd
          ports:
           - containerPort: 80
