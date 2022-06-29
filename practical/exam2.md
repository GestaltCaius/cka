Certified Kubernetes Administrator (CKA) Practice Exam: Part 2
===

> Switch to the appropriate context with kubectl:
`kubectl config use-context acgk8s`

# Edit the web-frontend Deployment to Expose the HTTP Port

1. Edit the web-frontend deployment in the web namespace:
```
kubectl edit deployment -n web web-frontend
```
2. Change the Pod template to expose port 80 on our NGINX containers:
```
spec:
  containers:
  - image: nginx:1.14.2
    ports:
    - containerPort: 80
```

3. Create a Service to Expose the web-frontend Deployment's Pods Externally
```yml
# web-frontend-svc.yml
apiVersion: v1
kind: Service
metadata:
  name: web-frontend-svc
  namespace: web
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

4. Create the service:
```sh
kubectl create -f web-frontend-svc.yml
```

# Scale Up the Web Frontend Deployment

1. Scale up the deployment:
```sh
kubectl scale deployment web-frontend -n web --replicas=5
```

# Create an Ingress That Maps to the New Service

1. Create a web-frontend-ingress file:
```yml
# web-frontend-ingress.yml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: web-frontend-ingress
  namespace: web
spec:
  rules:
  - http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: web-frontend-svc
            port:
              number: 80
```

2. Create the Ingress:
```sh
kubectl create -f web-frontend-ingress.yml
```