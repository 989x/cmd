# Web Servers (Apache / Nginx) Setup for Backend Nodes

This document describes how to set up **Apache** or **Nginx** web servers on the backend nodes that will be load-balanced by the main HAProxy node.


## 1. Lab Topology (Backend Nodes)

| Node Name          | IP Address  | Gateway      | Subnet | VLAN/Zone                   | หมายเหตุ                                   |
| ------------------ | ----------- | ------------ | ------ | --------------------------- | ----------------------------------------- |
| lab-film-ha-01     | 10.151.1.50 | 10.151.1.254 | /24    | VFW securezone2 (VLAN 2520) | Load Balancer (HAProxy), ใช้ DNS/Domain ตาม Host |
| lab-film-server-01 | 10.151.1.51 | 10.151.1.254 | /24    | VFW securezone2 (VLAN 2520) | Web Server (Apache/Nginx), 2/4/50         |
| lab-film-server-02 | 10.151.1.52 | 10.151.1.254 | /24    | VFW securezone2 (VLAN 2520) | Web Server (Apache/Nginx), 2/4/50         |

Backends will listen on **port 8080**, and the HAProxy main node will forward traffic to:

- `lab-film-server-01:8080`
- `lab-film-server-02:8080`


## 2. Basic Checks on Each Backend Node

Run the following on **lab-film-server-01** and **lab-film-server-02**:

```bash
hostname -I             # verify IP
ip route                # check default gateway (should be 10.151.1.254)
ping -c 3 10.151.1.50   # test reachability to HAProxy node
```


## 3. Install Docker

For Docker installation steps, please refer to the main load balancer documentation:

**`loadbalancer-haproxy-vip-setup-mainnode.md` – Section: "Install Docker Engine on Ubuntu"**

Alternatively, you can reference the official Docker documentation:

https://docs.docker.com/engine/install/ubuntu/


## 4. Option A – Apache (httpd) Web Server via Docker

The official Apache HTTP Server (**httpd**) Docker image provides a simple and reliable way to deploy an Apache web server inside a container.  
Using Docker ensures portability, isolation, and consistent behavior across backend nodes.

### 4.1 Start Apache container on port 8080

Run on each backend node:

```bash
docker pull httpd

docker run -dit --name my-apache-server -p 8080:80 httpd
```

### 4.2 Test Apache

```bash
curl -sS http://127.0.0.1:8080/ | head
```

Expected output:

```html
<html><body><h1>It works!</h1></body></html>
```


## 5. Option B – Nginx Web Server via Docker

If you prefer Nginx instead of Apache, use this option (or run Nginx on one node and Apache on the other for testing).

### 5.1 Start Nginx container on port 8080

On each backend node:

```bash
# Stop & remove previous "web" container if needed
sudo docker rm -f web 2>/dev/null || true

# Run Nginx container
sudo docker run -d --name web \
  --restart always \
  -p 8080:80 \
  nginx
```

### 5.2 Test Nginx

```bash
curl -sS http://127.0.0.1:8080/ | head
```

Expected HTML beginning with Nginx default page.


## 6. Stopping / Disabling Web Containers (for Failover Tests)

To intentionally bring a backend **down** during tests:

```bash
# Stop container but keep it so you can start again
sudo docker stop web

# Or stop and remove completely
sudo docker rm -f web
```

Verify that port 8080 is no longer listening:

```bash
sudo ss -lntp | grep ':8080' || echo "no listener on :8080"
curl -v http://127.0.0.1:8080/ || true
```


## 7. Notes for HAProxy Integration

* HAProxy on `lab-film-ha-01` is configured to use **TCP mode**, frontend `api` on `*:3333`.
* Backend configuration example:

```cfg
backend web-server
    balance roundrobin
    server server-01 10.151.1.41:8080 check
    server server-02 10.151.1.42:8080 check
```

* As long as each backend responds on `:8080`, the VIP (`10.151.1.50:3333`) will distribute traffic between them.
* When a backend container is stopped, HAProxy health checks (`check`) will mark it as down and automatically stop sending traffic to that node.
