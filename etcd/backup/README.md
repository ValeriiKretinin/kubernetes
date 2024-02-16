# Etcd Backup and Restore Instructions

This document provides detailed instructions on how to deploy the etcd backup solution and restore from a backup if necessary.

## Prerequisites

- A running Kubernetes cluster.
- `kubectl` configured to communicate with your cluster.
- Access to a master node to execute commands for manual restoration processes.

## Deploying the Backup Solution

```sh
kubectl -n kube-system apply -f etcd_backup.yml
```
This cronjob deletes backups that are more than 24 hours old, so make sure this configuration suits you or set your own retention period in backup-purge container

## Restoring backup

Select the required backup version to restore
```sh
ssh yourlogin@stand@master.nix.tech.altenar.net
sudo su
ls -la /etcd_backup/
```
To restore etcd data manually using the values from your existing etcd.env file, execute the following command on your Kubernetes master node:
```sh
source /etc/etcd.env && /usr/local/bin/etcdctl snapshot restore /etcd_backup/etcd-snapshot-VERSION.db \
  --data-dir /var/lib/etcd-restore
```
Also, you can use `etcd_restore.yml` if your cluster is up and running

Before restoring etcd data, we need to copy the environment file from its original location. Execute the following command to make a copy of the environment file on a master node:
```sh
sudo cp /etc/etcd.env etcd.env
sudo chown $(whoami) etcd.env
```
Create a ConfigMap
```sh
kubectl create configmap etcd-env --from-env-file=etcd.env -n kube-system
```
Apply job
```sh
kubectl apply -f etcd_restore.yml -n kube-system
```

By default, the Job will restore the latest available backup version.

Specifying a Specific Version:
To restore a specific version, set the value in the `ETCD_BACKUP_VERSION` environment variable with the version you want to restore.

```yaml
env:
  - name: ETCD_BACKUP_VERSION
    value: "20240206_130002" # Replace "20240206_130002" with the version you want to restore
```

After successfully restoring etcd data, you can safely remove the etcd-env ConfigMap from the cluster using the following command:

```sh
kubectl delete configmap etcd-env -n kube-system
```
This cleanup step ensures that the ConfigMap, which is no longer needed after the restoration, is removed from the cluster.