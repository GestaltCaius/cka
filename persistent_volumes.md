Using PersistentVolumes in Kubernetes
===

# Create a PersistentVolume That Allows Claim Expansion

Define the Storage Class by using:
```yml
# localdisk.yml
apiVersion: storage.k8s.io/v1 
kind: StorageClass 
metadata: 
  name: localdisk 
provisioner: kubernetes.io/no-provisioner
allowVolumeExpansion: true # set to true to be able to resize volume
```
```
kubectl create -f localdisk.yml.
```

---

Define the PersistentVolume with a size of 1Gi by using:
```yml
# host-pv.yml
kind: PersistentVolume 
apiVersion: v1 
metadata: 
   name: host-pv 
spec: 
   storageClassName: localdisk
   persistentVolumeReclaimPolicy: Recycle 
   capacity: 
      storage: 1Gi 
   accessModes: 
      - ReadWriteOnce 
   hostPath: 
      path: /var/output
```
```
kubectl create -f host-pv.yml
```

```
# Check the status of the PersistenVolume by using 
kubectl get pv
```

# Create a PersistentVolumeClaim


Define the PersistentVolumeClaim with a size of 100Mi by using:
```yml
# host-pvc.yml
apiVersion: v1 
kind: PersistentVolumeClaim 
metadata: 
   name: host-pvc 
spec: 
   storageClassName: localdisk 
   accessModes: 
      - ReadWriteOnce 
   resources: 
      requests: 
         storage: 100Mi
```
```
kubectl create -f host-pvc.yml.
```

Check the status of the PersistentVolume and PersistentVolumeClaim to verify that they have been bound:
```
kubectl get pv
kubectl get pvc
```

# Create a Pod That Uses a PersistentVolume for Storage
Create a Pod that uses the PersistentVolumeClaim by using

```yml
# pv-pod.yml.
apiVersion: v1 
kind: Pod 
metadata: 
   name: pv-pod 
spec: 
    containers: 
      - name: busybox 
        image: busybox 
        command: ['sh', '-c', 'while true; do echo Success! > /output/success.txt; sleep 5; done'] 
        volumeMounts: 
        - name: pv-storage 
          mountPath: /output 
    volumes: 
    - name: pv-storage 
      persistentVolumeClaim: 
      claimName: host-pvc
```
```
kubectl create -f pv-pod.yml.
kubectl get pods.
```

> If you wish, you can log in to the worker node and verify the output data by using cat /var/output/success.txt.