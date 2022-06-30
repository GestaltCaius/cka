kubectl config use-context acgk8s

Certified Kubernetes Administrator (CKA) Practice Exam: Part 9
===

> Switch to the acgk8s context:
kubectl config use-context acgk8s

# Create a Multi-Container Pod

1. Create a YAML file named multi.yml:
```yml
# multi.yml
apiVersion: v1
kind: Pod
metadata:
  name: multi
  namespace: baz
spec:
  containers:
  - name: nginx
    image: nginx
  - name: redis
    image: redis
```

2. Create the multi-container pod:
```sh
kubectl create -f multi.yml
```

3. Check the status of the pod:
```sh
kubectl get pods -n baz
```

# Create a Pod Which Uses a Sidecar to Expose the Main Container's Log File to Stdout

1. Create a YAML file named logging-sidecar.yml:
```yml
# logging-sidecar.yml
apiVersion: v1
kind: Pod
metadata:
  name: logging-sidecar
  namespace: baz
spec:
  containers:
  - name: busybox1
    image: busybox
    command: ['sh', '-c', 'while true; do echo Logging data > /output/output.log; sleep 5; done']
    volumeMounts:
    - name: sharedvol
      mountPath: /output
  - name: sidecar
    image: busybox
    command: ['sh', '-c', 'tail -f /input/output.log']
    volumeMounts:
    - name: sharedvol
      mountPath: /input
  volumes:
  - name: sharedvol
    emptyDir: {}
```

2. Create the logging-sidecar pod:
```sh
kubectl create -f logging-sidecar.yml
```

3. Check the status of the pod:
```sh
kubectl get pods -n baz
```

4. Check the logging-sidecar logs:
```sh
kubectl logs logging-sidecar -n baz -c sidecar
```