Certified Kubernetes Administrator (CKA) Practice Exam: Part 8
===

> Switch to the acgk8s context:
kubectl config use-context acgk8s

# Create a Networkpolicy That Denies All Access to the Maintenance Pod
1. Check the foo namespace:
```sh
kubectl get pods -n foo
```

2. Check the maintenance pod's labels:
```sh
kubectl describe pod maintenance -n foo
```

3. Create a new YAML file named np-maintenance.yml:
```yml
# np-maintenance.yml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: np-maintenance
  namespace: foo
spec:
  podSelector:
    matchLabels:
      app: maintenance
  policyTypes:
  - Ingress
  - Egress
```

> Ce genre de manifeste permet de bloquer tous les acces par defaut.

4. Create the NetworkPolicy:
```sh
kubectl create -f np-maintenance.yml
```

# Create a Networkpolicy That Allows All Pods in the users-backend Namespace to Communicate with Each Other Only on a Specific Port

1. Label the users-backend namespace:
```sh
kubectl label namespace users-backend app=users-backend
```

Using this label, we'll be able to select that namespace in the network policy.

2. Create a YAML file named np-users-backend-80.yml:
```yml
# np-users-backend-80.yml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: np-users-backend-80
  namespace: users-backend
spec:
  podSelector: {} # required, but we let it empty. Empty = select all pods
  policyTypes:
  - Ingress
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          app: users-backend
    ports:
    - protocol: TCP
      port: 80
```

3. Create the NetworkPolicy:
```sh
kubectl create -f np-users-backend-80.yml
```