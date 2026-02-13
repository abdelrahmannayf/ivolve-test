![step](screenshots/21.png)
![step](screenshots/lap21.png)


Lab 21: Securing Kubernetes with RBAC and Service Accounts
Objective

The goal of this lab is to secure access to Kubernetes resources using RBAC (Role-Based Access Control) and Service Accounts. Specifically, we will:

Create a namespace for isolation.

Create ServiceAccounts with different permission levels.

Assign roles and role bindings.

Test access control using the kubectl auth can-i command.

Deploy a pod and verify permissions.

Environment

Kubernetes Cluster (Minikube / multi-node)

kubectl CLI installed

Namespace for lab: rbac-lab

Steps & Commands
1. Create a Namespace
kubectl create namespace rbac-lab


This ensures all lab resources are isolated from other workloads.

2. Create Service Accounts
kubectl create serviceaccount user1 -n rbac-lab
kubectl create serviceaccount user2 -n rbac-lab


user1: will have admin privileges.

user2: will have read-only access.

3. Assign ClusterRole to user1
kubectl create clusterrolebinding user1-admin-binding \
--clusterrole=cluster-admin \
--serviceaccount=rbac-lab:user1


This gives user1 full cluster admin rights.

4. Create a Read-Only Role for the Namespace

Create a file read-only-role.yaml:

apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: rbac-lab
  name: read-only
rules:
- apiGroups: [""]
  resources: ["pods", "services"]
  verbs: ["get", "list", "watch"]


Apply it:

kubectl apply -f read-only-role.yaml

5. Bind the Role to user2
kubectl create rolebinding user2-readonly-binding \
--role=read-only \
--serviceaccount=rbac-lab:user2 \
--namespace=rbac-lab


This gives user2 read-only access to pods and services in rbac-lab.

6. Generate Service Account Tokens
kubectl create token user1 -n rbac-lab
kubectl create token user2 -n rbac-lab


These tokens can be used to authenticate as the respective ServiceAccounts.

7. Test Permissions

Deploy a pod in the namespace:

kubectl run nginx --image=nginx -n rbac-lab


Check pods:

kubectl get pods -n rbac-lab


Test user2 permissions:

kubectl auth can-i delete pods --as=system:serviceaccount:rbac-lab:user2 -n rbac-lab


Expected output: no

This confirms that user2 cannot delete pods (read-only).

Observations / Results

user1 has full admin access and can create/delete any resources.

user2 is restricted to read-only actions within rbac-lab.

Deploying a pod as admin works normally.

The kubectl auth can-i command is effective to verify permissions without performing actions.

Summary / Learning Points

Namespaces provide isolation for resources.

ServiceAccounts represent non-human identities in Kubernetes.

Roles and RoleBindings allow fine-grained access control within a namespace.

ClusterRoles and ClusterRoleBindings provide cluster-wide permissions.

Using RBAC properly enhances cluster security by enforcing the principle of least privilege.
