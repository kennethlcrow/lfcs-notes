# Filesystems

> Part of [[Storage]] | [[00 - LFCS MOC]]

---

## Common Filesystem Types

| Type | Notes |
|---|---|
| `ext4` | Default on Ubuntu; journaled, mature, reliable |
| `xfs` | Default on RHEL/CentOS; high performance, large files |
| `btrfs` | Copy-on-write, snapshots; newer |
| `vfat` | FAT32 — for USB drives and EFI partitions |
| `swap` | Not a real filesystem — used for swap space |
| `nfs` | Remote filesystem (NFS mount type in fstab) |
| `tmpfs` | RAM-based virtual filesystem (ignore in `df` output) |

---

## Creating Filesystems

```bash
sudo mkfs.ext4 /dev/vdb2           # create ext4 filesystem
sudo mkfs.xfs /dev/vdb1            # create XFS filesystem
sudo mkfs.vfat /dev/vdb4           # create FAT32 filesystem

# With a label (easier to reference in fstab)
sudo mkfs.ext4 -L "mybackups" /dev/vdb2
sudo mkfs.xfs -L "mybackups" /dev/vdb1
```

---

## Checking & Repairing Filesystems

> **Filesystems must be unmounted before checking.**

```bash
sudo umount /dev/vdb1              # unmount first

# XFS
sudo xfs_repair -v /dev/vdb1       # check and repair

# ext4
sudo fsck.ext4 -v -f -p /dev/vdb2
# -v verbose  -f force check  -p preen (auto-fix simple errors)

# Quick ext4 check (recommended)
sudo fsck.ext4 -p /dev/vdb2
```

---

## Filesystem Information

```bash
# ext4 info
sudo tune2fs -l /dev/vdb2          # show filesystem details
sudo dumpe2fs /dev/vdb2 | head -30 # detailed dump

# XFS info (must be mounted)
xfs_info /mybackups

# General
df -h                              # disk usage of mounted filesystems
df -i                              # inode usage
lsblk -f                           # filesystem types and UUIDs
```

---

## Adjusting ext4 Filesystem Options

```bash
sudo tune2fs -L "newlabel" /dev/vdb2       # change label
sudo tune2fs -m 1 /dev/vdb2               # reduce reserved blocks to 1%
sudo tune2fs -i 0 /dev/vdb2               # disable periodic fsck
```

---

## Growing a Filesystem

After extending a partition or LVM volume, grow the filesystem:

```bash
# ext4
sudo resize2fs /dev/vdb2           # grow to fill available space
sudo resize2fs /dev/vdb2 20G       # grow to specific size

# XFS (can only grow, not shrink; must be mounted)
sudo xfs_growfs /mybackups
```

---

## NFS — Network Filesystem

### Server Setup

```bash
sudo apt install nfs-kernel-server
sudo vim /etc/exports
```

```
/etc   127.0.0.1(ro)
/data  10.0.0.0/24(rw,sync,no_subtree_check)
/data  *.example.com(ro,sync,no_subtree_check)
/data  *(ro,sync,no_subtree_check)    # any client
```

**Export options:**

| Option | Meaning |
|---|---|
| `rw` | Read-write |
| `ro` | Read-only |
| `sync` | Write confirmed after data hits disk (safe) |
| `async` | Write confirmed before hitting disk (faster, risky) |
| `no_subtree_check` | Skip subtree checks (recommended, avoids issues with renames) |
| `no_root_squash` | Allow root on client to have root privileges on share |

```bash
sudo exportfs -r                   # re-export (apply changes to /etc/exports)
sudo exportfs -v                   # list active exports with options
man exports                        # full reference
```

### Client Setup

```bash
sudo apt install nfs-common

# Mount manually
sudo mount 192.168.0.5:/data /mnt

# Add to /etc/fstab for auto-mount
# 192.168.0.5:/data   /mnt   nfs   defaults   0 0
```

---

## Exam Tips

> **Exam tip:** `fsck` and `xfs_repair` both require the filesystem to be **unmounted** — attempting to check a live filesystem can corrupt it.

> **Exam tip:** XFS can only be **grown**, not shrunk. ext4 can technically shrink but it's risky and rarely tested.

> **Exam tip:** Don't add spaces between the host and the parenthesized options in `/etc/exports` — `10.0.0.5(rw)` is correct; `10.0.0.5 (rw)` gives everyone read access.

> **Exam tip:** After changing `/etc/exports`, run `sudo exportfs -r` to apply. Without it, the NFS server won't export the new share.

---

## Related Notes

- [[Disk Management]] — creating partitions to put filesystems on
- [[Mounting & fstab]] — mounting and auto-mounting filesystems
- [[LVM]] — logical volumes to create filesystems on
- [[Storage]]
