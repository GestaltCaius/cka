Using Kubernetes Ingress
===

# Create a Service to Expose the web-auth Deployment

```sh
# get the deployment pods label
kubectl get deployment web-auth -o yaml

#Response is `app: web-auth`
```

```yml
# web-auth-svc.yml 
apiVersion: v1
kind: Service
metadata:
  name: web-auth-svc
spec:
  type: ClusterIP
  selector:
    app: web-auth
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      name: http
```

```
kubectl create -f web-auth-svc.yml 
```

# Create an Ingress That Maps to the New Service

```yml
# kubectl create -f web-auth-ingress.yml 
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: web-auth-ingress
spec:
  rules:
  - http:
      paths:
      - path: /auth
        pathType: Prefix
        backend:
          service:
            name: web-auth-svc
            port:
              number: 80
```

```sh
kubectl create -f web-auth-ingress.yml 
```
```sh
kubectl describe ingress web-auth-ingress
```
```
Name:             web-auth-ingress
Labels:           <none>
Namespace:        default
Address:          
Default backend:  default-http-backend:80 (<error: endpoints "default-http-backend" not found>)
Rules:
  Host        Path  Backends
  ----        ----  --------
  *           
              /auth   web-auth-svc:80 (192.168.194.67:80,192.168.194.68:80)
Annotations:  <none>
Events:       <none>
```

