# Kubernetes Node Cleanup (After Accidental kubeadm init)

## Purpose

This document describes how to **clean a node that previously ran `kubeadm init`** and return it to a state that matches **node preparation only** (equivalent to `001-k8s-node-preparing.md`).

This is intended for **internal / personal use** when a cluster was created prematurely and must be fully removed before following the official cluster creation guide.


## Scope

* Apply **ONLY** to nodes that have run:

  * `kubeadm init`
  * or failed / partial `kubeadm init`
* Typically applies to:

  * First control-plane node (e.g. `master-01`)

Do **NOT** apply blindly to all nodes.


## Expected State After Cleanup

After completing this procedure:

* No Kubernetes cluster exists
* No control-plane components are running
* No etcd data remains
* Only the following remain installed:

  * containerd (or other CRI)
  * kubelet / kubeadm / kubectl binaries
  * kernel, sysctl, swap, network settings from node preparation

The node is ready to start **cluster creation from scratch**.


## Cleanup Procedure

### Step 1: Reset kubeadm

Force reset all kubeadm-managed state.

```bash
sudo kubeadm reset -f
```

### Step 2: Remove Kubernetes configuration and data

Delete all cluster-related files and directories.

```bash
sudo rm -rf /etc/kubernetes
sudo rm -rf /var/lib/etcd
sudo rm -rf ~/.kube
sudo rm -rf /home/ubuntu/.kube
```

### Step 3: Clear kubelet runtime state

Ensure no leftover pod or volume state remains.

```bash
sudo rm -rf /var/lib/kubelet/*
```

### Step 4: Restart required services

Only container runtime and kubelet should be running.

```bash
sudo systemctl restart containerd
sudo systemctl restart kubelet
```

### Step 5: Verify no control-plane ports are in use

All Kubernetes control-plane ports must be free.

```bash
sudo ss -lntp | egrep '6443|2379|2380|10250|10257|10259' || true
```

**Expected result:**

* No output


## Notes

* Do **NOT** use `--ignore-preflight-errors` as a workaround
* Always clean the node properly before re-running `kubeadm init`
* This procedure is safe and aligns with official kubeadm behavior
