Kubernetes ReplicaSet & Scaling Guide
📌 What is a ReplicaSet?

A ReplicaSet (RS) in Kubernetes ensures that a specified number of Pod replicas are always running.

If a Pod crashes or a Node goes down, the RS will automatically create a new Pod to maintain the desired state.

ReplicaSet is usually not created directly by users; it’s managed by Deployments, but can be used standalone.

📌 kubectl scale and kubectl edit
1. kubectl scale

Used to change the number of replicas (Pods) for a ReplicaSet or Deployment.

kubectl scale rs `replicaset-name` --replicas=3

2. kubectl edit

Opens the live resource definition in an editor.

You can modify fields like replicas or image directly.

kubectl edit rs `replicaset-name`

📌 Flow when we Edit or Scale

kubectl sends the update request to the Kube API Server.

The API Server validates and stores the new desired state in etcd.

The Controller Manager detects differences between desired and current state.

If replicas are reduced → Pods are terminated.

If replicas are increased → New Pods are created.

For new Pods:

The Scheduler assigns them to Nodes.

The Kubelet on that Node pulls the image and starts containers.

Pod status is updated in etcd and reflected when you run:

kubectl get pods

📌 Fixing ImagePullBackOff when updating image

If you update the ReplicaSet with a new image name but Pods still show ImagePullBackOff, it’s often because ReplicaSet does not automatically replace existing Pods.

You need to delete the old Pods so ReplicaSet creates new ones with the updated image.

# Delete Pods
kubectl delete pod -l name=busybox-pod

# RS will recreate them with the updated image

📌 Updating Image in ReplicaSet
Command
kubectl set image rs `replicaset-name` `container-name`=`new-image`

Example
kubectl set image rs new-replica-set busybox-container=busybox

Verify
kubectl get pods
kubectl describe rs new-replica-set

✅ Summary

ReplicaSet ensures a fixed number of Pods.

Use scale or edit to adjust replicas or configs.

Flow: kubectl → API Server → etcd → Controllers → Scheduler → Kubelet.

After updating image, delete old Pods if they are stuck in ImagePullBackOff.

Use kubectl set image for quick image updates.