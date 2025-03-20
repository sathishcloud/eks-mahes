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


Cluster creation completed
Now check 
Node - kubectl get node
Resource - kubectl get pods 
eksctl delete cluster --name my-cluster --region us-east-1
eksctl get clusters --region us-east-1
eksctl get nodegroup --cluster my-cluster --region us-east-1
eksctl delete nodegroup --cluster my-cluster --name new-nodegroup --region us-east-1

**Kubernetes Access**
aws eks update-kubeconfig --name my-cluster --region us-east-1
kubectl get nodes
eksctl get cluster --name my-cluster --region us-east-1

**Logging & Monitoring**
eksctl utils update-cluster-logging --cluster my-cluster --enable-types all --approve
aws logs describe-log-groups --log-group-name /aws/eks/my-cluster/cluster


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
