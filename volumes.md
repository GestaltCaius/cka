Managing Container Storage with Kubernetes Volumes
===

# Create a Pod That Outputs Data to the Host Using a Volume

```yml
# pod-host-mount.yml 
apiVersion: v1
kind: Pod
metadata:
  name: maintenance-pod
spec:
  containers:
  - name: busybox
    image: busybox
    command: ['sh', '-c', 'while true; do echo Success! >> /output/output.txt; sleep 5; done']
    volumeMounts:
    - name: output-vol
      mountPath: /output
  volumes:
  - name: output-vol
    hostPath:
      path: /var/data
```

```sh

Make sure the Pod is up and running by using kubectl get pods and check that maintenance-pod is running, so it should be outputting data to the host system.


ssh cloud_user@<PUBLIC_IP_ADDRESS>

# Create the pod
kubectl create -f maintenance-pod.yml

# check the pod is running
kubectl get pods
```

```sh
# Log into the worker node server using SSH
# Then, look at the output by using cat /var/data/output.txt to see whether the Pod setup was successful.
cloud_user@k8s-worker1:~$ cat /var/data/output.txt 
```
```
Success!
Success!
Success!
Success!
Success!
Success!
Success!
Success!
```

# Create a Multi-Container Pod That Shares Data Between Containers Using a Volume

```yml
# shared-data-pod.yml
apiVersion: v1
kind: Pod
metadata:
    name: shared-data-pod
spec:
    containers:
    - name: busybox1
      image: busybox
      command: ['sh', '-c', 'while true; do echo Success! >> /output/output.txt; sleep 5; done']
      volumeMounts:
      - name: shared-vol
        mountPath: /output
    - name: busybox2
      image: busybox
      command: ['sh', '-c', 'while true; do cat /input/output.txt; sleep 5; done']
      volumeMounts:
      - name: shared-vol
        mountPath: /input
    volumes:
    - name: shared-vol
      emptyDir: {}
```
```sh
kubectl create -f shared-data-pod.yml
kubectl get pods

# To make sure the Pod is working, check the logs for shared-data-pod.yml and specify the second container that is reading the data and printing it to the console
kubectl logs shared-data-pod -c busybox2
```
```
Success!
Success!
Success!
Success!
Success!
Success!
```