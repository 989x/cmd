# Package Installation and Management

- [Package Installation and Management](#package-installation-and-management)
- [Upgrade all packages on the system](#upgrade-all-packages-on-the-system)
- [Install nginx](#install-nginx)
- [Remove nginx](#remove-nginx)

## Package Installation and Management

A **package manager** helps you install, update, and remove software.

Common package managers:

- **Ubuntu / Debian**: `apt`
- **CentOS / RHEL / Amazon Linux (old)**: `yum`
- **Fedora / newer RHEL-based**: `dnf`

You only use **one** of these, depending on your Linux distribution.

Check your distro:

```bash
cat /etc/os-release
```

## Upgrade all packages on the system

### Ubuntu / Debian (apt)

Update package list, then upgrade:

```bash
sudo apt update
sudo apt upgrade
```

Upgrade including dependencies/removals:

```bash
sudo apt full-upgrade
```

### CentOS / RHEL / Fedora (yum / dnf)

Older systems (yum):

```bash
sudo yum update
```

Newer systems (dnf):

```bash
sudo dnf upgrade
```

## Install nginx

### Ubuntu / Debian (apt)

```bash
sudo apt update
sudo apt install nginx
```

Start and enable nginx:

```bash
sudo systemctl enable --now nginx
```

### CentOS / RHEL / Fedora (yum / dnf)

```bash
sudo yum install nginx   # or: sudo dnf install nginx
sudo systemctl enable --now nginx
```

Check status:

```bash
systemctl status nginx
```

## Remove nginx

### Ubuntu / Debian (apt)

Remove nginx (keep config files):

```bash
sudo apt remove nginx
```

Remove nginx and config files:

```bash
sudo apt purge nginx nginx-common
```

Clean unused packages:

```bash
sudo apt autoremove
```

### CentOS / RHEL / Fedora (yum / dnf)

Remove nginx:

```bash
sudo yum remove nginx    # or: sudo dnf remove nginx
```
