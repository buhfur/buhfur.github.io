---
title: LVM Management
parent: RHEL
grand_parent: Linux
nav_order: 80
layout: default
---

# Logical Volume Management (LVM) on RHEL

## Overview
Logical Volume Management (LVM) provides a flexible way to manage disk storage in Linux by abstracting physical storage devices into logical volumes that can be resized or moved without downtime. RHEL uses the `lvm2` package for LVM management.

---

## LVM Components
- **Physical Volume (PV):** A storage device or partition initialized for LVM.
- **Volume Group (VG):** A collection of physical volumes that act as a single storage pool.
- **Logical Volume (LV):** The virtual partition created from a volume group; can host a filesystem.
- **Physical Extents (PE):** The smallest allocatable unit on a physical volume.

---

## LVM Commands Summary

### 1. Managing Physical Volumes
```bash
# Create a physical volume
pvcreate /dev/sdb

# Display physical volumes
pvdisplay
pvs

# Remove a physical volume from LVM
pvremove /dev/sdb


```

### 2. Managing Volume Groups
```bash
# Create a volume group
vgcreate vg_data /dev/sdb /dev/sdc

# Create Volume group with custom PE size ( no rounding in LV creation )
vgcreate -s 1M vgname /dev/sdc 

# Display volume groups
vgdisplay
vgs

# Extend a volume group
vgextend vg_data /dev/sdd

# Reduce a volume group (ensure LV is moved or removed first)
vgreduce vg_data /dev/sdb

# Remove a volume group
vgremove vg_data
```

### 3. Managing Logical Volumes
```bash
# Create a logical volume (size 20G)
lvcreate -L 20G -n lv_storage vg_data

# Create a logical volume using all remaining space
lvcreate -l 100%FREE -n lv_backup vg_data

# Display logical volumes
lvdisplay
lvs

# Remove a logical volume
lvremove /dev/vg_data/lv_storage
```

---

## Filesystem Management
```bash
# Create a filesystem on a logical volume
mkfs.xfs /dev/vg_data/lv_storage

# Mount the logical volume
mkdir /mnt/storage
mount /dev/vg_data/lv_storage /mnt/storage

# Persist mount across reboots
echo '/dev/vg_data/lv_storage /mnt/storage xfs defaults 0 0' >> /etc/fstab
```

---

## Resizing Logical Volumes

### Extend a Logical Volume
```bash
# Extend LV by size
lvextend -L +10G /dev/vg_data/lv_storage

# Extend to use all free space
lvextend -l +100%FREE /dev/vg_data/lv_storage

# Resize filesystem (XFS)
xfs_growfs /mnt/storage

# Resize filesystem (EXT4)
resize2fs /dev/vg_data/lv_storage
```

### Reduce a Logical Volume (EXT4 only)
```bash
# Unmount first
umount /mnt/storage

# Run filesystem check
e2fsck -f /dev/vg_data/lv_storage

# Resize filesystem smaller than LV
resize2fs /dev/vg_data/lv_storage 10G

# Shrink logical volume
lvreduce -L 10G /dev/vg_data/lv_storage

# Mount again
mount /mnt/storage
```

---

## LVM Snapshots
```bash
# Create a snapshot (5G size)
lvcreate -L 5G -s -n lv_storage_snap /dev/vg_data/lv_storage

# List snapshots
lvs

# Merge snapshot back
lvconvert --merge /dev/vg_data/lv_storage_snap
```

---

## LVM Thin Provisioning
```bash
# Create thin pool
lvcreate --type thin-pool -L 100G -n thinpool vg_data

# Create thin volume
lvcreate -V 20G --thinpool thinpool -n lv_thin1 vg_data

# Display thin volumes
lvs -a
```

---

## LVM Maintenance and Troubleshooting

### Display LVM Status
```bash
lvs
vgs
pvs
```

### Remove Missing Physical Volumes
```bash
vgreduce --removemissing vg_data
```

### Repair Volume Group Metadata
```bash
vgcfgrestore vg_data
```

### Check Volume Group Consistency
```bash
vgck vg_data
```

---

## Configuration Files and Metadata
- **/etc/lvm/lvm.conf:** Main LVM configuration file.
- **/etc/lvm/archive/**: Stores metadata backups during LVM operations.
- **/etc/lvm/backup/**: Manual and automatic volume group backups.

---

## Best Practices
- Always ensure backups before resizing or removing volumes.
- Use `vgcfgbackup` before any major LVM operation.
- Prefer XFS for large and growing filesystems (supports only growing, not shrinking).
- Regularly monitor free space with `lvs` and `vgs`.
- Use descriptive names for LVs and VGs to simplify management.

---

## Example Workflow
```bash
# Step 1: Create PVs
pvcreate /dev/sdb /dev/sdc

# Step 2: Create VG
vgcreate vg_storage /dev/sdb /dev/sdc

# Step 3: Create LV
lvcreate -L 50G -n lv_data vg_storage

# Step 4: Format and mount
mkfs.xfs /dev/vg_storage/lv_data
mkdir /mnt/data
mount /dev/vg_storage/lv_data /mnt/data

# Step 5: Extend LV when needed
lvextend -l +100%FREE /dev/vg_storage/lv_data
xfs_growfs /mnt/data
```

---

## References
- `man lvm`
- `man pvcreate`, `vgcreate`, `lvcreate`
- Red Hat Documentation: [LVM Administration Guide](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/9/html-single/managing_storage_devices/index)
