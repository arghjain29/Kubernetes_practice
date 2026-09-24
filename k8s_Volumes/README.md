# k8s_Volumes — Persistent Volumes, Claims, and Pod Storage

This folder demonstrates how Kubernetes stores data outside the container filesystem using a `PersistentVolume` (PV), a `PersistentVolumeClaim` (PVC), and a Pod that mounts the claim.

Files:
- `pv.yaml` — creates a `PersistentVolume` backed by a host path (`/mnt/data` on the node).
- `pvc.yaml` — creates a `PersistentVolumeClaim` requesting storage for the Pod.
- `pod-volume.yaml` — mounts the PVC into a Pod at `/mnt/data`.

Why this matters:
- Container files are temporary and disappear when the container restarts or is recreated.
- `Volumes` let Pods persist data across restarts and share storage between containers when needed.
- In Kubernetes, a `PV` is the actual storage resource and a `PVC` is the request for that storage.

Quick start:

```powershell
# Start Minikube (Docker driver)
minikube start --driver=docker

# Create the storage resources
kubectl apply -f k8s_Volumes/pv.yaml
kubectl apply -f k8s_Volumes/pvc.yaml

# Launch a Pod that mounts the PVC
kubectl apply -f k8s_Volumes/pod-volume.yaml

# Check if everything is created and running
kubectl get pv,pvc,pods
kubectl describe pod pod-with-storage
```

Checking the mounted storage:

```powershell
kubectl exec -it pod-with-storage -- sh
/ # cd /mnt/data
/mnt/data # echo "I WAS HERE" > note.txt
/mnt/data # cat note.txt
```

This proves the write to `/mnt/data` is persisted through the mounted volume.

Commands I ran during this exercise:

```powershell
# Start cluster
minikube start --driver=docker

# Create PV and PVC
kubectl apply -f ./pv.yaml
kubectl apply -f ./pvc.yaml

# First attempt to create Pod (wrong YAML structure)
kubectl apply -f ./pod-volume.yaml
# Error: unknown field "spec.containers[0].volumes"

# Correct Pod applied successfully
kubectl apply -f ./pod-volume.yaml
kubectl get pods
kubectl describe pod pod-with-storage

# Open a shell in the container
kubectl exec -it pod-with-storage -- sh
/ # ls
/ # cd /mnt
/mnt # cd data
/mnt/data # echo "I WAS HERE" > /mnt/data/note.txt
/mnt/data # cat note.txt

# Remove and recreate the Pod to verify persistence
kubectl delete pod pod-with-storage
kubectl apply -f ./pod-volume.yaml
kubectl exec -it pod-with-storage -- sh
/ # cd /mnt/data
/mnt/data # cat note.txt
```

Learning tips:
- `kubectl explain pod.spec.volumes` and `kubectl explain pod.spec.containers.volumeMounts` are very useful when learning the exact YAML structure.
- A Pod can mount multiple volumes, not just one.
- The `hostPath` in `pv.yaml` is a local Node directory, so it is best for learning and local clusters, not production.
- In a real cluster, you would usually use NFS, cloud disks, or dynamic storage classes instead of `hostPath`.

Troubleshooting:

1. `unknown field "spec.containers[0].volumes"`
   - This happens when `volumes` is placed inside the container definition instead of at the Pod level.
   - Correct structure:

```yaml
spec:
  containers:
    - name: pod-with-storage
      image: busybox
      volumeMounts:
        - name: storage-volume
          mountPath: "/mnt/data"
  volumes:
    - name: storage-volume
      persistentVolumeClaim:
        claimName: demo-pvc
```

2. PVC not bound
   - Run:

```powershell
kubectl get pv
kubectl get pvc
kubectl describe pvc demo-pvc
```

3. Pod not running
   - Run:

```powershell
kubectl get pods
kubectl describe pod pod-with-storage
kubectl logs pod-with-storage
```

4. Storage path missing
   - Since the PV uses a `hostPath`, check that the path exists on the Minikube node.
   - For local learning, `hostPath: "/mnt/data"` is fine as long as the path is available on the worker node.

Mini exercise:
- Change the `mountPath` in `pod-volume.yaml` from `/mnt/data` to another folder.
- Write a different file into the mounted directory.
- Delete the Pod and recreate it.
- Verify that the file still exists after restart.

Conclusion:
This example shows the core idea behind Kubernetes storage: your application writes to a mounted volume, and that data survives beyond the lifetime of a single container. It is a key concept for stateful apps, databases, logs, and any workload that must preserve data.

Further reading:
- `kubectl explain pv`
- `kubectl explain pvc`
- `kubectl explain pod.spec.volumes`
- `kubectl explain pod.spec.containers.volumeMounts`
- Kubernetes docs: PersistentVolumes, PersistentVolumeClaims, and Volumes
