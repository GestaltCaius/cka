Certified Kubernetes Administrator (CKA) Practice Exam: Part 4
===

# Back Up the etcd Data

1. From the terminal, log in to the etcd server:
```sh
ssh etcd1
```

2. Back up the etcd data:
```sh
ETCDCTL_API=3 etcdctl snapshot save /home/cloud_user/etcd_backup.db \
--endpoints=https://etcd1:2379 \
--cacert=/home/cloud_user/etcd-certs/etcd-ca.pem \
--cert=/home/cloud_user/etcd-certs/etcd-server.crt \
--key=/home/cloud_user/etcd-certs/etcd-server.key
```

3. Verify the save
```sh
ETCDCTL_API=3 etcdctl --write-out=table snapshot status ./etcd_backup.db 
```
```
+----------+----------+------------+------------+
|   HASH   | REVISION | TOTAL KEYS | TOTAL SIZE |
+----------+----------+------------+------------+
| b245bf41 |        2 |          5 |      20 kB |
+----------+----------+------------+------------+
```

> https://kubernetes.io/docs/tasks/administer-cluster/configure-upgrade-etcd/#built-in-snapshot

# Restore the etcd Data from the Backup

1. Stop etcd:
```sh
sudo systemctl stop etcd
```

2. Delete the existing etcd data:
```sh
sudo rm -rf /var/lib/etcd
```

3. Restore etcd data from a backup:
```sh
sudo ETCDCTL_API=3 etcdctl snapshot restore /home/cloud_user/etcd_backup.db \
--initial-cluster etcd-restore=https://etcd1:2380 \
--initial-advertise-peer-urls https://etcd1:2380 \
--name etcd-restore \
--data-dir /var/lib/etcd
```

To restore the data from a backup, we have to create a temporary etcd that uses the same `/var/lib/etcd` directory.

4. Set database ownership:
```sh
sudo chown -R etcd:etcd /var/lib/etcd
```

Since we created the temp etcd with `sudo` we have to give the user and group `etcd` the permissions on the `/var/lib/etcd` directory.

5. Start etcd:
```sh
sudo systemctl start etcd
```

6. Verify the system is working:
```sh
ETCDCTL_API=3 etcdctl get cluster.name \
--endpoints=https://etcd1:2379 \
--cacert=/home/cloud_user/etcd-certs/etcd-ca.pem \
--cert=/home/cloud_user/etcd-certs/etcd-server.crt \
--key=/home/cloud_user/etcd-certs/etcd-server.key
```

We try to get the `cluster.name` key/value pair to check if the data was correctly restored.