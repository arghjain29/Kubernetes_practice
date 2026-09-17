
# Kubernetes_practice

This repository is for practicing Kubernetes locally. It will grow over time; this README reflects the current contents.

## Repository Structure
- [SettingUP_Enviroment/](./SettingUP_Enviroment/): Setup guide for Minikube, `kubectl`, Docker, and Windows-specific instructions.

## Learning Notes
- [SettingUP_Enviroment/settingup_minikube_kubectl.md](SettingUP_Enviroment/settingup_minikube_kubectl.md): Step-by-step Windows setup for Docker, Chocolatey, `kubectl`, and Minikube.

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

 


