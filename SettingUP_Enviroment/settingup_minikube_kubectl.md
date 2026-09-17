# Setting up Minikube & kubectl on Windows

This guide walks through prerequisites and step-by-step installation of Docker (or another container runtime), Chocolatey, `kubectl`, and Minikube on Windows. Commands are provided for PowerShell and assume an elevated (Administrator) shell where noted.

**Prerequisites**
- Windows 10/11 with virtualization support enabled in BIOS/UEFI.
- Sufficient RAM (>= 4GB recommended) and CPU cores (2+).
- Recommended: WSL 2 or Docker Desktop for Windows (with WSL2 backend) for best compatibility.

**Decide runtime / VM driver**
- Preferred: Docker Desktop (driver `docker`). If you use Docker Desktop, set it to use WSL2 backend if available.
- Alternative: Hyper-V (Windows Pro/Enterprise) or VirtualBox (ensure it supports your Windows version).

---

## 1. Install Docker Desktop (recommended)
Docker Desktop provides the Docker daemon used by Minikube when using the `docker` driver. Download and install from Docker:

- Download: https://www.docker.com/products/docker-desktop

After installing, sign in if prompted and enable WSL 2 integration (if using WSL2). Start Docker Desktop and verify it runs.

Verify Docker is running (PowerShell):

```powershell
docker version
docker info
```

If `docker` commands return info, Docker is ready.

---

## 2. Install Chocolatey (package manager for Windows)
Open an elevated PowerShell (Run as Administrator) and run:

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
```

Close and re-open PowerShell after install. Verify with:

```powershell
choco -v
```

---

## 3. Install kubectl using Chocolatey
`kubectl` is the Kubernetes command-line tool.

Install with Chocolatey:

```powershell
choco install kubernetes-cli -y
```

Verify the install:

```powershell
kubectl version --client --output=yaml
kubectl version --client
```

If you prefer the official binary, you can download it directly and add it to `PATH`.

---

## 4. Install Minikube using Chocolatey
Install Minikube with Chocolatey:

```powershell
choco install minikube -y
```

Verify the install:

```powershell
minikube version
```

---

## 5. Start Minikube
Common pattern is to use the `docker` driver (requires Docker Desktop running). Start Minikube with resources you prefer:

```powershell
# start minikube using Docker driver
minikube start --driver=docker --memory=4096 --cpus=2

# or explicitly set Kubernetes version
minikube start --driver=docker --kubernetes-version=v1.30.0
```

To use Hyper-V instead (if Docker driver not available):

```powershell
minikube start --driver=hyperv --hyperv-virtual-switch="Primary Virtual Switch"
```

Common useful commands:

```powershell
# check cluster status
minikube status

# show cluster information
kubectl cluster-info

# list nodes
kubectl get nodes -o wide

# show minikube dashboard
minikube dashboard
```

---

## 6. Configure kubectl to talk to Minikube
Minikube automatically sets up a kubeconfig entry when started. Verify current context:

```powershell
kubectl config current-context
kubectl config get-contexts
kubectl get pods --all-namespaces
```

If you ever need to switch back to minikube context:

```powershell
kubectl config use-context minikube
```

---

## 7. Troubleshooting
- If Minikube fails to start, check `minikube logs`:

```powershell
minikube logs
```

- If driver errors occur, ensure the chosen driver is installed and running (Docker Desktop running, Hyper-V enabled, VirtualBox installed).
- On permission errors, run PowerShell as Administrator.
- If Docker commands hang, restart Docker Desktop or WSL2.

---

## 8. Helpful commands summary

```powershell
# Chocolatey
choco install kubernetes-cli -y
choco install minikube -y

# Start minikube
minikube start --driver=docker

# Show cluster information
kubectl cluster-info
kubectl get nodes

# Stop and delete
minikube stop
minikube delete

# Dashboard
minikube dashboard
```

---

## 9. Next steps
- Deploy a sample application using your preferred image and expose it as a NodePort service:

```powershell
kubectl create deployment hello-k8s --image=kicbase/echo-server:1.0
kubectl expose deployment hello-k8s --type=NodePort --port=8080
```

Get the service details and NodePort:

```powershell
kubectl get svc hello-k8s
kubectl describe svc hello-k8s
```

Open the service in your browser (Minikube will open the service endpoint):

```powershell
minikube service hello-k8s
```

- Explore `minikube addons list` and enable `metrics-server`, `dashboard`, etc.