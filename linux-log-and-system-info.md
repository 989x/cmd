# Log and System Information

- [Check the SSH log](#check-the-ssh-log)
- [Check the current system time](#check-the-current-system-time)
- [How to check CPU, RAM, and disk usage](#how-to-check-cpu-ram-and-disk-usage)
- [Using DNS lookup and traceroute commands](#using-dns-lookup-and-traceroute-commands)

## Check the SSH log

On most systems, SSH server logs are stored in different files depending on the distribution.

### Ubuntu / Debian

SSH log entries are usually in:

```bash
sudo tail -f /var/log/auth.log
```

Search only for SSH-related messages:

```bash
sudo grep sshd /var/log/auth.log
```

### CentOS / RHEL

SSH log entries are usually in:

```bash
sudo tail -f /var/log/secure
```

Search only for SSH-related messages:

```bash
sudo grep sshd /var/log/secure
```

### Using `journalctl` (systemd-based systems)

You can also view SSH logs via `journalctl`:

```bash
sudo journalctl -u ssh
# or, on some systems:
sudo journalctl -u sshd
```

Follow logs in real time:

```bash
sudo journalctl -u ssh -f
```

## Check the current system time

Show the current date and time:

```bash
date
```

Example output:

```text
Mon Nov 10 22:15:42 +07 2025
```

Show detailed time configuration (timezone, NTP, etc.):

```bash
timedatectl
```

Set the system timezone (example: Bangkok):

```bash
sudo timedatectl set-timezone Asia/Bangkok
```

## How to check CPU, RAM, and disk usage

You often need a quick overview of how busy the system is and whether you are running out of resources.
The tools below are available on most Linux distributions.

### Quick overall view

Use `top` (built-in on almost all systems):

```bash
top
```

Press:

- `q` to quit
- `P` to sort by CPU usage
- `M` to sort by memory usage

If installed, `htop` provides a nicer, colored interface:

```bash
htop
```

### Check CPU usage

See current CPU load and top processes:

```bash
top
```

Show load averages (1, 5, 15 minutes):

```bash
uptime
```

Example:

```text
 14:32:10 up 10 days,  2:15,  2 users,  load average: 0.35, 0.40, 0.25
```

If `mpstat` is available (from `sysstat` package), per-CPU usage:

```bash
mpstat -P ALL 1
```

This shows CPU usage for all cores, updating every second.

### Check RAM usage

Show total, used, and free memory:

```bash
free -h
```

Example:

```text
              total        used        free      shared  buff/cache   available
Mem:           15Gi       4.0Gi       2.0Gi       512Mi       9.0Gi        10Gi
Swap:          2.0Gi       0.0Gi       2.0Gi
```

You can also view memory usage in `top`:

```bash
top
```

Press `M` to sort processes by memory usage.

For a detailed summary (if `vmstat` is installed):

```bash
vmstat -s
```

### Check disk usage

Show usage of all mounted filesystems:

```bash
df -h
```

Check usage of the root filesystem (`/`):

```bash
df -h /
```

See which folders inside a path are using the most space, for example `/var`:

```bash
sudo du -sh /var/*
```

`-s` = summary, `-h` = human-readable sizes (KB/MB/GB).

List block devices and their sizes (disks and partitions):

```bash
lsblk
```

This helps you see how many disks are attached and how they are partitioned.

## Using DNS lookup and traceroute commands

### DNS lookup

You can use `dig`, `nslookup`, or `host` to check DNS records.

#### Using `dig` (recommended)

```bash
dig example.com
```

Show only the IP address (A record):

```bash
dig +short example.com
```

Check a specific record type (e.g., MX records):

```bash
dig MX example.com
```

#### Using `nslookup`

```bash
nslookup example.com
```

Query a specific DNS server:

```bash
nslookup example.com 8.8.8.8
```

#### Using `host`

```bash
host example.com
```

Show only the IP address:

```bash
host -t A example.com
```

### Traceroute

`traceroute` shows the path your packets take to reach a destination.

Install traceroute if not present (Ubuntu/Debian):

```bash
sudo apt install traceroute
```

Basic usage:

```bash
traceroute example.com
```

If `traceroute` is not available, you can try `tracepath`:

```bash
tracepath example.com
```

This helps you diagnose network routing issues or see where latency occurs between your machine and a remote server.
