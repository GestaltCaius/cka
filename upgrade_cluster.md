Upgrade cluster
===


# Upgrade the Control Plane

```sh
#!/bin/bash

# Upgrade kubeadm:
# `--allow-change-held-packages` is needed since we blocked kubeadm upgrades
sudo apt-get update && \
sudo apt-get install -y --allow-change-held-packages kubeadm=1.22.2-00

# Make sure it upgraded correctly:
kubeadm version

# Drain the control plane node:
kubectl drain k8s-control --ignore-daemonsets

# Plan the upgrade:
sudo kubeadm upgrade plan v1.22.2

# Upgrade the control plane components:
sudo kubeadm upgrade apply v1.22.2

# Upgrade kubelet and kubectl on the control plane node:
sudo apt-get update && \
sudo apt-get install -y --allow-change-held-packages kubelet=1.22.2-00 kubectl=1.22.2-00

# Restart kubelet:
sudo systemctl daemon-reload
sudo systemctl restart kubelet

# Uncordon the control plane node:
kubectl uncordon k8s-control

# Verify the control plane is working:
kubectl get nodes

# If it shows a NotReady status, run the command again after a minute or so. It should become Ready.
```


# Upgrade the Worker Nodes

> Note: In a real-world scenario, you should not perform upgrades on all worker nodes at the same time. Make sure enough nodes are available at any given time to provide uninterrupted service.

Repeat these steps on each worker node.

```sh
#!/bin/bash

# Run the following on the control plane node to drain worker node:
kubectl drain NODE_NAME --ignore-daemonsets --force
# You may get an error message that certain pods couldn't be deleted, which is fine.

# In a new terminal window, log in to worker node:

# Upgrade kubeadm on worker node:
sudo apt-get update && \
sudo apt-get install -y --allow-change-held-packages kubeadm=1.22.2-00
kubeadm version

# Back on worker node 1, upgrade the kubelet configuration on the worker node:
sudo kubeadm upgrade node

# Upgrade kubelet and kubectl on worker node:
sudo apt-get update && \
sudo apt-get install -y --allow-change-held-packages kubelet=1.22.2-00 kubectl=1.22.2-00

# Restart kubelet:
sudo systemctl daemon-reload
sudo systemctl restart kubelet

# From the control plane node, uncordon worker node:
kubectl uncordon NODE_NAME
```