# Configure the auth-gateway Pod to Only Run on k8s-worker2

```sh
# Attach a label to k8s-worker2.
kubectl label nodes k8s-worker2 external-auth-services=true

# Add a nodeSelector to the auth-gateway pod descriptor (in spec section)
vi auth-gateway.yml 

# Delete and re-create the pod:
kubectl delete pod auth-gateway -n beebox-auth
kubectl create -f auth-gateway.yml

# Verify the pod is scheduled on the k8s-worker2 node:
kubectl get pod auth-gateway -n beebox-auth -o wide
```

```yml
# auth-gateway.yml
apiVersion: v1
kind: Pod
metadata:
  name: auth-gateway
  namespace: beebox-auth
spec:
  nodeSelector:
    external-auth-services: "true"
  containers:
  - name: nginx
    image: nginx:1.19.1
    ports:
    - containerPort: 80
```

# Configure the auth-data Deployment's Replica Pods to Only Run on k8s-worker2
```sh
# Add a nodeSelector to the pod template in the deployment spec (it will be the second spec in the file):
vi auth-data.yml

# Update the deployment:
kubectl apply -f auth-data.yml

# Verify the deployment's replicas are all running on k8s-worker2:
kubectl get pods -n beebox-auth -o wide
```


```yml
# auth-data.yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: auth-data
  namespace: beebox-auth
spec:
  replicas: 3
  selector:
    matchLabels:
      app: auth-data
  template:
    metadata:
      labels:
        app: auth-data
    spec:
      nodeSelector:
        external-auth-services: "true"
      containers:
      - name: nginx
        image: nginx:1.19.1
        ports:
        - containerPort: 80
```

---

> Là on a utilisé les labels, mais c'est également possible de le faire avec le nom du node directement.

```yml
spec:
  nodeName: myName
```
```yml
spec:
  nodeSelector:
    myLabel: myValue
```