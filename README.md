# Linux Practice Notes

This repository contains short, focused notes and scripts for practicing
Linux fundamentals and Kubernetes node preparation.

## Structure

### k8s/
Scripts for Kubernetes node and cluster preflight checks.

- `k8s-preflight.sh`  
  Kubernetes node preflight validation script.  
  Checks swap, kernel modules, sysctl, container runtime, kubelet,
  Kubernetes binaries, and basic cluster connectivity.

- `preflight-port-guard.sh`  
  Network/port safety checks to detect port conflicts before deploying
  Kubernetes or load balancers.

### securezone2/load-balance/
Notes related to Linux fundamentals and load balancer setup.

- `servers-apache-nginx-setup.md`  
  Apache and Nginx installation and basic configuration notes.

- `vip-setup-mainnode.md`  
  Virtual IP (VIP) configuration for main/load-balancer nodes.

- `linux-basics.md`  
  File and directory commands, editing files, copy/move/delete,
  curl usage, memory and disk basics.

- `linux-network-basics.md`  
  IP addressing, open ports, hostname, DNS tools, `/etc/hosts`,
  SSH port configuration.

- `linux-package-management.md`  
  Package management using `apt`, `yum`, `dnf`,
  installing, upgrading, and removing packages.

- `linux-log-and-system-info.md`  
  System logs, SSH logs, system time, disk and memory usage,
  DNS lookup tools, and network diagnostics.

## Purpose

- Practice Linux administration fundamentals
- Prepare and validate Kubernetes nodes before `kubeadm init/join`
- Keep concise, reusable notes for lab and real-world environments
