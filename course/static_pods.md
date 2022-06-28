Using Static Pods in Kubernetes
===

# Create a Manifest for a Static Pod

```sh
# Log in to the Worker Node 1 server:

ssh user@worker1

# Create a static pod manifest file:
sudo vi /etc/kubernetes/manifests/beebox-diagnostic.yml
```

```yml
# beebox-diagnostic.yml
apiVersion: v1
kind: Pod
metadata:
  name: beebox-diagnostic
spec:
  containers:
  - name: beebox-diagnostic
    image: acgorg/beebox-diagnostic:1
    ports:
    - containerPort: 80
```

# Start Up the Static Pod

```sh
# Restart kubelet to start the static pod:
# We don't have to restart the kubelet process, but it speeds up the static pod detection.
sudo systemctl restart kubelet

# Log in to the control plane node
ssh user@control-plane

# Check the status of your static Pod:
kubectl get pods

# Attempt to delete the static Pod using the k8s API:
kubectl delete pod beebox-diagnostic-k8s-worker1

# Check the status of the Pod:
kubectl get pods

# We'll see the Pod was immediately re-created, since it is only a mirror Pod created by the worker kubelet to represent the static Pod.
```