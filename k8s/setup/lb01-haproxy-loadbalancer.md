# lb01-haproxy-loadbalancer.md


## Purpose
Configure HAProxy as a Load Balancer for Kubernetes API Server (TCP 6443)


## Environment
Host: k8s99-lb-01  
IP: 172.16.51.190  
Role: Kubernetes API Load Balancer


## Step 1: Install HAProxy
```bash
sudo apt update
sudo apt install haproxy -y
sudo systemctl enable haproxy
```


## Step 2: Backup Default Configuration

```bash
sudo cp /etc/haproxy/haproxy.cfg /etc/haproxy/haproxy.cfg-backup
```


## Step 3: Configure HAProxy

Edit configuration file:

```bash
sudo vi /etc/haproxy/haproxy.cfg
```

Add the following configuration at the end of the file:

```cfg
frontend k8s-api-server
    bind *:6443
    mode tcp
    option tcplog
    default_backend k8s-api-server

backend k8s-api-server
    mode tcp
    balance source
    server k8s99-master-01 172.16.51.191:6443 check
    server k8s99-master-02 172.16.51.192:6443 check
```


## Step 4: Restart HAProxy

```bash
sudo systemctl restart haproxy
sudo systemctl status haproxy
```


## Step 5: Verify Listener

```bash
sudo ss -lntp | grep 6443
```

Expected result:

* HAProxy is listening on `0.0.0.0:6443`


## Notes

* This Load Balancer is used as `--control-plane-endpoint` in `kubeadm init`
* Backend servers must be reachable and running kube-apiserver
* Do not include master nodes that do not exist or are not initialized yet
