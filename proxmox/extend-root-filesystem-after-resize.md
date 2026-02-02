# Filename: extend-root-filesystem-after-resize.md


## Goal
After increasing a VM disk size (e.g., to 50G), `df -h /` may still show the old root size.
Reason: the disk grew, but the root partition and filesystem were not expanded.


## Minimal Steps (Ubuntu, ext4 root on /dev/sda1)

### 1) Verify current state
```bash
lsblk
df -h /
lsblk -f
```

### 2) Install growpart

```bash
sudo apt update
sudo apt install -y cloud-guest-utils
```

### 3) Expand root partition (partition 1 on /dev/sda)

```bash
sudo growpart /dev/sda 1
```

### 4) Expand ext4 filesystem on root

```bash
sudo resize2fs /dev/sda1
```

### 5) Confirm result

```bash
lsblk
df -h /
```


## Example Logs (same flow, UUID masked for readability)

### Example A: Disk is 50G but `/` still ~9G (kubedev-master-01)

```bash
ubuntu@kubedev-master-01:~$ df -h
Filesystem    Size    Used   Avail   Use%   Mounted on
tmpfs         392M    3.3M    389M     1%   /run
/dev/sda1     8.7G    5.1G    3.6G    59%   /
tmpfs         2.0G       0    2.0G     0%   /dev/shm
tmpfs         5.0M       0    5.0M     0%   /run/lock
/dev/sda16    881M    117M    703M    15%   /boot
/dev/sda15    105M    6.2M     99M     6%   /boot/efi
tmpfs         392M     12K    392M     1%   /run/user/1000
```

```bash
ubuntu@kubedev-master-01:~$ df -h /
Filesystem   Size  Used Avail Use% Mounted on
/dev/sda1    8.7G  5.1G  3.6G  59% /
```

### Example B: Root is ext4 on /dev/sda1 (kubedev-lb-01)

```bash
ubuntu@kubedev-lb-01:~$ sudo su
root@kubedev-lb-01:/home/ubuntu# lsblk -f
NAME      FSTYPE    FSVER   LABEL            UUID         FSAVAIL  FSUSE%  MOUNTPOINTS
sda
├─sda1    ext4      1.0     cloudimg-rootfs  123a-b123    5.4G      37%    /
├─sda14
├─sda15   vfat      FAT32   UEFI             456b-c456    98.2M      6%    /boot/efi
└─sda16   ext4      1.0     BOOT             789c-d789    702.4M    13%    /boot
sr0       iso9660           cidata           2026-01-19   -
```


## Only If Step 3 Fails

If `growpart` cannot expand `/dev/sda1`, capture the partition table:

```bash
sudo fdisk -l /dev/sda
```
