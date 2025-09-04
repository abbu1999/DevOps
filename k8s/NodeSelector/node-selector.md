# 🖥️ NodeSelector in Kubernetes

## 📌 What is NodeSelector?
- `nodeSelector` is the simplest way to constrain a **Pod** to run on particular **nodes** based on **labels**.  
- You apply **labels to nodes**, and then use `nodeSelector` in your **Pod specification** to tell Kubernetes:  
  > "Schedule this Pod only on nodes that have these labels."

---

## 🏷️ Working with Node Labels

### 1️⃣ Apply a Label to a Node
```bash
kubectl label nodes <node-name> <label-key>=<label-value>
```

Example:
```bash
kubectl label nodes node1 size=Large
```

---

### 2️⃣ Check Node Labels
```bash
kubectl get nodes --show-labels
```

---

### 3️⃣ Remove (Unlabel) a Node
```bash
kubectl label nodes node1 size-
```
This removes the label `size=Large` from `node1`.

---

## 📄 Example Pod with NodeSelector

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
    - name: nginx
      image: nginx
  nodeSelector:
    size: Large
```

- In this example:
  - Pod will only be scheduled on a node that has the label `size=Large`.
  - If no node has this label, Pod will remain in `Pending` state.

---

## 🔑 Key Notes
- `nodeSelector` is a **hard requirement** — Pod will **not run** if no matching node exists.  
- For **more flexible scheduling** (e.g., "prefer but not require"), Kubernetes provides **Node Affinity** instead.  
- Labels can be **key=value** pairs, and you can add multiple labels to the same node.  
- If multiple Pods use the same `nodeSelector`, Kubernetes spreads them across nodes (if available) to balance load.

---

## 📊 NodeSelector vs NodeAffinity

| Feature              | NodeSelector | Node Affinity |
|----------------------|--------------|---------------|
| Complexity           | Simple key=value match | Advanced expressions (`In`, `NotIn`, `Exists`, etc.) |
| Requirement type     | Only hard requirement | Hard (requiredDuringScheduling) and soft (preferredDuringScheduling) |
| Flexibility          | Low | High |
| Use Case             | Quick pinning to labeled nodes | Complex scheduling rules |

---

✅ **Summary**  
- Use `nodeSelector` when you need a **simple way** to schedule Pods on specific labeled nodes.  
- For **production-grade flexibility**, prefer **Node Affinity**.  

---
🔗 **Reference:** [Kubernetes Docs - NodeSelector](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/)