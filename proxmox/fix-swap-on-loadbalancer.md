# Fix / Restore Swap (Load-Balancer VM)

Use this guide when swap was disabled or deleted (e.g., `swapoff -a`, removed `/swap.img`, or edited `/etc/fstab`).


## Verify Current Swap Status

```bash
free -h
```


## Restore Swap (Create New /swap.img)

Example: create 4GB swap file.

```bash
sudo fallocate -l 4G /swap.img
sudo chmod 600 /swap.img
sudo mkswap /swap.img
sudo swapon /swap.img
```

Verify:

```bash
free -h
```


## Make Swap Persistent After Reboot (fstab)

Edit `/etc/fstab`:

```bash
sudo vi /etc/fstab
```

Add this line (at the end of the file):

```bash
/swap.img none swap sw 0 0
```

Validate:

```bash
sudo mount -a
```

Re-check:

```bash
free -h
```
