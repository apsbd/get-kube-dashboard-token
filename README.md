# Kubernetes Dashboard Access

How to get Kubernetes Dashboard Token

## 1. Create a Service Account

``
kubectl create serviceaccount dashboard-admin-sa -n kube-system
``

## 2. Bind the Service Account to a Cluster Role

Create a yaml file:

``
nano dashboard-admin-sa.yaml
``

```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: dashboard-admin-sa
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: dashboard-admin-sa
  namespace: kube-system
```

Apply the cluster role:

``
kubectl apply -f dashboard-admin-sa.yaml
``

## 3. Examine Service Account Details

``
kubectl describe serviceaccount dashboard-admin-sa -n kube-system
``

## 4. Manually Create a Token:
If a token was not automatically created, you can manually create a token for your service account. Here’s how you can do it:

* Create a secret of type ServiceAccountToken:

``
nano dashboard-admin-sa-token.yaml
``

```
apiVersion: v1
kind: Secret
metadata:
  name: dashboard-admin-sa-token
  namespace: kube-system
  annotations:
    kubernetes.io/service-account.name: dashboard-admin-sa
type: kubernetes.io/service-account-token
```

Apply this with:

``
kubectl apply -f dashboard-admin-sa-token.yaml
``
* After creating the secret, you need to link it to your service account. You can edit the service account and add the secret under secrets:

``
kubectl edit serviceaccount dashboard-admin-sa -n kube-system

```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: dashboard-admin-sa
secrets:
  - name: dashboard-admin-sa-token
```

* Verify the Changes:

``
kubectl describe serviceaccount dashboard-admin-sa -n kube-system
``

``
kubectl get secrets -n kube-system
``

``
kubectl describe secret dashboard-admin-sa-token -n kube-system
``
