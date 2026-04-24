# Disk Management

> Part of [[Storage]] | [[00 - LFCS MOC]]

---

## Viewing Disks & Partitions

```bash
lsblk                              # tree view of block devices + mount points
lsblk -f                           # include filesystem type, UUID, label
sudo blkid                         # UUIDs and filesystem types for all devices
sudo blkid /dev/vdb1               # UUID of a specific partition

fdisk -l                           # list all disks and partitions (old tool)
sudo fdisk -l /dev/vdb             # list partitions on a specific disk
```

---

## Partitioning

### fdisk (MBR / GPT — interactive)

```bash
sudo fdisk /dev/vdb                # open disk for partitioning
```

Key commands inside fdisk:
- `p` — print partition table
- `n` — new partition
- `d` — delete partition
- `t` — change partition type (82 = Linux swap, 83 = Linux, 8e = LVM)
- `w` — write changes and exit
- `q` — quit without saving

### gdisk (GPT only)

```bash
sudo gdisk /dev/vdb                # GPT-aware partitioning (same commands as fdisk)
```

### parted (scriptable)

```bash
sudo parted /dev/vdb               # interactive
sudo parted /dev/vdb mklabel gpt   # create GPT partition table
sudo parted /dev/vdb mkpart primary ext4 1MiB 10GiB
```

---

## Swap

Swap is disk space used as overflow when RAM is full.

### Partition-based Swap

```bash
sudo mkswap /dev/vdb3              # format partition as swap
sudo swapon --verbose /dev/vdb3    # enable swap
sudo swapoff /dev/vdb3             # disable swap
swapon --show                      # list active swap areas
```

### File-based Swap

```bash
# Create 2GB swap file
sudo dd if=/dev/zero of=/swap bs=1M count=2048 status=progress

# Secure permissions (no other users should read swap contents)
sudo chmod 600 /swap

# Format and enable
sudo mkswap /swap
sudo swapon --verbose /swap
swapon --show
```

### Make Swap Persistent (via fstab)

```
/dev/vdb3   none   swap   defaults   0 0
```

See [[Mounting & fstab]] for details.

---

## Monitoring Storage I/O

```bash
sudo apt install sysstat           # install iostat + pidstat

iostat                             # I/O summary since boot
iostat -x 1                        # extended stats, refresh every 1 second
pidstat -d 1                       # per-process I/O stats

# Fields to watch:
# tps        = transfers per second (request frequency)
# kB_read/s  = read throughput
# kB_wrtn/s  = write throughput
```

---

## Network Block Devices (NBD)

Access a block device on a remote server as if it's local.

```bash
# On the NBD server (share a block device):
sudo apt install nbd-server
# Configure /etc/nbd-server/config

# On the NBD client:
sudo apt install nbd-client
sudo modprobe nbd                          # load NBD kernel module
sudo nbd-client server-ip 10809 /dev/nbd0 # connect remote device to /dev/nbd0

# Now /dev/nbd0 behaves like a local block device
```

---

## Exam Tips

> **Exam tip:** After partitioning with `fdisk`, you may need to run `sudo partprobe` or `sudo partx -u /dev/vdb` so the kernel re-reads the partition table without a reboot.

> **Exam tip:** Swap partition type in fdisk = `82`. LVM type = `8e`.

> **Exam tip:** Swap file must have `chmod 600` before `mkswap` — otherwise it's a security risk.

> **Exam tip:** `iostat` shows *historical averages* since boot, not current activity. Use `iostat -x 1` or `pidstat -d 1` for live monitoring.

---

## Related Notes

- [[Filesystems]] — create a filesystem on a partition
- [[Mounting & fstab]] — auto-mount at boot
- [[LVM]] — flexible partitioning without repartitioning
- [[Storage]]
