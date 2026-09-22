
# Kubernetes_practice

This repository is for practicing Kubernetes locally. It will grow over time; this README reflects the current contents.

## Repository Structure
- [SettingUP_Enviroment/](./SettingUP_Enviroment/): Setup guide for Minikube, `kubectl`, Docker, and Windows-specific instructions.
- [Basic_Pods/](./Basic_Pods/): Simple Pod examples and learning notes.
- [K8s_Services/](./K8s_Services/): Deployment, Pod, and Service examples (NodePort & LoadBalancer) with learning comments.

## Learning Notes
 - See `SettingUP_Enviroment/README.md` for setup steps and verification.  
 - `K8s_Services/README.md` explains Deployments and Services examples.

## Quick Start
1. Follow the setup guide: `SettingUP_Enviroment/settingup_minikube_kubectl.md`.
2. Start Minikube:

```powershell
minikube start --driver=docker
```

3. Deploy the preferred test app:

```powershell
kubectl create deployment hello-k8s --image=kicbase/echo-server:1.0
kubectl expose deployment hello-k8s --type=NodePort --port=8080
minikube service hello-k8s
```

Deploy the learning Pod example or the services example:

```powershell
# Apply the example Pod manifest
kubectl apply -f Basic_Pods/pod.yaml

# Inspect Pod status, logs and events while learning
kubectl get pods -w
kubectl describe pod my-pod
kubectl logs my-pod
```

Services example quick test:

```powershell
# Apply sample deployment and NodePort service from K8s_Services
kubectl apply -f K8s_Services/deployment.yaml
kubectl apply -f K8s_Services/service-nodeport.yaml
minikube service welcome-service-nodeport
```

 


