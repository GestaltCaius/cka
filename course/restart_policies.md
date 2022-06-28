Restart policies and Liviness probes
===

# Set a Restart Policy to Restart the Container When It Is Down

```sh
# Find the pod that needs to be modified:
# Take note of the beebox-shipping-data pod's IP address.
kubectl get pods -o wide

# Use the busybox pod to make a request to the pod to see if it is working:
# We will likely get an error message.
kubectl exec busybox -- curl <beebox-shipping-data_ IP>:8080


# Get the pod's YAML descriptor:
kubectl get pod beebox-shipping-data -o yaml > beebox-shipping-data.yml

# Open the file:
# Set the restartPolicy to Always:
# spec:
#   ...
#   restartPolicy: Always
#   ...
```

# Create a Liveness Probe to Detect When the Application Has Crashed
```sh
# Add a liveness probe:
# spec:
#   containers:
#   - ...
#     name: shipping-data
#     livenessProbe:
#       httpGet:
#         path: /
#         port: 8080
#       initialDelaySeconds: 5
#       periodSeconds: 5
#     ...

# Delete the pod:
kubectl delete pod beebox-shipping-data

# Re-create the pod to apply the changes:
kubectl apply -f beebox-shipping-data.yml

# Check the pod status
kubectl get pods -o wide

# If you wait a minute or so and check again, you should see the pod is being restarted whenever the application crashes.
# Check the http response from the pod again (it will have a new IP address since we re-created it):
kubectl exec busybox -- curl <beebox-shipping-data_IP>:8080

# If you wish, you can explore and see what happens as the application crashes and the pod is restarted automatically.
```