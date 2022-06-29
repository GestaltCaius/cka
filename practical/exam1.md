Certified Kubernetes Administrator (CKA) - Practice Exam Part 1
===

# Count the Number of Nodes That Are Ready to Run Normal Workloads

1. Switch to the appropriate context with kubectl:
```sh
kubectl config use-context acgk8s
```

2. Count the number of nodes ready to run a normal workload:
```sh
kubectl get nodes
```

3. Check that the worker nodes can run normal workloads:
```sh
kubectl describe node  acgk8s-worker1
```

4. Scroll to the top of the output and check the list of taints. You should see none.

5. Repeat the steps above for `acgk8s-worker2`. You should see no taints on that node either.

6. Save this number to the file /k8s/0001/count.txt:
```sh
echo 2 > /k8s/0001/count.txt
```

7. Run the verification script to check your work:
```sh
./verify.sh
```

# Retrieve Error Messages from a Container Log

1. Obtain error messages from a container's log:
```sh
kubectl logs -n backend data-handler -c proc
```

2. Return only the error messages:
```sh
kubectl logs -n backend data-handler -c proc | grep ERROR
```

3. Save this output to the file /k8s/0002/errors.txt:
```sh
kubectl logs -n backend data-handler -c proc | grep ERROR > /k8s/0002/errors.txt
```

4. Run the verification script to check your work:
```sh
./verify.sh
```

# Find the Pod with a Label of app=auth in the Web Namespace That Is Utilizing the Most CPU

1. Before doing this step, please wait a minute or two to give our backend script time to generate CPU load. Locate which Pod in the `web` namespace with the label `app=auth` is using the most CPU (in some cases, other Pods may show as consuming more CPU):
```sh
kubectl top pod -n web --sort-by cpu --selector app=auth
kubectl top pod -l app=auth -n web --sort-by=cpu
```

2. Save the name of this Pod to /k8s/0003/cpu-pod.txt:
```sh
echo auth-web > /k8s/0003/cpu-pod.txt
```

3. Run the verification script to check your work:

```sh
./verify.sh
```