# 🚀 Setup Kubernetes on Amazon EKS (EKS + Docker + Minikube)

This guide explains how to set up:

- Amazon EKS Cluster
- kubectl
- eksctl
- Docker
- Minikube

Official AWS Documentation:  
https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html

---

# 📌 Pre-Requisites

- EC2 Instance
- IAM Role attached to EC2 with permissions:
  - IAM
  - EC2
  - VPC
  - CloudFormation

> ⚠ If your bootstrap system is outside AWS, create an IAM user with programmatic access.

---

# ☁️ AWS EKS Setup

---

## 1️⃣ Install kubectl (For EKS)

### Download kubectl

```sh
curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.19.6/2021-01-05/bin/linux/amd64/kubectl
```

### Make kubectl executable

```sh
chmod +x kubectl
```

### Move to system path

```sh
sudo mv kubectl /usr/local/bin/
```

### Verify installation

```sh
kubectl version --short --client
```

---

## 2️⃣ Install eksctl

### Download and extract latest release

```sh
curl --silent --location \
"https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" \
| tar xz -C /tmp
```

### Move binary

```sh
sudo mv /tmp/eksctl /usr/local/bin/
```

### Verify installation

```sh
eksctl version
```

---

## 3️⃣ Create IAM Role

Attach IAM Role to EC2 instance with access to:

- IAM
- EC2
- VPC
- CloudFormation

---

## 4️⃣ Create EKS Cluster

### Generic Command

```sh
eksctl create cluster \
  --name <cluster-name> \
  --region <region-name> \
  --node-type <instance-type> \
  --nodes-min 2 \
  --nodes-max 2 \
  --zones <AZ-1>,<AZ-2>
```

### Example

```sh
eksctl create cluster \
  --name naresh \
  --region us-east-1 \
  --node-type t2.small \
  --nodes-min 2 \
  --nodes-max 2
```

> ⏳ Cluster creation may take 10–15 minutes.

---

## 5️⃣ Validate Cluster

### Check Nodes

```sh
kubectl get nodes
```

### Create Test Pod

```sh
kubectl run test-pod --image=nginx
```

### Check Pods

```sh
kubectl get pods
```

---

## 6️⃣ Delete EKS Cluster

```sh
eksctl delete cluster --name naresh --region ap-south-1
```

---

# 🐳 Docker + Minikube Setup (Local Kubernetes)

---

## EC2 Instance Requirement

```
t2.medium (Minimum 2 CPU, 2GB RAM required)
```

---

# 🐳 Install Docker

### Install Docker

```sh
sudo dnf install docker -y
```

### Start Docker Service

```sh
sudo systemctl start docker.service
```

### Enable Docker Service

```sh
sudo systemctl enable docker.service
```

### Add ec2-user to Docker group

```sh
sudo usermod -aG docker ec2-user
```

### Apply group changes immediately

```sh
newgrp docker
```

### Verify Docker

```sh
docker --version
docker ps
```

---

# 🚀 Install Minikube

### Download Minikube

```sh
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
```

### Install Minikube

```sh
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

### Start Minikube

```sh
minikube start
```

### Check Status

```sh
minikube status
```

---

# 📦 Install kubectl (General Kubernetes Setup)

### Download kubectl

```sh
curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
```

### Make kubectl executable

```sh
chmod +x kubectl
```

### Move to system path

```sh
sudo mv kubectl /usr/local/bin/kubectl
```

### Verify

```sh
kubectl version --client
```

---

# ✅ Final Verification

```sh
kubectl get nodes
kubectl get pods
minikube status
docker ps
```

---

# 🎯 Summary

You have successfully learned how to:

- Install kubectl
- Install eksctl
- Create and delete EKS cluster
- Install Docker
- Install Minikube
- Run Kubernetes locally and on AWS

---

⭐ Happy Learning DevOps!
