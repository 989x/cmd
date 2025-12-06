## Network

- [Check the machine's IP address](#check-the-machines-ip-address)
- [Show open ports](#show-open-ports)
- [Add a hostname to the machine](#add-a-hostname-to-the-machine)
- [Check the DNS in use on the system](#check-the-dns-in-use-on-the-system)
- [How to map hosts (vi /etc/hosts)](#how-to-map-hosts-vi-etchosts)
- [How to change the SSH port](#how-to-change-the-ssh-port)
- [Install Nginx (Ubuntu / Debian)](#install-nginx-ubuntu--debian)
- [Test connectivity to a specific port (telnet)](#test-connectivity-to-a-specific-port-telnet)

## Check the machine's IP address

Show all network interfaces and IP addresses:

```bash
ip addr
```

Quickly show only IPv4 addresses:

```bash
hostname -I
```

(Older systems may use `ifconfig` instead of `ip`.)

## Show open ports

List listening ports and the processes using them:

```bash
sudo ss -tulpn
```

Common options:

* `-t` → TCP
* `-u` → UDP
* `-l` → listening sockets
* `-p` → show process
* `-n` → don’t resolve names (faster)

Example to show only TCP listening ports:

```bash
sudo ss -tln
```

## Add a hostname to the machine

Show current hostname:

```bash
hostname
```

Set a new hostname (on most modern systems):

```bash
sudo hostnamectl set-hostname myserver
```

You may need to log out and back in (or reboot) to see it everywhere.

## Check the DNS in use on the system

Most common file to see DNS servers:

```bash
cat /etc/resolv.conf
```

Example output:

```text
nameserver 8.8.8.8
nameserver 1.1.1.1
```

On systems with `systemd-resolved`, you can also run:

```bash
systemd-resolve --status
```

## How to map hosts (vi /etc/hosts)

The `/etc/hosts` file maps hostnames to IPs locally.

Open it with `vi`:

```bash
sudo vi /etc/hosts
```

Example entries to add:

```text
127.0.0.1   myapp.local
192.168.1.10   db.internal
```

Basic `vi` steps:

1. Press `i` to enter insert mode and edit.
2. When done, press `Esc`.
3. Type `:wq` and press `Enter` to save and quit.

## How to change the SSH port

1. Open the SSH server config file:

   ```bash
   sudo vi /etc/ssh/sshd_config
   ```

2. Find the line (it may be commented out):

   ```text
   #Port 22
   ```

   Change it to e.g.:

   ```text
   Port 2222
   ```

3. Save and exit (`:wq` in `vi`).

4. Restart the SSH service:

   ```bash
   sudo systemctl restart sshd   # some distros use 'sshd'
   # or
   sudo systemctl restart ssh    # on Ubuntu/Debian
   ```

5. Update the firewall (example with UFW):

   ```bash
   sudo ufw allow 2222/tcp
   ```

Next time you connect with SSH:

```bash
ssh -p 2222 user@server-ip
```

## Install Nginx (Ubuntu / Debian)

To install Nginx using `apt` on a Debian-based system like Ubuntu, follow these steps.

### 1. Update the package list

Make sure you have the latest package information:

```bash
sudo apt update
```

### 2. Install Nginx

Install the Nginx package:

```bash
sudo apt install nginx
```

You may be prompted to confirm the installation; type `Y` and press Enter.

### 3. Start Nginx (if not already running)

Nginx often starts automatically after installation. To start it explicitly:

```bash
sudo systemctl start nginx
```

### 4. Enable Nginx to start on boot

Ensure Nginx starts automatically when the server reboots:

```bash
sudo systemctl enable nginx
```

### 5. Check Nginx status

Verify that Nginx is running and active:

```bash
sudo systemctl status nginx
```

## Test connectivity to a specific port (telnet)

You can use `telnet` to check whether a remote host and port are reachable over TCP.  
This is useful for testing web servers, APIs, or any TCP-based service.

Install telnet (if not already installed):

```bash
sudo apt install telnet    # Ubuntu / Debian
sudo yum install telnet    # CentOS / RHEL
````

Test the connection to an IP and port:

```bash
telnet 103.138.176.223 80
```

Example output:

```bash
Trying 103.138.176.223...
Connected to 103.138.176.223.
Escape character is '^]'.
^CConnection closed by foreign host.
```

Notes:

* **Connected** means the port is open and reachable.
* **Connection refused** means the service is not listening on that port.
* **Timed out** usually indicates a firewall or network blocking the connection.
