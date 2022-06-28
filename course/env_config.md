Passing Configuration Data to a Kubernetes Container
===

# Generate an htpasswd File and Store It as a Secret

```sh
# Generate an htpasswd file:
htpasswd -c .htpasswd user

# Create a password you can easily remember (we'll need it again later).
# View the file's contents:
cat .htpasswd

# Create a Secret containing the htpasswd data:
kubectl create secret generic nginx-htpasswd --from-file .htpasswd

# Delete the .htpasswd file:
rm .htpasswd
```

# Create the Nginx Pod

```sh
# View the existing ConfigMap:
kubectl get cm

# Get more info about nginx-config:
kubectl describe cm nginx-config

# Create the pod:
kubectl apply -f pod.yml

# Check the status of your pod and get its IP address:
kubectl get pods -o wide

# Its IP address will be listed once it has a Running status. We'll use this in the final two commands.
# Within the existing busybox pod, without using credentials, verify everything is working:
# We'll see HTML for the 401 Authorization Required page — but this is a good thing, as it means our setup is working as expected.
kubectl exec busybox -- curl <NGINX_POD_IP>

# Run the same command again using credentials (including the password you created at the beginning of the lab) to verify everything is working:
# This time, we'll see the Welcome to nginx! page HTML.
kubectl exec busybox -- curl -u user:<PASSWORD> <NGINX_POD_IP>
```

```yml
# pod.yml

apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx:1.19.1
    ports:
    - containerPort: 80
    volumeMounts:
    - name: config-volume
      mountPath: /etc/nginx
    - name: htpasswd-volume
      mountPath: /etc/nginx/conf
  volumes:
  - name: config-volume
    configMap:
      name: nginx-config
  - name: htpasswd-volume
    secret:
      secretName: nginx-htpasswd
```