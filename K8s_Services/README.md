# K8s_Services — Services & Deployments examples

This folder contains example Kubernetes manifests that demonstrate how to expose applications and manage replicas.

Files:
- `deployment.yaml` — a `Deployment` running 3 replicas of a simple echo server. Includes resource requests/limits and container port.
- `pod.yaml` — a single `Pod` running the same echo server (useful for debugging and learning).
- `service-nodeport.yaml` — `Service` of type `NodePort` exposing the app on node port `30080`.
- `service-loadbalancer.yaml` — `Service` of type `LoadBalancer` exposing the app on port `80` (minikube simulates LB).

Quick start (Minikube):

```powershell
# Apply Deployment and Services
kubectl apply -f K8s_Services/deployment.yaml
kubectl apply -f K8s_Services/service-nodeport.yaml
# or use LoadBalancer service (minikube will simulate it)
kubectl apply -f K8s_Services/service-loadbalancer.yaml

# Check resources
kubectl get deploy,rs,pods,svc -n default

# Open the service in a browser (Minikube-friendly)
minikube service welcome-service-nodeport
minikube service welcome-service-loadbalancer
```

Learning tips:
- Use `kubectl describe` to view events and the mapping from Service ports to Pod ports.
- Try scaling the `Deployment` and watch traffic distribution across replicas:

```powershell
kubectl scale deployment welcome-deployment --replicas=5
kubectl get pods -o wide
```

- Compare using `kubectl expose` vs defining `Service` manifests directly.
- Experiment: change the container image tag in `deployment.yaml` and reapply to observe a rolling update.

Troubleshooting:
- If `LoadBalancer` stays in `Pending`, your environment likely lacks an external LB (normal for local clusters). Use `minikube service` or `NodePort`.
- If the Service cannot reach Pods, ensure Pod labels match the Service selector (`app: welcome`).

Further reading:
- `kubectl explain service` and `kubectl explain deployment`
- Kubernetes docs: Services (ClusterIP / NodePort / LoadBalancer) and Deployments
