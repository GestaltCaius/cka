kubectl
===

# Get a List of Persistent Volumes Sorted by Capacity

```sh
# Get a list of persistent volumes sorted by capacity:
kubectl get pv
kubectl get pv -o yaml

# Get all persistent volumes name
kubectl get persistentvolumes -o jsonpath='{.items[*].metadata.name}'

# Get pv sorted by volume capacity (storage)
kubectl get pv --sort-by=.spec.capacity.storage

# Save the list to a file:
kubectl get pv --sort-by=.spec.capacity.storage > /home/cloud_user/pv_list.txt

# List the contents of the file to verify it saved:
cat pv_list.txt
```

# Run a Command Inside the quark Pod's Container to Obtain a Key Value

```sh
# Get the contents of a file from inside the quark pod's container:
kubectl exec quark -n beebox-mobile -- cat /etc/key/key.txt

# Save it to a file:
kubectl exec quark -n beebox-mobile -- cat /etc/key/key.txt > /home/cloud_user/key.txt

# List the contents of the file to verify it saved:
cat key.txt
```

# Create a Deployment Using a Spec File

```sh
# Create a deployment using the deployment spec found in home/cloud_user/deployment.yml:

kubectl apply -f /home/cloud_user/deployment.yml
kubectl get deployments -n beebox-mobile
kubectl get pods -n beebox-mobile
```

```yml
# deployment.yml 

apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  namespace: beebox-mobile
  labels:
    app: beebox-auth
spec:
  replicas: 3
  selector:
    matchLabels:
      app: beebox-auth
  template:
    metadata:
      labels:
        app: beebox-auth
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```

# Delete the beebox-auth Service

```sh
# Delete beebox-auth-svc:
kubectl delete service beebox-auth-svc -n beebox-mobile
```

# Tips

```sh
# Get a sample yaml file

kubectl create deployment tmp --image=nginx --dry-run -o yaml

# Use the --record cmd to show the recorded command when describing an object later
kubectl scale deployment my-deployment --replicas 5 --record


```