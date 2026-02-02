# Linux & Kubernetes Practice Notes

This repository is a collection of concise, hands-on notes and scripts for
practicing Linux administration fundamentals and preparing nodes for
Kubernetes clusters in lab and real-world environments.

The focus is on understanding what happens **before**, **during**, and
**around** Kubernetes setup—including OS preparation, networking,
load balancing, and basic system operations.

## Repository Structure

### k8s/

Scripts and notes related to Kubernetes node and cluster preparation.

- `k8s-preflight.sh`  
  Kubernetes node preflight validation script.  
  Checks swap configuration, kernel modules, sysctl values, container runtime,
  kubelet status, Kubernetes binaries, and basic cluster connectivity.

- `preflight-port-guard.sh`  
  Network and port safety checks to detect potential port conflicts before
  deploying Kubernetes components or load balancers.

- `cleanup-kubeadm-init.md`  
  Notes for cleaning up a node after a failed or reset `kubeadm init`.

- `master01-k8s-control-plane.md`  
  Control plane setup notes for a Kubernetes master node.

- `lb01-haproxy-loadbalancer.md`  
  HAProxy-based load balancer setup for Kubernetes control plane access.

### securezone2/load-balance/

Notes focused on Linux fundamentals and load balancer-related setup.

- `servers-apache-nginx-setup.md`  
  Apache and Nginx installation and basic configuration notes.

- `vip-setup-mainnode.md`  
  Virtual IP (VIP) configuration for main or load-balancer nodes.

- `linux-basics.md`  
  File and directory operations, editing files, copy/move/delete,
  `curl` usage, and basic memory and disk concepts.

- `linux-network-basics.md`  
  IP addressing, open ports, hostname configuration, DNS tools,
  `/etc/hosts`, and SSH port management.

- `linux-package-management.md`  
  Package management using `apt`, `yum`, and `dnf`,
  including installing, upgrading, and removing packages.

- `linux-log-and-system-info.md`  
  System and SSH logs, system time, disk and memory usage,
  DNS lookup tools, and network diagnostics.

### proxmox/

Notes and fixes related to Proxmox VM management and storage changes.

- `extend-root-filesystem-after-resize.md`  
  Expand the root partition and filesystem after increasing VM disk size
  (example: disk resized to 50G but `/` still shows ~9G until `growpart` + `resize2fs`).
- `fix-swap-on-loadbalancer.md`
  Restore swap when it was disabled or deleted (recreate `/swap.img`, enable with `swapon`, and persist via `/etc/fstab`).
  

## Purpose

- Practice Linux system administration fundamentals
- Understand OS and network requirements for Kubernetes
- Prepare and validate nodes before `kubeadm init` / `kubeadm join`
- Maintain clear, reusable notes for labs, testing, and real environments
