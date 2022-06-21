Using DaemonSets in Kubernetes
===

> In the lab, we wanted to have a pod running on each node (and future nodes), that cleans up a specific directory on the node.


# Create a DaemonSet Specification YAML File

```sh
# Create the DaemonSet in the cluster:
kubectl apply -f daemonset.yml

# Get a list of pods, and verify a DaemonSet pod is running on each worker node:
kubectl get pods -o wide
```

```yml
# daemonset.yml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: beebox-cleanup
spec:
  selector: # selector, so daemon set knows what pods are being managed by it.
    matchLabels:
      app: beebox-cleanup
  template:
    metadata:
      labels:
        app: beebox-cleanup
    spec:
      containers:
      - name: busybox
        image: busybox:1.27
        command: ['sh', '-c', 'while true; do rm -rf /beebox-temp/*; sleep 60; done']
        volumeMounts:
        - name: beebox-tmp
          mountPath: /beebox-temp
      volumes:
      - name: beebox-tmp
        hostPath:
          path: /etc/beebox/tmp
```