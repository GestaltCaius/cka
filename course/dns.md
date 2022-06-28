Using Kubernetes Services with DNS
===

# Perform an Nslookup for a Service in the Same Namespace

```sh
kubectl exec -n web busybox -- nslookup web-frontend
```
```
Server:    10.96.0.10
Address 1: 10.96.0.10 kube-dns.kube-system.svc.cluster.local

Name:      web-frontend
Address 1: 10.110.227.34 web-frontend.web.svc.cluster.local
```

---

```sh
kubectl exec -n web busybox -- nslookup web-frontend.web.svc.cluster.local
```
```
Server:    10.96.0.10
Address 1: 10.96.0.10 kube-dns.kube-system.svc.cluster.local

Name:      web-frontend.web.svc.cluster.local
Address 1: 10.110.227.34 web-frontend.web.svc.cluster.local
```

# Perform an Nslookup For a Service in a Different Namespace

```sh
kubectl exec -n web busybox -- nslookup user-db
```
```
Server:    10.96.0.10
Address 1: 10.96.0.10 kube-dns.kube-system.svc.cluster.local

nslookup: can't resolve 'user-db'
command terminated with exit code 1
```

---

```sh
kubectl exec -n web busybox -- nslookup user-db.data.svc.cluster.local
```
```
Server:    10.96.0.10
Address 1: 10.96.0.10 kube-dns.kube-system.svc.cluster.local

Name:      user-db.data.svc.cluster.local
Address 1: 10.96.127.131 user-db.data.svc.cluster.local
```