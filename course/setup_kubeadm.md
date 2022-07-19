Setting up `kubeadm`
===

# Liens utiles

1. [`containerd` prerequisites](https://kubernetes.io/docs/setup/production-environment/container-runtimes/#install-and-configure-prerequisites)
2. [Install `containerd`](https://kubernetes.io/docs/tasks/administer-cluster/migrating-from-dockershim/change-runtime-containerd/#install-containerd)
3. [Installing `kubeadm`, `kubelet` and `kubectl`](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/#installing-kubeadm-kubelet-and-kubectl)
4. 

# Install k8s packages

```sh
#!/bin/bash

# Modules to load on startup
cat <<EOF | sudo tee /etc/modules-load.d/containerd.conf
overlay
br_netfilter
EOF

# Load modules (to avoid restart during setup)
sudo modprobe overlay
sudo modprobe br_netfilter

# Set system configurations for Kubernetes networking:
cat <<EOF | sudo tee /etc/sysctl.d/99-kubernetes-cri.conf
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
net.bridge.bridge-nf-call-ip6tables = 1
EOF

# Apply new settings:
sudo sysctl --system

# Install containerd:
sudo apt-get update && sudo apt-get install -y containerd

# Create default configuration file for containerd:
sudo mkdir -p /etc/containerd

# Generate default containerd configuration and save to the newly created default file:
sudo containerd config default | sudo tee /etc/containerd/config.toml

# Restart containerd to ensure new configuration file usage:
sudo systemctl restart containerd

# Verify that containerd is running.
sudo systemctl status containerd

# Disable swap:
sudo swapoff -a

# Disable swap on startup in /etc/fstab (it removes swap lines with sed):
sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab

# Install dependency packages:
sudo apt-get update && sudo apt-get install -y apt-transport-https curl

# Download and add GPG key:
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -

# Add Kubernetes to repository list:
cat <<EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF

# Update package listings:
sudo apt-get update

# Install Kubernetes packages (Note: If you get a dpkg lock message, just wait a minute or two before trying the command again):
# Make sure you set the same version for all packages!
sudo apt-get install -y kubelet=1.23.0-00 kubeadm=1.23.0-00 kubectl=1.23.0-00

# Turn off automatic updates:
# This way we make sure we update k8s only manually, when we decide to do it. (better control over which version we use)
sudo apt-mark hold kubelet kubeadm kubectl
```

Repeat these steps on the control plane, and on each worker node.

# Init the cluster

We just need to init the cluster on the control plane node.

```sh
#!/bin/bash

# Initialize the Kubernetes cluster on the control plane node using kubeadm (Note: This is only performed on the Control Plane Node):
# We specify the CIDR range of our cluster, and the k8s version we installed on the nodes.
sudo kubeadm init --pod-network-cidr 192.168.0.0/16 --kubernetes-version 1.23.0

# Set kubectl access:
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

# Test access to cluster:
kubectl get nodes

```

# Init network with Calico add-on

As long as we don't setup our networking add-on, the control plane node will stay in the NotReady state.

```sh
#!/bin/bash

# Apply the calico manifest to setup the add-on
kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml

# Check status of the control plane node:
kubectl get nodes
```

# Join the worker node to the cluster

```sh
#!/bin/bash

# In the Control Plane Node, create the token and copy the kubeadm join command (NOTE:The join command can also be found in the output from kubeadm init command):
kubeadm token create --print-join-command

# In both Worker Nodes, paste the kubeadm join command to join the cluster. Use sudo to run it as root:
sudo kubeadm join ...

# In the Control Plane Node, view cluster status (Note: You may have to wait a few moments to allow all nodes to become ready):
kubectl get nodes

```