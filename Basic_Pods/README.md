# Basic_Pods — Learning examples

This folder contains a simple Pod manifest enhanced for learning Kubernetes basics.

Files:
- `pod.yaml` — a commented Pod manifest demonstrating:
  - labels and annotations
  - `imagePullPolicy` and `restartPolicy`
  - environment variables (`env`)
  - resource `requests` and `limits`
  - an `emptyDir` volume and `volumeMounts`

Quick commands:

```powershell
# Create the Pod
kubectl apply -f Basic_Pods/pod.yaml

# Watch the Pod start and check status
kubectl get pods -w

# Describe to see events and mounted volumes
kubectl describe pod my-pod

# View container logs
kubectl logs my-pod

# Delete the Pod when done
kubectl delete -f Basic_Pods/pod.yaml
```

Learning tips:
- Use `kubectl explain pod` and `kubectl explain pod.spec.containers` to explore fields.
- Try adding a second container (sidecar) to observe multi-container Pod patterns.
- Add `livenessProbe`, `readinessProbe`, or `startupProbe` as an exercise to learn probe behavior.

Troubleshooting (quick):
- If Pod is `CrashLoopBackOff` or not starting, inspect logs and recent events:

```powershell
kubectl describe pod my-pod
kubectl logs my-pod           # current pod logs
kubectl logs -p my-pod        # previous container's logs (if restarted)
kubectl get events --sort-by=.metadata.creationTimestamp
```

- Common checks:
  - Verify the container image runs locally or check container exit code in `kubectl describe`.
  - Confirm resource `requests` are not preventing scheduling.
  - Ensure required ports are opened by the container process.