Troubleshooting a Broken Kubernetes Application
===

# Identify What is Wrong with the Application

1. Examine the `web-consumer` deployment, which resides in the `web` namespace, and its Pods by using `kubectl get deployment -n web web-consumer`.

1. Get more information by using `kubectl describe deployment -n web web-consumer`, including container specifications and labels that the Pods are using.

1. Look more closely at the Pods by using `kubectl get pods -n web` to see if both Pods are up and running.

1. Get more information about the Pods by using `kubectl describe pod -n web <POD_NAME>`, and evaluate any warning messages that may come up.

1. Look at the logs associated with the container busybox by using `kubectl logs -n web <POD_NAME> -c busybox`.

1. Determine what may be going wrong by reading the output from the container logs.

1. Take a closer look at the pod itself by using `kubectl get pod -n web <POD_NAME> -o yaml` to get the data in the yaml format.

1. Determine which command is causing the errors (in this case, the `while true; do curl auth-db; sleep 5; done` command).

# Fix the Problem

1. Take a closer look at the service by using `kubectl get svc -n web auth-db`.

1. Locate where the service is by using `kubectl get namespaces` and finding the one other non-default namespace called `data`.

1. Check `kubectl get svc -n data` and find the `auth-db` service in this namespace, rather than the `web` namespace.

1. Start resolving the issue by using `kubectl edit deployment -n web web-consumer`.

1. In the spec section, scroll down to find the pod template and locate the `while true; do curl auth-db; sleep 5; done` command.

1. Change the command to `while true; do curl auth-db.data.svc.cluster.local; sleep 5; done` to give the fully qualified domain name of that service. This will allow the web-consumer deployment's Pods to communicate with the service successfully.

1. Check `kubectl get pods -n web` to ensure that the old pods have terminated and the new pods are running successfully.

1. Check the log of one of the new pods by using `kubectl logs -n web <POD-NAME> -c busybox`. This time the pod should be able to communicate successfully with the service.