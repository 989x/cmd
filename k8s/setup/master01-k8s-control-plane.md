# master01-k8s-control-plane.md


## Purpose
Commands executed on k8s99-master-01 for creating and managing the Kubernetes control plane,
including cluster initialization, CNI installation, metrics, and GUI access preparation.


## Environment
Host: k8s99-master-01  
IP: 172.16.51.191  
Role: Primary Kubernetes Control Plane Node


## 1. Kubelet Node IP Configuration
```bash
echo 'KUBELET_EXTRA_ARGS=--node-ip=172.16.51.191' | sudo tee /etc/default/kubelet
sudo systemctl daemon-reexec
sudo systemctl restart kubelet
```


## 2. Initialize Kubernetes Cluster

```bash
sudo kubeadm init \
  --control-plane-endpoint "172.16.51.190:6443" \
  --apiserver-advertise-address 172.16.51.191 \
  --upload-certs
```


## 3. Configure kubectl (Admin Access)

```bash
mkdir -p $HOME/.kube
sudo cp /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```


## 4. Verify Cluster State

```bash
kubectl get nodes
```


## 5. Install Cilium CLI

```bash
CILIUM_CLI_VERSION=$(curl -s https://raw.githubusercontent.com/cilium/cilium-cli/main/stable.txt)
CLI_ARCH=amd64
if [ "$(uname -m)" = "aarch64" ]; then CLI_ARCH=arm64; fi

curl -L --fail --remote-name-all \
  https://github.com/cilium/cilium-cli/releases/download/${CILIUM_CLI_VERSION}/cilium-linux-${CLI_ARCH}.tar.gz{,.sha256sum}

sha256sum --check cilium-linux-${CLI_ARCH}.tar.gz.sha256sum

sudo tar xzvf cilium-linux-${CLI_ARCH}.tar.gz -C /usr/local/bin
rm -f cilium-linux-${CLI_ARCH}.tar.gz*
```


## 6. Install Cilium (CNI)

```bash
cilium install
cilium status --wait
```


## 7. Verify System Pods and Nodes

```bash
kubectl get pods -n kube-system
kubectl get nodes
```


## 8. Install Metrics Server

```bash
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```


## 9. Edit Metrics Server (Enable Insecure TLS)

```bash
kubectl edit deployment metrics-server -n kube-system
```

Add the following argument:

```yaml
- --kubelet-insecure-tls
```


## 10. Verify Metrics Server

```bash
kubectl get pods -n kube-system
kubectl top nodes
```


## 11. Deploy Headlamp

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/headlamp/main/kubernetes-headlamp.yaml
```


## 12. Expose Headlamp via NodePort

```bash
kubectl get svc -n kube-system
kubectl edit svc headlamp -n kube-system
```

Change:

```yaml
spec:
  type: NodePort
```


## 13. Verify Headlamp Service

```bash
kubectl get svc,node -n kube-system -o wide
```


## 14. Create Headlamp Admin Service Account

```bash
kubectl -n kube-system create serviceaccount headlamp-admin
```


## 15. Grant Cluster Admin Role to Headlamp

```bash
kubectl create clusterrolebinding headlamp-admin \
  --serviceaccount=kube-system:headlamp-admin \
  --clusterrole=cluster-admin
```


## 16. Generate Headlamp Login Token

```bash
kubectl create token headlamp-admin -n kube-system
```


## 17. Retrieve kubeconfig for GUI Tools (Lens / Others)

```bash
cat /root/.kube/config
```


## Notes

* Join commands for additional master and worker nodes are generated automatically during `kubeadm init`
* Network Plugin (Cilium) must be installed before nodes become Ready
* This file intentionally excludes commands for other nodes
