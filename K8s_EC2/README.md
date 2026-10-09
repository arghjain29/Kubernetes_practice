# K8s_EC2 — Kubernetes on AWS EC2 Ubuntu

This folder documents the steps used to set up a local Kubernetes learning environment on an AWS EC2 Ubuntu instance and deploy a simple React Todo List app with Minikube.

## What Was Done

The workflow was split into two parts:

1. Install and prepare the Kubernetes environment on Ubuntu.
2. Deploy the React Todo List app and expose it through Kubernetes networking.

## 1. Installation Steps

Follow these commands in order.

### 1) Update and upgrade system packages

```powershell
sudo apt update && sudo apt upgrade -y
```

Updates the package index and upgrades installed packages to the latest versions.

### 2) Install Docker

```powershell
sudo apt install -y docker.io
```

Installs Docker, which Minikube uses as the container runtime driver.

### 3) Enable and start Docker

```powershell
sudo systemctl enable docker
sudo systemctl start docker
```

Starts the Docker service and enables it on boot.

### 4) Add your user to the Docker group

```powershell
sudo usermod -aG docker $USER
```

Allows Docker commands to run without `sudo` after you log out and back in.

### 5) Download and install Minikube

```powershell
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

Downloads the Minikube binary and installs it in your system path.

### 6) Install Kubernetes dependencies

```powershell
sudo apt install -y apt-transport-https ca-certificates curl
```

Installs the packages required to add the Kubernetes repository.

### 7) Add the Kubernetes GPG key and repository

```powershell
sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
```

Adds the official Kubernetes package signing key and repository source.

### 8) Update package list and install kubectl

```powershell
sudo apt update
sudo snap install kubectl --classic
```

Installs `kubectl`, the Kubernetes command-line tool.

### 9) Start Minikube with the Docker driver

```powershell
minikube start --driver=docker
```

Starts a local Kubernetes cluster using Docker.

## 2. Verify the Installation

Check that the cluster is ready:

```powershell
kubectl get nodes
```

You should see one node with the status `Ready`.

## 3. Deploy the React Todo List App

This section shows how the app was run on Kubernetes using a pod and a NodePort service.

### 1) Start Minikube

```powershell
minikube start --driver=docker
```

Ensures the local Kubernetes cluster is running.

### 2) Verify the nodes

```powershell
kubectl get nodes
```

Confirms the Minikube node is healthy.

### 3) Deploy the app pod

```powershell
kubectl run todolistapp --image=kubekode/react-todo-list-app
```

Creates a pod from the React Todo List image.

### 4) Check pod status

```powershell
kubectl get pods
```

Verifies the pod is running.

### 5) Expose the pod as a service

```powershell
kubectl expose pod todolistapp --type=NodePort --port=80 --name=todolistapp-service
```

Creates a NodePort service so the app can be accessed from outside the cluster.

### 6) List services

```powershell
kubectl get svc
```

Shows the service name, cluster IP, and node port.

### 7) Get the service URL from Minikube

```powershell
minikube service todolistapp-service --url
```

Returns the URL you can use to access the app.

### 8) Test the app with curl

```powershell
curl http://<MINIKUBE-IP>:<NODE-PORT>
```

Checks whether the app responds successfully.

### 9) Forward the service port to your machine

```powershell
kubectl port-forward svc/todolistapp-service 3000:80 --address 0.0.0.0 &
```

Maps local port `3000` to service port `80`.

### 10) Configure the EC2 security group

- Open the inbound rules for your EC2 instance.
- Allow TCP traffic on port `3000` from `0.0.0.0/0` if you want browser access from outside.

### 11) Open the app in a browser

```powershell
http://<EC2-IP>:3000
```

Replace `<EC2-IP>` with the public IP address of your EC2 instance.

## Learning Notes

- Minikube uses Docker here so you can practice Kubernetes without a full cluster.
- `kubectl run` is a quick way to create a pod for learning, but a Deployment is better for real applications.
- `kubectl expose` creates a service from an existing pod and is useful for quick demos.
- `NodePort` is the simplest service type for local or lab environments.
- `kubectl port-forward` is useful when you want temporary access without creating an external load balancer.

## Troubleshooting

- If `kubectl get nodes` does not show `Ready`, check the Minikube startup logs and Docker service status.
- If the app is not reachable through port forwarding, confirm the EC2 security group allows port `3000`.
- If Docker commands require `sudo`, log out and log back in after adding your user to the Docker group.
- If the service exists but does not respond, verify the pod is running and the service selector matches the pod labels.

## Quick Command Recap

```powershell
sudo apt update && sudo apt upgrade -y
sudo apt install -y docker.io
sudo systemctl enable docker
sudo systemctl start docker
sudo usermod -aG docker $USER
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
sudo apt install -y apt-transport-https ca-certificates curl
sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt update
sudo snap install kubectl --classic
minikube start --driver=docker
kubectl get nodes
kubectl run todolistapp --image=kubekode/react-todo-list-app
kubectl expose pod todolistapp --type=NodePort --port=80 --name=todolistapp-service
kubectl get svc
minikube service todolistapp-service --url
kubectl port-forward svc/todolistapp-service 3000:80 --address 0.0.0.0 &
```
