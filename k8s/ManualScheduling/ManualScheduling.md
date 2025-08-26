# Manual Pod Scheduling in Kubernetes

## 🔹 Why Do We Need Manual Scheduling?
Normally, Kubernetes uses the **scheduler** to decide which node a Pod should run on.  
However, sometimes you may want to **manually schedule a Pod** to a specific node. Common reasons include:
- Debugging issues on a particular node.
- Running a Pod that requires a **specific hardware resource** (e.g., GPU).
- Ensuring workloads are placed on **dedicated nodes** for compliance/security.
- Learning/demo purposes to understand scheduling.

This can be achieved by setting the `nodeName` field in the Pod specification.

---

## 🔹 Example: Manually Scheduling a Pod
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  nodeName: node01
  containers:
  - name: nginx
    image: nginx
```

Here:
- `nodeName: node01` forces Kubernetes to schedule the Pod on the node named **node01**.
- The scheduler is bypassed entirely — the Pod is sent directly to that node’s kubelet.

---

## 🔹 Why Updating `nodeName` After Pod Creation Doesn’t Work
- The `spec.nodeName` field is **immutable after scheduling**.
- Once a Pod is created, the scheduler (or manual `nodeName`) binds it to a node.
- After binding:
  - The **kubelet** on that node takes responsibility.
  - Changing `nodeName` in the YAML and applying it **won’t move the Pod**.
- If you want to move the Pod to another node:
  - You must **delete** it and **recreate** it with the new `nodeName`.

This behavior prevents “teleporting” Pods across nodes.

---

## 🔹 Behavior in Higher-Level Controllers
Controllers like **Deployments**, **StatefulSets**, and **DaemonSets** manage Pods for you.  
If you update `nodeName` or scheduling rules in their spec:
- **Existing Pods** → stay where they are (unchanged).
- **New Pods** → get scheduled according to the updated rules.

Thus, scheduling rules in controllers only affect **newly created Pods**, not the ones already running.

---

## 🔹 General Rules of Scheduling
- **Pods are immutable regarding scheduling decisions** such as:
  - `nodeName`
  - `PersistentVolumeClaims` (PVC binding)
- **Controllers** (Deployment/StatefulSet/etc.) only apply new rules to **new Pods**.
- To change scheduling of existing Pods, you need to **delete and recreate** them.

---

## 🔹 Key Takeaways
- Use `nodeName` for **manual scheduling** to a specific node.
- `nodeName` is **immutable** after Pod creation.
- For Pods → delete + recreate to move them.
- For Deployments/StatefulSets → updates affect only **new Pods**.
- Manual scheduling is useful, but for production you should prefer:
  - **nodeSelector**
  - **nodeAffinity**
  - **taints and tolerations**

---

## 🔹 Reference
For more details, see:  
[Certified Kubernetes Administrator Course Docs (KodeKloud)](https://github.com/kodekloudhub/certified-kubernetes-administrator-course/blob/master/docs)