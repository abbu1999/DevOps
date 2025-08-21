# Kubernetes Service: ClusterIP

---

## 📌 What is ClusterIP Service Type?
- **ClusterIP** is the **default service type** in Kubernetes.
- It exposes the service **internally within the cluster only**.
- It creates a **virtual IP address (ClusterIP)** that can only be accessed inside the cluster.

---

## 🔹 Why We Use ClusterIP?
- Used for **internal communication** between pods and services.
- Not accessible from outside the cluster.
- Ideal when applications inside the cluster need to communicate with each other.

---

## 🔹 Key Notes
- If **type is not mentioned**, the default service type is **ClusterIP**.
- If **protocol is not mentioned**, the default protocol is **TCP**.
- We can connect to the service using:
  1. **Service Name** (DNS-based discovery)
  2. **ClusterIP Address**

---

## 📂 Example ClusterIP YAML

```yaml
apiVersion: v1
kind: Service
metadata:
  name: cluster-ip-service
spec:
  type: ClusterIP
  ports:
    - name: http
      targetPort: 80
      port: 80
      protocol: TCP
  # selector:
  #   app: nginx
```

---

✅ In this example:
- Service type: `ClusterIP`
- Port: `80`
- TargetPort: `80`
- Protocol: `TCP` (default)
- Selector is commented → meaning the service will **not forward traffic to any pod** unless the selector is defined.

---