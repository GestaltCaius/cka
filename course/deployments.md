Managing Kubernetes Applications with Deployments
===

# Update the App to a New Version of the Code

```sh
# Edit the beebox-web deployment image:
kubectl edit deployment beebox-web

# ...
# spec:
#   containers:
#   - image: acgorg/beebox-web:1.0.2 # change image version
#     imagePullPolicy: IfNotPresent
#     name: web-server

# ...

# Check the status of your deployment to watch the rolling update occur:
kubectl rollout status deployment.v1.apps/beebox-web
```

# Scale the App to a Larger Number of Replicas

```sh
# Scale the deployment to 5 replicas:
kubectl scale deployment.v1.apps/beebox-web --replicas=5

# View the deployment to watch it scale up:
kubectl get deployment beebox-web

# View the Pods:
kubectl get pods
```