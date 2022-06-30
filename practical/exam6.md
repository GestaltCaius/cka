Certified Kubernetes Administrator (CKA): Practice Exam: Part 6
===

> Switch to the acgk8s context:
kubectl config use-context acgk8s

# Drain the Worker1 Node

1. Attempt to drain the worker1 node:
```sh
kubectl drain acgk8s-worker1
```

The command fails, and tells us what flags to add.

2. Override the errors and drain the node:
```sh
kubectl drain acgk8s-worker1 --delete-local-data --ignore-daemonsets --force
```

# Create a Pod That Will Only Be Scheduled on Nodes with a Specific Label

1. Add the disk=fast label to the worker2 node:
```sh
kubectl label nodes acgk8s-worker2 disk=fast
```

2. Create a YAML file named fast-nginx.yml:
```yml
# fast-nginx.yml
apiVersion: v1
kind: Pod
metadata:
  name: fast-nginx
  namespace: dev
spec:
  nodeSelector:
    disk: fast # label
  containers:
  - name: nginx
    image: nginx
```

The `nodeSelector` makes sure this pod only run on nodes that has the label.

3. Create the fast-nginx pod:
```sh
kubectl create -f fast-nginx.yml
```

4. Check the status of the pod:
```
kubectl get pod fast-nginx -n dev -o wide
```