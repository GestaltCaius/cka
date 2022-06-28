Exploring Kubernetes Networking
===

> Pods and nodes are stuck in the NotReady state. Let's investigate.

# Fix the Issue Causing Pods Not to Start Up

```sh
# List the pods to check their status:
kubectl get pods

# Check the node status:
kubectl get nodes

# It looks like the nodes are NotReady.

# Describe a node to see if you can get more info:
kubectl describe node k8s-worker1
# It looks like kube-proxy, a component that handles networking-related tasks, is stuck starting up.

# Check the status of the networking plugin pods:
kubectl get pods -n kube-system
# The networking pods seem to be missing. Most likely, a networking plugin was never installed.

# Install the Calico networking plugin:

kubectl apply -f https://docs.projectcalico.org/v3.15/manifests/calico.yaml
# or 
kubectl apply -f https://docs.projectcalico.org/v3.15/manifests/calico.yaml

# Check the status of the Nodes and Pods again:
# They should both be Ready after about a minute.
kubectl get nodes
kubectl get pods
```

# Verify You Can Communicate between Pods Using the Cluster Network

```sh
# Verify the two pods can communicate over the network:
kubectl get pods -o wide

# Run curl on the IP address of the cyberdyne-frontend Pod (which will be listed in the output from the previous command):

kubectl exec testclient -- curl <cyberdyne-frontend_POD_IP>

# The result should be HTML of an Nginx page, meaning the Pods are able to communicate.
```