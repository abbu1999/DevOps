Kubernetes Namespace
📌 What is a Namespace?
A Namespace in Kubernetes is a virtual cluster within a physical cluster. It provides a mechanism for isolating groups of resources within a single cluster. Namespaces are intended for use in environments with many users spread across multiple teams or projects.

Think of namespaces as virtual clusters inside your Kubernetes cluster that help you:

Organize resources

Isolate teams/environments

Apply resource quotas and policies

Control access with RBAC

📌 Why Use Namespaces?
1. Resource Isolation
Prevent naming conflicts (same resource names in different namespaces)

Separate development, staging, and production environments

2. Access Control
Apply RBAC policies to specific namespaces

Limit user/team access to their respective namespaces

3. Resource Management
Apply resource quotas per namespace

Limit resource consumption for different teams/projects

4. Organization
Group related resources together

Logical separation of applications and services

📌 Default Namespaces
Kubernetes comes with these built-in namespaces:

default: Where resources are created if no namespace is specified

kube-system: For system components (pods, services created by Kubernetes itself)

kube-public: Readable by all users (for cluster information)

kube-node-lease: For node lease objects (heartbeats)

📌 Working with Namespaces
Create a Namespace via YAML File (Declarative)
yaml
# namespace.yaml
apiVersion: v1
kind: Namespace
metadata:
  name: finance
  labels:
    environment: production
    team: finance
Apply the file:

bash
kubectl apply -f namespace.yaml
Create a Namespace Imperatively (Command Line)
bash
kubectl create namespace finance
Label a Namespace
Add labels to organize or identify namespaces:

bash
# Add single label
kubectl label namespace finance environment=production

# Add multiple labels
kubectl label namespace finance environment=production team=finance-app department=accounting

# Overwrite existing label (add --overwrite)
kubectl label namespace finance environment=staging --overwrite
Remove a Label from Namespace
bash
# Remove specific label
kubectl label namespace finance environment-

# Remove multiple labels
kubectl label namespace finance team- department-
View Namespaces with Labels
bash
kubectl get namespaces --show-labels

# Detailed view
kubectl describe namespaces
Run a Pod in Specific Namespace
bash
kubectl run redis --image=redis --namespace=finance

# Create deployment in specific namespace
kubectl create deployment redis-deploy --image=redis -n finance

# Apply YAML to specific namespace
kubectl apply -f pod.yaml -n finance
Switch Default Namespace Context
bash
# Set current context to use finance namespace
kubectl config set-context --current --namespace=finance

# Verify current namespace context
kubectl config view --minify | grep namespace
View Resources in Specific Namespace
bash
kubectl get pods --namespace=finance

# Use shorthand
kubectl get pods -n finance

# View all resources in namespace
kubectl get all -n finance

# View specific resource with details
kubectl describe pod redis -n finance
View Resources Across All Namespaces
bash
kubectl get pods --all-namespaces

# Use shorthand
kubectl get pods -A

# View services across all namespaces
kubectl get services -A
Delete Resources in Namespace
bash
# Delete specific pod in namespace
kubectl delete pod redis -n finance

# Delete all resources in namespace
kubectl delete all --all -n finance

# Delete namespace (will delete everything in it)
kubectl delete namespace finance
📌 Practical Example: Finance Namespace Setup
1. Create the finance namespace:
bash
kubectl create namespace finance
2. Add labels to identify the namespace:
bash
kubectl label namespace finance environment=production department=finance app-type=database
3. Deploy a Redis pod in the finance namespace:
bash
kubectl run redis --image=redis --namespace=finance
4. Create a deployment in the finance namespace:
bash
kubectl create deployment finance-app --image=nginx -n finance
5. Verify the resources are running in the correct namespace:
bash
kubectl get all -n finance
kubectl get namespaces --show-labels
6. Access pods across namespaces:
bash
# From default namespace, access finance redis
kubectl exec -it redis -n finance -- redis-cli

# Check service DNS (cross-namespace communication)
# redis.finance.svc.cluster.local
7. Remove specific labels:
bash
kubectl label namespace finance app-type-
8. Switch to finance namespace as default:
bash
kubectl config set-context --current --namespace=finance
# Now all commands will use finance namespace by default
kubectl get pods # No need for -n flag
⚠️ Important Notes
Resource Scope: Most resources (Pods, Services, Deployments) are namespaced, but some (like Nodes, PersistentVolumes, Namespaces themselves) are cluster-scoped

DNS Resolution: Services can be accessed across namespaces using the format: <service-name>.<namespace-name>.svc.cluster.local

Default Context: Always verify your current namespace with kubectl config view --minify | grep namespace

Resource Quotas: Apply ResourceQuota objects to limit resource usage per namespace

Network Policies: Use NetworkPolicy to control traffic between namespaces

API Access: Some resources like StorageClasses are cluster-scoped and cannot be created within namespaces

🔄 Quick Reference Commands
Command	Description
kubectl get namespaces	List all namespaces
kubectl create namespace <name>	Create new namespace
kubectl config set-context --current --namespace=<name>	Set default namespace
kubectl get pods -n <namespace>	Get pods in specific namespace
kubectl get pods -A	Get pods in all namespaces
kubectl label namespace <name> key=value	Add label to namespace
kubectl label namespace <name> key-	Remove label from namespace
kubectl delete namespace <name>	Delete namespace and all its resources
✅ With proper namespace management, you can effectively organize, isolate, and control access to your Kubernetes resources!

