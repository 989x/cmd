# Network

- [Check the machine's IP address](#check-the-machines-ip-address)
- [DNS & Host Configuration](#dns--host-configuration)

  - [Check the DNS in use](#check-the-dns-in-use)
  - [Map hosts (/etc/hosts)](#map-hosts-etchosts)
  - [Add a hostname](#add-a-hostname)

- [DNS & Network Diagnostics](#dns--network-diagnostics)
- [Change SSH port](#change-ssh-port)
- [Test connectivity to a specific port (telnet)](#test-connectivity-to-a-specific-port-telnet)

## Check the machine's IP address

Show all network interfaces and IP addresses:

```bash
ip addr
```

Show IPv4 addresses only:

```bash
hostname -I
```

(Older systems may use `ifconfig`.)

## DNS & Host Configuration

### Check the DNS in use

```bash
cat /etc/resolv.conf
# Example output:
# nameserver 8.8.8.8
# nameserver 1.1.1.1
```

Systemd-based systems:

```bash
systemd-resolve --status
```

### Map hosts (/etc/hosts)

```bash
sudo vi /etc/hosts
# Example entries:
# 127.0.0.1       myapp.local
# 192.168.1.10    db.internal
# (vi tips) i = insert, Esc = exit insert, :wq = save & quit
```

### Add a hostname

```bash
hostname
# (shows current hostname)

sudo hostnamectl set-hostname myserver
# Re-login or reboot may be required to see it everywhere.
```

## DNS & Network Diagnostics

### DNS Lookup

#### Using `dig`:

```bash
dig example.com
# Example (short):
dig +short example.com
# Example (MX):
dig MX example.com
```

#### Using `nslookup`:

```bash
nslookup example.com
# Specify DNS:
nslookup example.com 8.8.8.8
```

#### Using `host`:

```bash
host example.com
# Only A record (IP):
host -t A example.com
```

### Traceroute

```bash
# Install (Ubuntu/Debian):
sudo apt install traceroute
# Run:
traceroute example.com
# Alternative:
tracepath example.com
```

## Change SSH port

```bash
# Edit SSH server config:
sudo vi /etc/ssh/sshd_config
# Change from:
#   #Port 22
# to:
#   Port 2222

# Restart SSH:
sudo systemctl restart sshd
# or (Ubuntu/Debian):
sudo systemctl restart ssh

# Allow firewall (UFW example):
sudo ufw allow 2222/tcp

# Next time connect:
ssh -p 2222 user@server-ip
```

## Test connectivity to a specific port (telnet)

```bash
# Install:
sudo apt install telnet    # Ubuntu / Debian
sudo yum install telnet    # CentOS / RHEL

# Test connection:
telnet 103.138.176.223 80
# Example result:
# Trying 103.138.176.223...
# Connected to 103.138.176.223.
# Meaning:
#  - Connected → port open
#  - Connection refused → service not listening
#  - Timed out → firewall or routing issue
```
