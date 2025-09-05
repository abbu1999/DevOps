# 🖥️ Kubernetes Node Affinity Guide

---

## 📌 What is Node Affinity?

**Node Affinity** in Kubernetes is a set of rules that influence **which nodes a Pod can be scheduled on**, based on **node labels**.  
It works like `nodeSelector` but is **more expressive and flexible**.

- **Taints/Tolerations** → decide *which Pods can be blocked or allowed* on a node.  
- **Node Affinity** → decides *where a Pod prefers or requires to run*.  

---

## 🔎 Checking & Managing Node Labels

1. **Check Node Labels**  
```bash
kubectl describe node node01
Add a Label to a Node

bash
Copy code
kubectl label node node01 size=Large
Remove a Label from a Node

bash
Copy code
kubectl label node node01 size-
⚙️ Types of Node Affinity
Node affinity rules are always defined under:

yaml
Copy code
spec.affinity.nodeAffinity
1️⃣ requiredDuringSchedulingIgnoredDuringExecution
During Scheduling: Pod must be scheduled only on nodes with matching labels.

Ignored During Execution: If labels are later removed, the Pod continues to run (not evicted).

✅ Example use: strict rules (e.g., "Only run on SSD nodes").

2️⃣ preferredDuringSchedulingIgnoredDuringExecution
During Scheduling: Scheduler prefers matching nodes.

If available → schedules on them.

If not → can fall back to other nodes.

Ignored During Execution: If labels are later removed, Pod continues running.

✅ Example use: soft preference (e.g., "Prefer GPU nodes, but run elsewhere if not available").

3️⃣ requiredDuringSchedulingRequiredDuringExecution (❗rarely used)
During Scheduling: Pod must match node labels.

During Execution: If labels change and no longer match, the Pod will be evicted automatically.

✅ Example use: very strict enforcement (less common in production due to evictions).

🔑 Match Expressions
Node affinity uses matchExpressions with:

key → node label key

operator → how to match (In, NotIn, Exists, DoesNotExist, Gt, Lt)

values → acceptable values for the key

📂 Example Pod with Node Affinity
yaml
Copy code
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
    - name: data-process
      image: nginx
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
          - matchExpressions:
              - key: size
                operator: In
                values:
                  - Large
🔎 Explanation:
key: size → looks for the node label size

operator: In → node must have label size with one of the listed values

values: Large → only nodes labeled size=Large qualify

If no node has size=Large, Pod stays Pending.

✅ Summary
Node Affinity → controls where Pods can be scheduled using labels.

Use kubectl label to manage node labels.

Types:

requiredDuringSchedulingIgnoredDuringExecution → strict at scheduling, ignored later.

preferredDuringSchedulingIgnoredDuringExecution → soft rule, fallback allowed.

requiredDuringSchedulingRequiredDuringExecution → strict at scheduling and runtime (evicts if violated).

More powerful and flexible than nodeSelector.

📖 Reference: Kubernetes Official Docs

yaml
Copy code

---

✅ This version fixes your YAML (`operator: In` instead of `Equal`), adds explanations for **operators**, and covers all affinity types.  

Do you want me to generate a **downloadable `.md` file link** for this now?