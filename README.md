# Kubernetes Repository

This repository is dedicated to Kubernetes (k8s) configurations and scripts for managing the backup and restoration of etcd data. Etcd is a key-value store used by Kubernetes to store all its data, making its management critical for cluster reliability and disaster recovery.

## Contents

- **Etcd Backup**: Automated backup configurations using CronJobs.
- **Etcd Restore**: Job definitions for restoring etcd data from backups.

## Getting Started

To use the configurations in this repository, clone it to your local machine or download the specific YAML files you need. Ensure you have `kubectl` configured and have the necessary permissions to deploy resources to your Kubernetes cluster.
