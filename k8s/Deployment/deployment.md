# Kubernetes Deployment Guide

---

## What is a Deployment?

A **Deployment** in Kubernetes is a higher-level abstraction that manages **ReplicaSets** and **Pods**.  
It provides:

- **Desired state management** — ensures the specified number of Pods are always running.
- **Self-healing** — recreates Pods if they fail.
- **Rolling updates & rollbacks** — upgrade images without downtime.
- **Scalability** — easily scale Pods up or down.

Unlike a standalone Pod, a Deployment ensures **consistency** and **resilience** by automatically maintaining and controlling multiple replicas.

---

## Example Deployment YAML

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-first-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      type: frontend
  template:
    metadata:
      labels:
        type: frontend
    spec:
      containers:
        - name: nginx-deployment-container
          image: nginx
```

---

## Commands with this Deployment

### 1) Create the Deployment
```bash
kubectl apply -f my-first-deployment.yaml
```

---

### 2) View Deployments
```bash
kubectl get deployments
kubectl get deployment my-first-deployment -o wide
```

---

### 3) View ReplicaSets created by this Deployment
```bash
kubectl get rs
```

---

### 4) View Pods managed by the ReplicaSet
```bash
kubectl get pods -l type=frontend
```

---

### 5) Get detailed information
```bash
kubectl describe deployment my-first-deployment
```

---

### 6) Scale the Deployment
```bash
kubectl scale deployment my-first-deployment --replicas=5
```

---

### 7) Update the Deployment (change image)
```bash
kubectl set image deployment/my-first-deployment nginx-deployment-container=nginx:1.25.3
```

---

### 8) Check rollout status
```bash
kubectl rollout status deployment/my-first-deployment
```

---

### 9) Undo rollout (rollback to previous version)
```bash
kubectl rollout undo deployment/my-first-deployment
```

---

### 10) Delete the Deployment
```bash
kubectl delete deployment my-first-deployment
```

---

## Check Resource Scope (cluster-scoped vs namespaced)
```bash
kubectl api-resources
kubectl api-resources --namespaced=true
kubectl api-resources --namespaced=false
```

---

## Important Note on Pod Naming

**If you create a Pod directly** using a Pod definition file, the Pod name will be exactly as defined — no random suffix is added.

**Example:**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-nginx-pod
spec:
  containers:
    - name: nginx
      image: nginx
```
Resulting Pod name: `my-nginx-pod`

**If you create a Deployment**, it creates a ReplicaSet, which then creates Pods. Those Pods **will have auto-generated suffixes** for uniqueness.

**Example pod name:** `my-first-deployment-5c8f6d4f9d-abc12`

This ensures multiple replicas don’t conflict with each other.

---

## Quick Imperative Examples (optional)

Create an NGINX Deployment quickly:
```bash
kubectl create deployment nginx --image=nginx
```

Generate Deployment YAML without creating it:
```bash
kubectl create deployment nginx --image=nginx --dry-run=client -o yaml
```

Create and scale an HTTPD Deployment to 3 replicas:
```bash
kubectl create deployment httpd-frontend --image=httpd:2.4-alpine --replicas=3
```

---

## Best Practices

- Prefer **Deployments** over standalone Pods for production workloads.
- Use **labels** (e.g., `app=nginx`, `type=frontend`) to query and manage resources.
- Pin **versioned images** (e.g., `nginx:1.25.3`) instead of `latest`.
- Keep manifests in **version control** (GitOps).
- Use `kubectl rollout` commands to manage and observe updates safely.

---