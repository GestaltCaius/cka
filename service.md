Exposing Kubernetes Pods Using Services
===

# Expose the Pods from the User-db Deployment as an Internal Service

# Examine the properties of the user-db deployment:
```sh
kubectl get deployment user-db -o yaml
```

In the deployment properties, find the spec and look for the Pod template, paying particular attention to the labels, especially the label and exposed port(s).

* `app=user-db`
* port `TCP 80`

Create a Service that will expose its Pods to other components within the cluster.

Define the Service by using:
```yml
# user-db-svc.yml
apiVersion: v1 
kind: Service 
metadata: 
  name: user-db-svc 
spec: 
  type: ClusterIP 
  selector: 
    app: user-db 
  ports: 
  - protocol: TCP 
    port: 80 
    targetPort: 80
```

Test the Service to make sure it works:
```sh
kubectl exec busybox -- curl user-db-svc
```

# Expose the Pods from the Web-frontend Deployment as an External Service

Now, examine the properties of the frontend deployment:
```sh
kubectl get deployment web-frontend -o yaml.
```

* label: `app=web-frontend`
* port: `TCP 80`

Start creating a Service that will expose its Pods on port 30080 of each cluster node by using vi .

Define the Service by using:
```yml
# web-frontend-svc.yml
apiVersion: v1 
kind: Service 
metadata: 
  name: web-frontend-svc 
spec: 
  type: NodePort 
  selector: 
    app: web-frontend 
  ports: 
  - protocol: TCP 
    port: 80 
    targetPort: 80 
    nodePort: 30080
```

Test the Service from outside the cluster (such as in a browser) by navigating to http://<PUBLIC_IP_ADDRESS>:30080, where `PUBLIC IP ADDRESS` is the public address of the control plane or of one of the worker nodes.

It also works from the busybox pod.