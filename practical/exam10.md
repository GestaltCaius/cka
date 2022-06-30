Certified Kubernetes Administrator (CKA) Practice Exam: Part 10
===

> Switch to the acgk8s context:
kubectl config use-context acgk8s

# Determine What Is Wrong with the Broken Node

1. Check the current status of the nodes:
```sh
kubectl get nodes
```

2. Identify the broken node and save the name to a file named broken-node.txt:
```sh
echo acgk8s-worker2 > /k8s/0004/broken-node.txt
```

3. Attempt to determine the cause of the issue:
```sh
kubectl describe node acgk8s-worker2
```

The conditions section says the kubectl has stopped sending its status.
Kubectl must be stopped, and that's what it causing the issue.

# Fix the Problem

1. Access the node using ssh:
```sh
ssh acgk8s-worker2
```

2. Check the kubelet log:
```sh
sudo journalctl -u kubelet
```
Last entry in the log says it's shut down.

3. Check the kubelet status:
```sh
sudo systemctl status kubelet
```

4. Enable kubelet:
```sh
sudo systemctl enable kubelet
```

5. Start kubelet:
```sh
sudo systemctl start kubelet
```

6. Verify that kubelet started successfully:
```sh
sudo systemctl status kubelet
```

7. Return to the control plane node:
```sh
exit
```
8. Check the status of the nodes:
```sh
kubectl get nodes
```
