# Log and System Information

- [Check the SSH log](#check-the-ssh-log)
- [Check the current system time](#check-the-current-system-time)
- [Check the CUBE RAM disk usage](#check-the-cube-ram-disk-usage)
- [Using DNS lookup and traceroute commands](#using-dns-lookup-and-traceroute-commands)

## Check the SSH log

On most systems, SSH server logs are stored in different files depending on the distribution.

### Ubuntu / Debian

SSH log entries are usually in:

```bash
sudo tail -f /var/log/auth.log
````

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

## Check the CUBE RAM disk usage

A RAM disk is usually mounted as a `tmpfs`.
You can check its usage with `df` or `du`.

### Check via `df`

If your CUBE RAM disk is mounted at `/cube` (adjust as needed):

```bash
df -h | grep cube
```

Or show all `tmpfs` filesystems:

```bash
df -h -t tmpfs
```

### Check which files are using space

To see which folders inside the RAM disk are taking space, for example `/cube`:

```bash
du -sh /cube/*
```

`du -sh` shows the size of each sub-folder in a human-readable format.

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
