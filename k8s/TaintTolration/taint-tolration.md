# Kubernetes Taints & Tolerations

---

## 📌 What are Taints and Tolerations?

- **Taint** → Applied **on Nodes**.  
  It marks a Node so that **only Pods with a matching Toleration** can be scheduled onto it.  
- **Toleration** → Applied **on Pods**.  
  It allows the Pod to be scheduled on Nodes with matching Taints.  

👉 Taints prevent unwanted Pods from landing on special Nodes.  
👉 Tolerations allow specific Pods to bypass the taint and run on those Nodes.  

⚠️ Note:  
- A Node with a taint **can still run normal Pods on other Nodes** if those Pods don’t have the toleration.  
- Taints and tolerations **only control scheduling**. They do not force Pods to always run on that Node.

---

## 📌 Applying a Taint on a Node

```bash
kubectl taint nodes <node-name> <key>=<value>:<effect>
```

### Example:
```bash
kubectl taint nodes node01 spray=mortein:NoSchedule
```

This means: Node `node01` is tainted with `spray=mortein` and Pods **without the toleration** will **not** be scheduled there.

---

## 📌 Effects of Taints

- **NoSchedule** → New Pods without toleration will not be scheduled on the Node.  
- **PreferNoSchedule** → Kubernetes will try to avoid scheduling Pods without toleration, but it is not guaranteed.  
- **NoExecute** →  
  - New Pods without toleration will not be scheduled.  
  - Existing Pods without toleration will be evicted.  

---

## 📌 Viewing Taints on a Node

```bash
kubectl describe node <nodeName> | grep -i taints
```

---

## 📌 Removing a Taint (Untaint a Node)

```bash
kubectl taint nodes <node-name> <key>=<value>:<effect>-
```

### Example:
```bash
kubectl taint nodes node01 spray=mortein:NoSchedule-
```

---

## 📌 Toleration Example (Pod YAML)

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: bee
spec:
  containers:
    - name: nginx
      image: nginx
  tolerations:
    - key: "spray"
      operator: "Equal"
      value: "mortein"
      effect: "NoSchedule"
```

---

## 📌 Explanation of Toleration Fields

- **key** → Must match the taint key on the Node (e.g., `spray`).  
- **operator** → Determines how the key/value is compared.  
  - `"Equal"` → Key & Value must match.  
  - `"Exists"` → Only key is checked; value is ignored.  
- **value** → Must match the taint value if operator is `"Equal"`.  
- **effect** → Must match the taint effect (`NoSchedule`, `PreferNoSchedule`, or `NoExecute`).  

⚠️ **All values in toleration must be under double quotes `" "` in YAML.**

---

## 📌 Key Notes

- Taints are **only applied on Nodes**.  
- Tolerations are **only applied on Pods**.  
- Taints don’t guarantee that Pods with tolerations will run on that Node, but Pods **without toleration will be blocked**.  
- Tolerated Pods can still run on any other Nodes (not just the tainted one).  
- Taints + tolerations help Kubernetes decide **which Pods can run on which Nodes**.

---

## ✅ Summary

- **Taint = Node-side restriction.**  
- **Toleration = Pod-side permission.**  
- Together, they control **Pod placement** in a cluster.  

---