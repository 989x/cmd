# HAProxy VIP Load Balancer Setup (Docker Version)

This document contains the full procedure and troubleshooting steps for creating a **TCP Load Balancer using HAProxy in Docker**, with a **VIP (Virtual IP)** on the HA machine that forwards all traffic to backend containers running on other servers.

This guide is written based on the test scenario in the lab environment.


## 1. System Overview

You have 3 important machines:

| Hostname               | IP Address  | Role                      |
| - | -- | - |
| **lb-fim-ha-01**       | 10.151.1.50 | HAProxy VIP Load Balancer |
| **lab-film-server-01** | 10.151.1.41 | Backend Container Server  |
| **lab-film-server-02** | 10.151.1.42 | Backend Container Server  |

Goal:

> Client calls **VIP:3333** → HAProxy → load balance traffic → **10.151.1.41:8080** or **10.151.1.42:8080**


## 2. Backend Container Setup (Server 01 & Server 02)

On both backend servers, the Apache/HTTPD container must run on port `8080`.

### Check if backend is running

```bash
docker ps -a
```

Expected output example:

```
CONTAINER ID   IMAGE   PORTS
xxxx           httpd   0.0.0.0:8080->80/tcp
```

### Test backend

```bash
curl -sS http://10.151.1.41:8080/
curl -sS http://10.151.1.42:8080/
```

You should see:

```
<html><body><h1>It works!</h1></body></html>
```


## 3. Stopping Apache on Server (If Needed)

If the backend container must be stopped for testing:

### Stop container

```bash
docker update --restart=no web
docker stop web
```

### Verify port is free

```bash
ss -lntp | grep ':8080' || echo "no listener on :8080"
```

### Test again

```bash
curl -v http://10.151.1.41:8080/
```


## 4. Install Docker on HAProxy Machine (`lab-film-ha-01`)

Run:

```bash
sudo apt update
sudo apt install -y docker.io
sudo systemctl enable --now docker
sudo docker run --rm hello-world
```


## 5. Create HAProxy Configuration

### Create folder

```bash
mkdir -p ~/haproxy-vip && cd ~/haproxy-vip
```

### Create `haproxy.cfg`

```bash
nano haproxy.cfg
```

Paste:

```cfg
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


## 6. Validate Config Syntax

```bash
sudo docker run --rm \
  -v ~/haproxy-vip/haproxy.cfg:/usr/local/etc/haproxy/haproxy.cfg:ro \
  haproxy:latest haproxy -c -f /usr/local/etc/haproxy/haproxy.cfg
```

Should output:

```
Configuration file is valid
```


## 7. Run HAProxy in Docker

```bash
sudo docker rm -f haproxy-vip 2>/dev/null || true

sudo docker run -d --name haproxy-vip \
  -p 3333:3333 \
  -v ~/haproxy-vip/haproxy.cfg:/usr/local/etc/haproxy/haproxy.cfg:ro \
  --restart always \
  haproxy:latest
```


## 8. Verify HAProxy

### Check container

```bash
sudo docker ps
```

### Check logs

```bash
sudo docker logs -f haproxy-vip
```

You should NOT see:

```
Cannot assign requested address
```


## 9. Testing Load Balancer

### Local test from HA machine:

```bash
curl -v http://127.0.0.1:3333/
```

Should return backend content.

### External test (from another machine):

```bash
curl -v http://10.151.1.50:3333/
```

### Test TCP only:

```bash
nc -vz 10.151.1.50 3333
```


## 10. What Happens Internally (Traffic Flow Explanation)

1. Client calls `VIP:3333`
2. HAProxy receives connection on `*:3333`
3. HAProxy selects backend server using **round-robin**
4. HAProxy checks health using **TCP check**
5. If `.41` is down → traffic goes to `.42`
6. If `.42` is down → traffic goes to `.41`
7. If both down → connection refused
8. Client never sees backend directly (proxy mode)


## 11. Common Troubleshooting

### Cannot bind to 10.151.1.50:3333

Cause: Container cannot bind host IP
Fix: Use:

```
bind *:3333
```

and publish port using Docker:

```
-p 3333:3333
```

### Backend not responding

Check:

```
curl http://10.151.1.41:8080/
curl http://10.151.1.42:8080/
```

### Check logs

```
docker logs haproxy-vip
```


## 12. Summary

You have successfully created:

* A VIP-based Load Balancer
* HAProxy running inside Docker
* TCP proxying from VIP:3333 → backend servers on :8080
* Health checks + round-robin distribution
* Fully portable configuration for future exams/tests

This file can now be reused for repeated lab exams or deployments.
