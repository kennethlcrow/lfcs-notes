# Storage

> Exam weight: **20%** | Part of [[00 - LFCS MOC]]

---

## Domain Overview

Storage covers managing disks, partitions, filesystems, and how they are mounted. It also includes LVM for flexible storage, swap management, and remote filesystems.

### Topics

- [[Disk Management]] — partitioning, swap, block devices
- [[Filesystems]] — creating, checking, and mounting filesystems
- [[Mounting & fstab]] — persistent mounts, mount options, NFS
- [[LVM]] — Logical Volume Manager: flexible, resizable storage

---

## Quick Reference

```bash
lsblk                              # block device tree (disks → partitions → mount points)
lsblk -f                           # include filesystem type and UUID
sudo blkid /dev/vdb1               # get UUID of a partition
df -h                              # filesystem usage (mounted only)
findmnt                            # all mount points
findmnt -t xfs,ext4                # only real filesystems
```

---

## Device Naming

| Name | Meaning |
|---|---|
| `/dev/sda` | First SATA/SCSI disk |
| `/dev/sdb` | Second SATA/SCSI disk |
| `/dev/vda` | First virtual disk (KVM/cloud) |
| `/dev/vdb1` | First partition on second virtual disk |
| `/dev/mapper/vg-lv` | LVM logical volume |

---

## Exam Tips

> **Exam tip:** Use UUIDs in `/etc/fstab` rather than device names (`/dev/sdX`). Device names can change if cables are reconnected in different order; UUIDs don't.

> **Exam tip:** Always run `sudo systemctl daemon-reload` after editing `/etc/fstab` so systemd picks up the changes without a reboot.

> **Exam tip:** Filesystem checks (`xfs_repair`, `fsck`) require the filesystem to be **unmounted** first.

> **Exam tip:** LVM allows resizing live volumes — useful exam scenario. Know `lvextend` + `resize2fs`/`xfs_growfs`.

---

## Related Notes

- [[Disk Management]]
- [[Filesystems]]
- [[Mounting & fstab]]
- [[LVM]]
