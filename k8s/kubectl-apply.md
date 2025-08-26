# 📘 How `kubectl apply` Works in Kubernetes

When you run `kubectl apply -f <file>.yaml`, Kubernetes uses a **3-way merge** strategy to decide what needs to be created, updated, or removed in the cluster.  

---

## 🔹 1. Three Sources of Truth
Kubernetes compares **three different versions** of an object:

1. **Local File**  
   - The YAML/JSON file you provide (`deployment.yaml` etc.).  
   - Represents the desired state you want.

2. **Last Applied Configuration**  
   - Stored inside the live object’s annotations under:
     ```
     metadata.annotations["kubectl.kubernetes.io/last-applied-configuration"]
     ```
   - Saved as JSON whenever you run `kubectl apply`.  
   - Represents the last state that `kubectl apply` successfully applied.

3. **Live Object (Cluster State)**  
   - The object as it currently exists in the Kubernetes cluster.  
   - Includes extra runtime fields added by Kubernetes controllers (like `status`, managedFields, etc.).  

---

## 🔹 2. The 3-Way Merge
- Kubernetes compares:
  - **Local File** vs. **Last Applied Config** → finds **what you changed**.
  - **Live Object** vs. **Last Applied Config** → finds **what the cluster or controllers changed**.
- Then it merges them to determine the final patch to apply.

---

## 🔹 3. Apply Workflow
1. **Check if object exists**
   - If **not found** → `kubectl apply` creates it.
   - If **found** → proceed with merge.

2. **Generate patch**
   - Uses the 3-way diff (local, last applied, live) to build a JSON patch.

3. **Update live object**
   - Sends the patch to the API server.
   - The API server updates the live object accordingly.

4. **Update last applied annotation**
   - Stores the current local file (as JSON) in the object’s annotation:
     ```yaml
     metadata:
       annotations:
         kubectl.kubernetes.io/last-applied-configuration: |
           { ... JSON content of your local file ... }
     ```

---

## 🔹 Key Points
- The **last-applied-configuration** annotation is crucial for detecting changes made by `kubectl apply`.  
- If you use `kubectl edit` or another controller updates the object, those changes remain unless they conflict with your local file.  
- If you use `kubectl replace` instead of `apply`, it overwrites the whole object (no 3-way merge).  

---

## 🔹 Example Flow

1. You run:
   ```bash
   kubectl apply -f deployment.yaml
   ```
   → Deployment is created. Local config stored in `last-applied-configuration`.

2. Later, you change replicas from 2 → 3 in the file and run again:
   ```bash
   kubectl apply -f deployment.yaml
   ```
   → Kubernetes sees difference (local vs last-applied) and patches live object.

3. Controller updates status (e.g., availableReplicas = 3).  
   → This does not affect your last-applied config or future applies.

---

## ✅ Summary
- `kubectl apply` uses **3-way merge**: Local File, Last Applied, Live Object.  
- Updates live object only for changes you made in your local file.  
- Stores your applied config in an annotation for future diffs.  
- Safe and declarative way to manage Kubernetes resources.  