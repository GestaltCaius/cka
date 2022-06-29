Certified Kubernetes Administrator (CKA) Practice Exam: Part 3
===

> Switch to the appropriate context with kubectl:
`kubectl config use-context acgk8s`

# Create a Service Account

1. Create a service account:
```sh
kubectl create sa webautomation -n web
```

# Create a ClusterRole That Provides Read Access to Pods

1. Define the ClusterRole:
```yml
# pod-reader.yml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "watch", "list"]
```

1. Creat the ClusterRole:
```sh
kubectl create -f pod-reader.yml
```

# Bind the ClusterRole to the Service Account to Only Read Pods in the web Namespace

1. Define the RoleBinding:
```yml
# rb-pod-reader.yml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: rb-pod-reader
  namespace: web
subjects:
- kind: ServiceAccount
  name: webautomation
roleRef:
  kind: ClusterRole
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

2. Create the RoleBinding:
```sh
kubectl create -f rb-pod-reader.yml
```

3. Verify the RoleBinding works:
```sh
kubectl get pods -n web --as=system:serviceaccount:web:webautomation
```