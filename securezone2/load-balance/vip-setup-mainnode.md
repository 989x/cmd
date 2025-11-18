# HAProxy VIP Load Balancer Configuration for Main Node (Docker Deployment)

This guide documents the complete setup process for creating a **TCP Load Balancer using HAProxy on Docker**, running on `lab-film-ha-01`.
The HAProxy service listens on **VIP:3333** and forwards incoming traffic to backend container servers:

* Backend 01 → `10.151.1.41:8080`
* Backend 02 → `10.151.1.42:8080`

This document is designed as a reusable reference for lab exams or future deployments.


## 1. Installing Docker Engine on Ubuntu

Reference:
[https://docs.docker.com/engine/install/ubuntu/](https://docs.docker.com/engine/install/ubuntu/)

### Add Docker official GPG key and repository

```bash
sudo apt update
sudo apt install -y ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the Docker APT repository
sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
Components: stable
Signed-By: /etc/apt/keyrings/docker.asc
EOF

sudo apt update
```

### Install Docker Engine and components

```bash
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

### Validate Docker installation

```bash
# Check if Docker is running
sudo systemctl status docker

# If not running:
sudo systemctl start docker

# Run test container
sudo docker run hello-world
```


## 2. Pre-check: Verify Network & Backend Containers

### Verify HAProxy machine can reach VIP

```bash
ping -c 3 10.151.1.50
```

### Test backend containers

```bash
curl -sS http://10.151.1.41:8080/ | head
curl -sS http://10.151.1.42:8080/ | head
```

Expected output:

```
<html><body><h1>It works!</h1></body></html>
```


## 3. Prepare HAProxy configuration directory

```bash
mkdir -p ~/haproxy-vip
cd ~/haproxy-vip
nano haproxy.cfg
```

### `haproxy.cfg` content

```
global
    log stdout format raw local0
    maxconn 2000

defaults
    log     global
    mode    tcp
    option  tcplog
    timeout connect 5s
    timeout client  50s
    timeout server  50s

frontend api
    bind *:3333
    default_backend web-server

backend web-server
    balance roundrobin
    server server-01 10.151.1.41:8080 check
    server server-02 10.151.1.42:8080 check
```


## 4. Run HAProxy using Docker

### Pull HAProxy image

```bash
sudo docker pull haproxy:latest
```

### Remove old container (if exists)

```bash
sudo docker rm -f haproxy-vip 2>/dev/null || true
```

### Start HAProxy container

```bash
sudo docker run -d --name haproxy-vip \
  -p 3333:3333 \
  -v ~/haproxy-vip/haproxy.cfg:/usr/local/etc/haproxy/haproxy.cfg:ro \
  --restart always \
  haproxy:latest
```

### Check logs

```bash
sudo docker logs -f haproxy-vip
```

Expected:

```
[NOTICE] (1) : Loading success.
```


## 5. Connectivity Tests

### Local TCP test

```bash
nc -vz 127.0.0.1 3333
```

Expected:

```
Connection to 127.0.0.1 3333 port [tcp/*] succeeded!
```

### Remote VIP test

```bash
curl -v http://10.151.1.50:3333/
```

### View HAProxy logs (live)

```bash
sudo docker logs -f haproxy-vip
```


## 6. Summary

You have successfully set up:

* Docker Engine on Ubuntu
* HAProxy running inside a Docker container
* VIP listener on port **3333**
* TCP load balancing between two backend servers
* Health-check enabled (`check` option)
* Persistent HAProxy container (`--restart always`)

This configuration can be reused for lab environments, training, or production-like simulations.
