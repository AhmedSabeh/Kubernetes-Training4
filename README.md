# Lab 4 : Kubernetes Service Account with RBAC

## Overview
This project demonstrates how to create a Kubernetes Service Account with Role-Based Access Control (RBAC). It includes:
- Creating a ServiceAccount.
- Defining a Role for read-only access to pods.
- Binding the Role to the ServiceAccount.
- Retrieving the ServiceAccount token.

---

## Step 1: Create a Service Account
Create a ServiceAccount named `pod-reader-sa` in the default namespace:

```sh
kubectl create serviceaccount pod-reader-sa
```

Verify the ServiceAccount:
```sh
kubectl get serviceaccount pod-reader-sa
```

---

## Step 2: Define a Role (pod-reader)
A Role grants permissions within a specific namespace. Create a file named `pod-reader-role.yaml`:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: pod-reader
  namespace: default
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "watch", "list"]
```

Apply the Role:
```sh
kubectl apply -f pod-reader-role.yaml
```

Verify the Role:
```sh
kubectl get role pod-reader -n default
```

---

## Step 3: Bind the Role to the Service Account
Create a RoleBinding to attach the `pod-reader` Role to the `pod-reader-sa` ServiceAccount.

Create a file named `role-binding.yaml`:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: pod-reader-binding
  namespace: default
subjects:
- kind: ServiceAccount
  name: pod-reader-sa
  namespace: default
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

Apply the RoleBinding:
```sh
kubectl apply -f role-binding.yaml
```

Verify the RoleBinding:
```sh
kubectl get rolebinding pod-reader-binding -n default
```

---

## Step 4: Get the ServiceAccount Token
Retrieve the authentication token for the ServiceAccount:

```sh
kubectl get secret $(kubectl get serviceaccount pod-reader-sa -o jsonpath='{.secrets[0].name}') -o jsonpath='{.data.token}' | base64 --decode
```
---

## Conclusion
This guide provides a step-by-step approach to setting up a Kubernetes ServiceAccount with limited permissions using RBAC. This setup enhances security by restricting API access.

