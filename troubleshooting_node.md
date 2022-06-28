Troubleshooting a Broken Kubernetes Cluster
===

# Determine What is Wrong with the Cluster

1. Find out which node is having a problem by using `kubectl get nodes`. Identify if a node is in the NotReady state.

2. Get more information on the node by using `kubectl describe node <NODE_NAME>`.

3. Look for the Conditions section of the Node Information and find out what is affecting the node's status, causing it to fail. (It says kubectl has stopped sending info)

4. Log in to the worker 2 node server using the credentials provided: `ssh cloud_user@<PUBLIC_IP_ADDRESS>`

5. Look at the kubelet logs of the worker 2 node by using `sudo journalctl -u kubelet`.

6. Go to the end of the log by pressing Shift + G and see the error messages stating that kubelet has stopped.

7. Look at the status of the kubelet status by using `sudo systemctl status kubelet`, and note whether the kubelet service is running or not.

# Fix the Problem

1. In order to fix the problem, we need to not only start the server but also enable kubelet to ensure that it continues to work if the server restarts in the future. Use `clear` to clear the service status, and then start and enable kubelet by using `sudo systemctl enable kubelet`, followed by `sudo systemctl start kubelet`.

2. Check if kubelet is active by using `sudo systemctl status kubelet`, and note if the service is listed as active (running).

3. Return to the control plane node.

4. Check if all nodes are now in a Ready status by using `kubectl get nodes`. You may have to wait and try the command a few times before all nodes appear as Ready.