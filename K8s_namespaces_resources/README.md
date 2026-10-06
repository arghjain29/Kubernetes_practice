# K8s_namespaces_resources - Namespaces and resource limits

This folder introduces two key Kubernetes concepts: namespaces and resource requests/limits.

Files:
- `namespace.yaml` - creates a dedicated namespace named `dev-namespace` for isolating workloads.
- `pod-limits.yaml` - deploys a Pod inside that namespace with specific CPU and memory requests and limits.

Why this matters:
- Namespaces help separate development, testing, and production workloads inside the same cluster.
- Resource requests tell Kubernetes how much memory and CPU a container needs to run.
- Resource limits cap the maximum CPU and memory a container can consume.

Quick start:

```powershell
# Create the namespace
kubectl apply -f K8s_namespaces_resources/namespace.yaml

# Create the Pod inside the namespace
kubectl apply -f K8s_namespaces_resources/pod-limits.yaml

# Check the namespace and Pod
kubectl get namespaces
kubectl get pods -n dev-namespace
kubectl describe pod limited-pod -n dev-namespace
```

What is happening in the example:

```yaml
resources:
  requests:
    memory: "64Mi"
    cpu: "150m"
  limits:
    memory: "128Mi"
    cpu: "300m"
```

- `64Mi` memory request means the Pod needs at least that much memory to schedule.
- `128Mi` memory limit means the container cannot exceed that memory usage.
- `150m` CPU request is 0.15 CPU cores.
- `300m` CPU limit is 0.30 CPU cores.

Learning notes:
- Namespaces are a logical boundary, not a full security boundary by themselves.
- Pods in different namespaces can still share the same cluster and networking rules unless you configure isolation.
- Requests and limits are important for scheduling fairness and preventing one app from starving others.
- Use `kubectl top pod -n dev-namespace` if metrics-server is available to see live usage in a real cluster.

Troubleshooting:

1. Pod stays `Pending`
   - Check whether the namespace exists and the Pod is targeted to it.
   - Review events with:

```powershell
kubectl get events -n dev-namespace --sort-by=.metadata.creationTimestamp
```

2. Pod is rejected because of resource pressure
   - Verify the node has enough available CPU and memory.
   - Adjust requests and limits so they fit the node capacity.

3. Container is killed due to memory or CPU limit
   - Increase the limit or optimize the app so it stays within the configured budget.

Mini exercise:
- Create a second Pod in the same namespace with a different CPU request.
- Compare scheduling behavior and resource usage.
- Try increasing the limits and observe how it affects the Pod status.

Useful commands:

```powershell
kubectl get ns
kubectl get pods -A
kubectl describe namespace dev-namespace
kubectl delete pod limited-pod -n dev-namespace
kubectl delete namespace dev-namespace
```

Further reading:
- `kubectl explain namespace`
- `kubectl explain pod.spec.containers.resources`
- Kubernetes docs: Namespaces and Resource Management
