# Mounting & fstab

> Part of [[Storage]] | [[00 - LFCS MOC]]

---

## Manual Mounting

```bash
sudo mount /dev/vdb1 /mnt/         # mount a filesystem
sudo mount -t xfs /dev/vdb1 /mnt/  # specify filesystem type explicitly
sudo umount /mnt/                  # unmount (note: not "unmount")
sudo umount /dev/vdb1              # can also unmount by device

# Mount with options
sudo mount -o ro /dev/vdb2 /mnt              # read-only
sudo mount -o ro,noexec,nosuid /dev/vdb2 /mnt

# Remount with new options (without unmounting)
sudo mount -o remount,rw,noexec,nosuid /dev/vdb2 /mnt
```

---

## Viewing Mounts

```bash
lsblk                              # block device tree with mount points
findmnt                            # all mount points (including virtual)
findmnt -t xfs,ext4                # only real filesystems
mount | grep vdb                   # grep for specific device
```

---

## /etc/fstab — Persistent Mounts

Filesystems listed here are automatically mounted at boot.

```bash
sudo vim /etc/fstab
man fstab                          # field reference
```

### Field Format

```
<device>   <mountpoint>   <fstype>   <options>   <dump>   <pass>
```

| Field | Description |
|---|---|
| Device | Block device, UUID, or NFS path |
| Mount point | Directory to mount at |
| fstype | `xfs`, `ext4`, `swap`, `nfs`, etc. |
| Options | `defaults`, `ro`, `noexec`, etc. |
| Dump | `0` = no backup (almost always 0) |
| Pass | `0`=never check, `1`=check first (root), `2`=check after root |

### Example Entries

```
# Local XFS filesystem
/dev/vdb1                    /mybackups   xfs    defaults   0 2

# Local ext4 filesystem with UUID (preferred)
UUID=ec83bbc6-458e-490c-9188-2d5cc97a0e20   /data   ext4   defaults   0 2

# Swap partition
/dev/vdb3   none   swap   defaults   0 0

# Swap file
/swap   none   swap   defaults   0 0

# NFS remote filesystem
192.168.0.5:/shared   /mnt   nfs   defaults   0 0

# Read-only with custom options
/dev/vdb1   /mybackups   xfs   ro,noexec   0 2
```

### Why UUID Instead of Device Name?

Device names (`/dev/sda`, `/dev/sdb`) depend on which order drives are detected at boot. If cables are swapped or a drive is added, names can shift and the wrong filesystem gets mounted.

UUIDs are permanent identifiers that never change.

```bash
sudo blkid /dev/vdb1                  # get UUID of a specific partition
ls -l /dev/disk/by-uuid/              # list all UUIDs and their devices
```

---

## Applying fstab Without Rebooting

```bash
sudo systemctl daemon-reload          # tell systemd to re-read fstab
sudo mount -a                         # mount everything in fstab not yet mounted
```

---

## Mount Options Reference

### Filesystem-Independent

| Option | Effect |
|---|---|
| `defaults` | rw, suid, exec, auto, nouser, async |
| `ro` | Read-only |
| `rw` | Read-write |
| `noexec` | Cannot execute programs from this filesystem |
| `exec` | Allow execution |
| `nosuid` | Ignore SUID/SGID bits |
| `noatime` | Don't update access time on reads (performance) |
| `auto` | Mount with `mount -a` |
| `noauto` | Don't mount with `mount -a` |
| `user` | Allow regular users to mount |
| `nouser` | Only root can mount (default) |

### Filesystem-Specific

```bash
man xfs                            # XFS-specific mount options (MOUNT OPTIONS section)
man ext4                           # ext4-specific mount options

# XFS example with allocsize
sudo mount -o allocsize=32K /dev/vdb1 /mybackups
```

> Use `findmnt -t xfs,ext4` to verify applied mount options.

---

## Swap Auto-mount

```bash
# In /etc/fstab:
/dev/vdb3   none   swap   defaults   0 0

# After reboot, verify:
swapon --show
```

---

## Exam Tips

> **Exam tip:** After editing `/etc/fstab`, run `sudo mount -a` to catch errors immediately — a typo in fstab can prevent the system from booting.

> **Exam tip:** The pass field: `1` for root filesystem, `2` for everything else, `0` to skip checking (use for NFS, swap, etc.).

> **Exam tip:** `noexec,nosuid` is a good security combo for data-only filesystems (photos, uploads, backups) — programs on them can't run even if written there.

> **Exam tip:** `sudo systemctl daemon-reload` is required after editing fstab to let systemd pick up new mount units.

---

## Related Notes

- [[Filesystems]] — creating filesystems to mount
- [[Disk Management]] — partitions and swap
- [[LVM]] — logical volumes also go in fstab
- [[Storage]]
