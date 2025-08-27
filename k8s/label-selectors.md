# Kubernetes Label Selectors

Labels in Kubernetes are key/value pairs attached to objects such as Pods, Services, and Deployments. They are used to organize, group, and select subsets of objects. Label selectors allow you to filter resources based on these labels.

---

## Why Use Labels?
- To logically group resources (e.g., by environment, version, or team).
- To apply operations (like `kubectl get`, `kubectl delete`, or `kubectl scale`) only on a subset of objects.
- To connect Services to specific Pods.

---

## Basic Commands

### 1. Get all resources with a specific label key:
```bash
kubectl get pods -l env
👉 Lists all Pods that have the env label defined (regardless of value).

2. Get all resources with a specific label key and value:
bash
Copy
Edit
kubectl get pods -l env=dev
👉 Lists all Pods that have the label env=dev.

3. Get all resources across types filtered by a label:
bash
Copy
Edit
kubectl get all -l env=prod
👉 Lists all resources (Pods, Services, Deployments, etc.) with the label env=prod.

4. Show all Pods along with their labels:
bash
Copy
Edit
kubectl get pods --show-labels
👉 Displays Pods with their attached labels.

Advanced Label Selectors
Equality-based selectors
kubectl get pods -l env=dev → selects Pods where env=dev.

kubectl get pods -l 'env!=dev' → selects Pods where env is not equal to dev.

Set-based selectors
kubectl get pods -l 'env in (dev,prod)' → selects Pods where env is either dev or prod.

kubectl get pods -l 'env notin (dev,prod)' → selects Pods where env is neither dev nor prod.

Combining multiple conditions
kubectl get pods -l 'env=prod,tier=frontend'
👉 Selects Pods that match both conditions (env=prod AND tier=frontend).

Reference
For more details, see:
Certified Kubernetes Administrator Course - Labels & Selectors