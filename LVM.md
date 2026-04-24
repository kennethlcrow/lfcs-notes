# LVM

> Part of [[Storage]] | [[00 - LFCS MOC]]

---

## What is LVM?

**Logical Volume Manager** adds a flexible abstraction layer between physical disks and filesystems. Instead of fixed partitions, you get volumes that can be resized, moved, and snapshotted.

### LVM Stack

```
Physical Disks  →  Physical Volumes (PV)  →  Volume Group (VG)  →  Logical Volumes (LV)
/dev/sdb           /dev/sdb (pv)              myvg                   /dev/myvg/data
/dev/sdc           /dev/sdc (pv)                                     /dev/myvg/logs
```

- **PV** (Physical Volume) — a disk or partition prepared for LVM
- **VG** (Volume Group) — a pool that combines one or more PVs
- **LV** (Logical Volume) — a flexible "virtual partition" carved from a VG

---

## Setting Up LVM

### Step 1 — Create Physical Volumes

```bash
sudo pvcreate /dev/vdb /dev/vdc        # prepare disks for LVM
pvs                                    # list physical volumes
pvdisplay                              # detailed PV info
```

### Step 2 — Create a Volume Group

```bash
sudo vgcreate myvg /dev/vdb /dev/vdc   # create VG from PVs
vgs                                    # list volume groups
vgdisplay                              # detailed VG info
```

### Step 3 — Create Logical Volumes

```bash
sudo lvcreate -n data -L 10G myvg      # 10GB LV named "data"
sudo lvcreate -n logs -L 5G myvg       # 5GB LV named "logs"
sudo lvcreate -n backup -l 100%FREE myvg  # use all remaining space

lvs                                    # list logical volumes
lvdisplay                              # detailed LV info
```

### Step 4 — Create Filesystem & Mount

```bash
sudo mkfs.ext4 /dev/myvg/data
sudo mkfs.xfs  /dev/myvg/logs

sudo mkdir /data /logs
sudo mount /dev/myvg/data /data
sudo mount /dev/myvg/logs /logs
```

Add to `/etc/fstab` for persistence:
```
/dev/myvg/data   /data   ext4   defaults   0 2
/dev/myvg/logs   /logs   xfs    defaults   0 2
```

---

## Extending Volumes (Online)

LVM's key advantage — resize without unmounting (for most filesystems).

```bash
# Extend VG first if needed (add a new disk to the pool)
sudo pvcreate /dev/vdd
sudo vgextend myvg /dev/vdd

# Extend the logical volume
sudo lvextend -L +5G /dev/myvg/data     # add 5GB
sudo lvextend -L 20G /dev/myvg/data     # set total size to 20GB
sudo lvextend -l +100%FREE /dev/myvg/data  # use all remaining VG space

# Grow the filesystem to fill the LV
sudo resize2fs /dev/myvg/data           # ext4 (can be done live)
sudo xfs_growfs /logs                   # XFS (use mount point, must be mounted)
```

---

## Reducing a Volume

> **Warning:** XFS cannot be shrunk. Only ext4 supports shrinking.

```bash
# For ext4 only — must unmount first
sudo umount /data
sudo e2fsck -f /dev/myvg/data          # check filesystem first
sudo resize2fs /dev/myvg/data 8G       # shrink filesystem to 8GB
sudo lvreduce -L 8G /dev/myvg/data     # then shrink the LV
sudo mount /dev/myvg/data /data
```

---

## Removing LVM Components

```bash
sudo umount /data
sudo lvremove /dev/myvg/data           # remove logical volume
sudo vgremove myvg                     # remove volume group
sudo pvremove /dev/vdb /dev/vdc        # remove physical volumes
```

---

## Snapshots

LVM snapshots capture a point-in-time state of a volume.

```bash
# Create a snapshot of /dev/myvg/data (reserve 1G for changes)
sudo lvcreate -L 1G -s -n data_snap /dev/myvg/data

# Mount snapshot read-only
sudo mount -o ro /dev/myvg/data_snap /mnt/snap

# Remove snapshot when done
sudo umount /mnt/snap
sudo lvremove /dev/myvg/data_snap
```

---

## RAID with LVM

LVM can create software RAID:

```bash
# RAID 1 (mirror) across 2 PVs
sudo lvcreate --type raid1 -m 1 -L 10G -n data_raid myvg

# RAID 5 across 3 PVs
sudo lvcreate --type raid5 -l 100%FREE -n data_raid5 myvg
```

---

## Useful Commands Summary

```bash
pvs / pvdisplay / pvscan            # physical volume info
vgs / vgdisplay / vgscan            # volume group info
lvs / lvdisplay / lvscan            # logical volume info

vgchange -a y myvg                  # activate a volume group
```

---

## Exam Tips

> **Exam tip:** The order always goes: `pvcreate` → `vgcreate` → `lvcreate` → `mkfs` → `mount`.

> **Exam tip:** After `lvextend`, you **must** also grow the filesystem or the extra space won't be usable. `resize2fs` for ext4, `xfs_growfs` for XFS.

> **Exam tip:** XFS filesystems cannot be shrunk — only grown. Plan storage sizes carefully, or use ext4 if you might need to shrink.

> **Exam tip:** LV paths follow the pattern `/dev/<vg_name>/<lv_name>` or `/dev/mapper/<vg_name>-<lv_name>` — both work.

---

## Related Notes

- [[Disk Management]] — preparing physical disks/partitions
- [[Filesystems]] — creating filesystems on LVs
- [[Mounting & fstab]] — auto-mounting LV-based filesystems
- [[Storage]]
