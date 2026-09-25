# K8s_config_secrets — ConfigMaps and Secrets

This folder demonstrates how to inject configuration and sensitive values into a Pod using a `ConfigMap` and a `Secret`.

Files:
- `configmap.yaml` — stores non-sensitive key/value data such as app settings.
- `secret.yaml` — stores sensitive data in base64-encoded form.
- `pod-env.yaml` — runs a Pod that loads values from both the ConfigMap and Secret using `envFrom`.

Why this matters:
- `ConfigMap` is for non-secret configuration like feature flags, URLs, or labels.
- `Secret` is for sensitive values such as passwords, tokens, and keys.
- `envFrom` lets a container load all keys from a ConfigMap or Secret as environment variables.

Quick start:

```powershell
# Start Minikube
minikube start --driver=docker

# Create the ConfigMap and Secret
kubectl apply -f K8s_config_secrets/configmap.yaml
kubectl apply -f K8s_config_secrets/secret.yaml

# Create the Pod that consumes both
kubectl apply -f K8s_config_secrets/pod-env.yaml

# Verify the Pod is running
kubectl get pods
```

Check the injected values:

```powershell
kubectl exec -it my-pod -- sh
/ # echo $VAR_1
/ # echo $VAR_2
/ # echo $password
```

In this exercise, the Secret value was encoded with base64 first:

```powershell
echo -n "supersecret" | base64
```

and decoded back with:

```powershell
echo 'c3VwZXJzZWNyZXQ=' | base64 --decode
```

Learning notes:
- The ConfigMap keys become environment variables inside the container.
- The Secret value is decoded by Kubernetes before being exposed as an env var.
- `envFrom` is convenient for learning, but in real apps you may prefer `env` for tighter control over what gets injected.
- `busybox` is used here because it is small and easy to inspect interactively.

Troubleshooting:

1. Pod starts but environment variables are missing
   - Confirm the names in `envFrom` match the resource names exactly.
   - Run `kubectl get configmap my-config` and `kubectl get secret my-secret`.

2. Secret value looks unreadable in YAML
   - That is expected. Kubernetes Secrets store values in base64-encoded form, not plain text.

3. Pod does not start
   - Check `kubectl describe pod my-pod` for events.
   - Check that both resources were created successfully before the Pod.

Mini exercise:
- Change one ConfigMap value, reapply the file, and recreate the Pod.
- Observe how the new environment variable value appears after the Pod starts again.
- Add a new Secret key and verify that it shows up inside the container.

Further reading:
- `kubectl explain configmap`
- `kubectl explain secret`
- `kubectl explain pod.spec.containers.envFrom`
- Kubernetes docs: ConfigMaps and Secrets
