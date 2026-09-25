
# Kubernetes_practice

This repository is for practicing Kubernetes locally. It will grow over time; this README reflects the current contents.

## Repository Structure
- [SettingUP_Enviroment/](./SettingUP_Enviroment/): Setup guide for Minikube, `kubectl`, Docker, and Windows-specific instructions.
- [Basic_Pods/](./Basic_Pods/): Simple Pod examples and learning notes.
- [K8s_Services/](./K8s_Services/): Deployment, Pod, and Service examples (NodePort & LoadBalancer) with learning comments.
- [k8s_Volumes/](./k8s_Volumes/): PersistentVolume, PersistentVolumeClaim, and Pod storage examples with troubleshooting notes.
- [K8s_config_secrets/](./K8s_config_secrets/): ConfigMap, Secret, and environment injection examples with learning notes.

## Learning Notes
 - See `SettingUP_Enviroment/README.md` for setup steps and verification.
 - `Basic_Pods/README.md` explains simple Pod examples and container basics.
 - `K8s_Services/README.md` explains Deployments and Services examples.
 - `k8s_Volumes/README.md` explains persistent storage, PV/PVC binding, and volume mounting.
 - `K8s_config_secrets/README.md` explains ConfigMaps, Secrets, and loading env vars into a Pod.

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

Volumes example quick test:

```powershell
# Create the PV and PVC
kubectl apply -f k8s_Volumes/pv.yaml
kubectl apply -f k8s_Volumes/pvc.yaml

# Create a Pod that mounts the PVC
kubectl apply -f k8s_Volumes/pod-volume.yaml

# Verify the Pod is running and the volume is mounted
kubectl get pods
kubectl describe pod pod-with-storage
kubectl exec -it pod-with-storage -- sh
/ # cd /mnt/data
/mnt/data # echo "I WAS HERE" > note.txt
/mnt/data # cat note.txt
```

ConfigMap and Secret example quick test:

```powershell
# Create the ConfigMap, Secret, and Pod
kubectl apply -f K8s_config_secrets/configmap.yaml
kubectl apply -f K8s_config_secrets/secret.yaml
kubectl apply -f K8s_config_secrets/pod-env.yaml

# Check the injected values inside the container
kubectl exec -it my-pod -- sh
/ # echo $VAR_1
/ # echo $VAR_2
/ # echo $password
```

The Secret value was encoded with base64 first:

```powershell
echo -n "supersecret" | base64
```

 


