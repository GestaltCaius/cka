Certified Kubernetes Administrator (CKA) Practice Exam: Part 5
===

> Switch to the appropriate context with kubectl:
`kubectl config use-context acgk8s`

* [Upgrading kubeadm clusters](https://v1-23.docs.kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-upgrade/)

# Upgrade All Kubernetes Components on the Control Plane Node

1. Upgrade kubeadm:
```sh
sudo apt-get update && \
sudo apt-get install -y --allow-change-held-packages kubeadm=1.22.2-00
```

2. Drain the control plane node:
```sh
kubectl drain acgk8s-control --ignore-daemonsets
```

3. Plan the upgrade:
```sh
sudo kubeadm upgrade plan v1.22.2
```

4. Apply the upgrade:
```sh
sudo kubeadm upgrade apply v1.22.2
```

5. Upgrade kubelet and kubectl:
```sh
sudo apt-get update && \
sudo apt-get install -y --allow-change-held-packages kubelet=1.22.2-00 kubectl=1.22.2-00
```

6. Reload:
```sh
sudo systemctl daemon-reload
```

7. Restart kubelet:
```sh
sudo systemctl restart kubelet
```

8. Uncordon the control plane node:
```sh
kubectl uncordon acgk8s-control
```

# Upgrade All Kubernetes Components on the Worker Node

1. Drain the worker1 node:
```sh
kubectl drain acgk8s-worker1 --ignore-daemonsets --force
```

We use `--force`, because if the worker node had standalone pods the drain command would fail.

2. SSH into the node:
```sh
ssh acgk8s-worker1
```

3. Install a new version of kubeadm:
```sh
sudo apt-get update && \
sudo apt-get install -y --allow-change-held-packages kubeadm=1.22.2-00
```

4. Upgrade the node:
```sh
sudo kubeadm upgrade node
```

5. Upgrade kubelet and kubectl:
```sh
sudo apt-get update && \
sudo apt-get install -y --allow-change-held-packages kubelet=1.22.2-00 kubectl=1.22.2-00
```

6. Reload:
```sh
sudo systemctl daemon-reload
```

7. Restart kubelet:
```sh
sudo systemctl restart kubelet
```

8. Type `exit` to exit the node.

9. Uncordon the node:
```sh
kubectl uncordon acgk8s-worker1
```

10. Repeat the process above for `acgk8s-worker2` to upgrade the other worker node.