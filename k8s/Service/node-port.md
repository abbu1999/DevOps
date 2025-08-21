# Kubernetes Service: NodePort

------------------------------------------------------------------------

## 📌 What is a Service?

A **Service** in Kubernetes is an abstraction that defines a logical set
of Pods and a policy by which to access them.\
Services enable communication between different components inside or
outside the cluster.

By default, if you do not define a type, the **Service type is
`ClusterIP`**, which makes the Service accessible only within the
cluster.

------------------------------------------------------------------------

## 📌 NodePort Service

A **NodePort Service** exposes the application on each Node's IP at a
static port (the NodePort).\
You can access it using:

    <NodeIP>:<NodePort>

This makes the service accessible from outside the cluster.

------------------------------------------------------------------------

## 📌 Important Properties

### `targetPort`

-   Refers to the port **on the container (Pod)** where the application
    is running.
-   Example: If your Nginx container listens on port `80`, set
    `targetPort: 80`.

### `port`

-   Refers to the **port inside the cluster Service**.
-   Other Pods will use this port to access the Service.
-   Mandatory field.
-   ⚠️ If `targetPort` is not defined, Kubernetes assumes
    `targetPort = port`.

### `nodePort`

-   Refers to the **port exposed externally on each Node**.
-   Range: `30000–32767`.
-   If not defined, Kubernetes will auto-assign one within the range.

------------------------------------------------------------------------

## 📌 Example NodePort Service

``` yaml
apiVersion: v1
kind: Service
metadata:
  name: node-port-service
spec:
  type: NodePort
  ports:
    - name: http
      targetPort: 80
      port: 80 
      nodePort: 30020   # Port Range - 30000–32767
      protocol: TCP
  selector:
    app: nginx
```

------------------------------------------------------------------------

## ⚠️ Common Error: Service Selector Mismatch

When creating a Service, the **selector must match the Pod labels**
defined under:

``` yaml
spec:
  template:
    metadata:
      labels:
```

✅ Correct Example:

``` yaml
selector:
  app: nginx
```

❌ Wrong Example:

``` yaml
selector:
  type: Labels-For-Node-Port-Service
```

(if Pod does not have this label)

If labels don't match, the Service won't find any Pods, and you may see
errors like:

    service not available: no running pod for service <service-name> found

------------------------------------------------------------------------

## 📌 Exposing Service on Minikube

You can expose your NodePort service using:

``` bash
minikube service node-port-service --url
```

This will output the external URL to access your service, for example:

    http://192.168.49.2:30020

If you see an error, double-check: 1. Pods are running
(`kubectl get pods`) 2. Service selector matches Pod labels
(`kubectl describe svc node-port-service`)

------------------------------------------------------------------------

✅ With this setup, you can now expose and test your applications
running on Kubernetes via NodePort!