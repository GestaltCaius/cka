Using Init Containers in Kubernetes
===

# Create a Sample Pod That Uses an Init Container to Delay Startup

Le nslookup du init container verifie si le service a un record DNS dans k8s.

```sh
# Create the pod:
kubectl create -f pod.yml

# Check the status of the pod:
# It should remain in the Init status.
kubectl get pods

# Test Your Setup by Creating the Service and Verifying the Pod Starts Up
# Create the service from the shipping-svc.yml file:
kubectl create -f shipping-svc.yml

# Check the status of your pod again:
# It should enter the Running status after about a minute.
kubectl get pods
```

```yml
# pod.yml 
apiVersion: v1
kind: Pod
metadata:
  name: shipping-web
spec:
  containers:
  - name: nginx
    image: nginx:1.19.1
  initContainers:
  - name: shipping-svc-check
    image: busybox:1.27
    command: ['sh', '-c', 'until nslookup shipping-svc; do echo waiting for shipping-svc; sleep 2; done']
```
```yml
# shipping-svc.yml 
apiVersion: v1
kind: Service
metadata:
  name: shipping-svc
spec:
  selector:
    app: shipping-svc
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
---
apiVersion: v1
kind: Pod
metadata:
  name: shipping-backend
  labels:
   app: shipping-svc
spec:
  containers:
  - name: nginx
    image: nginx:1.19.1
```