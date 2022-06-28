Roles, Cluster Roles, RBAC
===

# Create a Role for the dev User

```yml
# pod-reader-role.yml

apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: beebox-mobile
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods", "pods/log"]
  verbs: ["get", "watch", "list"]
```


```sh
# Test access by attempting to list pods as the dev user:
kubectl get pods -n beebox-mobile --kubeconfig dev-k8s-config
# We'll get an error message.

# Create the role:
kubectl apply -f pod-reader-role.yml
```

# Bind the Role to the dev User and Verify Your Setup Works

```yml
# pod-reader-rolebinding.yml

apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: pod-reader
  namespace: beebox-mobile
subjects:
- kind: User
  name: dev
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

```sh
# Create the RoleBinding:
kubectl apply -f pod-reader-rolebinding.yml

# Test access again to verify you can successfully list pods:
# This time, we should see a list of pods (there's just one).
kubectl get pods -n beebox-mobile --kubeconfig dev-k8s-config

# Verify the dev user can read pod logs:
# We'll get an Auth processing... message.
kubectl logs beebox-auth -n beebox-mobile --kubeconfig dev-k8s-config

# Verify the dev user cannot make changes by attempting to delete a pod:
# We'll get an error, which is what we want.
kubectl delete pod beebox-auth -n beebox-mobile --kubeconfig dev-k8s-config
```