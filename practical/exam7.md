Certified Kubernetes Administrator (CKA) Practice Exam: Part 7
===

> Switch to the acgk8s context:
kubectl config use-context acgk8s

# Create a PersistentVolume

1. Create a YAML file named localdisk.yml:
```yml
# localdisk.yml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: localdisk
provisioner: kubernetes.io/no-provisioner
allowVolumeExpansion: true
```

2. Create a storage class using the YAML file:
```sh
kubectl create -f localdisk.yml
```

3. Create a YAML file named host-storage-pv.yml:
```yml
# host-storage-pv.yml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: host-storage-pv
spec:
  storageClassName: localdisk
  persistentVolumeReclaimPolicy: Recycle # recycle to be able to re-use the volume
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /etc/data
```

4. Create the PersistentVolume:
```sh
kubectl create -f host-storage-pv.yml
```

5. Check the status of the volume:
```sh
kubectl get pv host-storage-pv
```
It is indeed, available.

# Create a Pod That Uses the PersistentVolume for Storage

1. Create a YAML file named host-storage-pvc.yml:
```yml
# host-storage-pvc.yml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: host-storage-pvc
  namespace: auth
spec:
  storageClassName: localdisk
  accessModes:
    - ReadWriteOnce # same as the volume we created
  resources:
    requests:
      storage: 100Mi
```

2. Create the PersistentVolumeClaim * in the same namespace as the pod:
```sh
kubectl create -f host-storage-pvc.yml
```

3. Check the status of the PersistentVolumeClaim:
```
kubectl get pv
```

It is now bound.

4. Verify that the claim is bound to the volume:
```sh
kubectl get pvc -n auth
```

It is bound to the pv we created.

6. Create a YAML file named pv-pod.yml:
```yml
# pv-pod.yml
apiVersion: v1
kind: Pod
metadata:
  name: pv-pod
  namespace: auth
spec:
  containers:
  - name: busybox
    image: busybox
    command: ['sh', '-c', 'while true; do echo success > /output/output.log; sleep 5; done']
    volumeMounts:
    - name: pv-storage # mount the pv
      mountPath: /output
  volumes:
  - name: pv-storage
    persistentVolumeClaim:
      claimName: host-storage-pvc # reference the pv claim
```

7. Create the pod:
```sh
kubectl create -f pv-pod.yml
```

# Expand the PersistentVolumeClaim

1. Edit host-storage-pvc:
```sh
kubectl edit pvc host-storage-pvc -n auth
```

2. Under spec, change the storage value to 200Mi.