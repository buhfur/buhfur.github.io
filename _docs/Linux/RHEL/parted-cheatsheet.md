---
title: Parted Cheatsheet
parent: RHEL
grand_parent: Linux
nav_order: 210
layout: default
---

# Table of Contents 
{: .no_toc }

1. TOC 
{:toc}


---

# Using `parted` for Partitioning and LVM

This page documents **scriptable, exam-safe usage of `parted`** for:

- Standard disk partitioning
- LVM‑ready partition creation
- Safe automation patterns

Targeted for **RHEL / Alma / Rocky / Debian** and **RHCSA 9 / 10** preparation.

> `parted` only creates partitions.  
> Filesystems and LVM are created **after** partitioning.

---

## Conceptual Flows

### Standard Partition

```
parted → partition → mkfs → mount
```

### LVM Partition

```
parted → LVM-marked partition → pvcreate → vgcreate → lvcreate → mkfs
```

---

## 1. Basic Non‑Interactive Partitioning (Recommended)

```bash
#!/bin/bash
set -e

DISK="/dev/sdb"

parted -s "$DISK" \
  mklabel gpt \
  mkpart primary xfs 1MiB 100%

partprobe "$DISK"
```

### Notes
- `-s` → script mode (non‑interactive)
- `1MiB` start → avoids alignment issues
- `100%` → uses entire disk

---

## 2. Multiple Standard Partitions

```bash
DISK=/dev/sdb

parted -s "$DISK" mklabel gpt
parted -s "$DISK" mkpart primary xfs 1MiB 2GiB
parted -s "$DISK" mkpart primary ext4 2GiB 6GiB
parted -s "$DISK" mkpart primary linux-swap 6GiB 100%

partprobe "$DISK"
```

```bash
mkfs.xfs  /dev/sdb1
mkfs.ext4 /dev/sdb2
mkswap    /dev/sdb3
```

---

## 3. LVM Partition (GPT – Recommended)

```bash
DISK=/dev/sdb

parted -s "$DISK" \
  mklabel gpt \
  mkpart primary 1MiB 100% \
  set 1 lvm on

partprobe "$DISK"
```

---

## 4. What the LVM Flag Does

| Disk Label | Effect |
|-----------|-------|
| GPT | Sets partition type to **Linux LVM** |
| MBR | Sets partition type to **0x8e** |

- Helps tooling and admins identify intent
- **Not strictly required**, but **strongly recommended**
- RHCSA graders expect it

---

## 5. Full Workflow: Partition → LVM → Filesystem

```bash
DISK=/dev/sdb

# Partition
parted -s "$DISK" mklabel gpt
parted -s "$DISK" mkpart primary 1MiB 100%
parted -s "$DISK" set 1 lvm on
partprobe "$DISK"

# LVM
pvcreate /dev/sdb1
vgcreate vg_data /dev/sdb1
lvcreate -n lv_data -l 100%FREE vg_data

# Filesystem
mkfs.xfs /dev/vg_data/lv_data
```

---

## 6. Multiple LVM Partitions on One Disk

```bash
parted -s /dev/sdb mklabel gpt
parted -s /dev/sdb mkpart primary 1MiB 10GiB
parted -s /dev/sdb mkpart primary 10GiB 100%
parted -s /dev/sdb set 1 lvm on
parted -s /dev/sdb set 2 lvm on
```

```bash
pvcreate /dev/sdb1 /dev/sdb2
vgcreate vg_pool /dev/sdb1 /dev/sdb2
```

---

## 7. MBR (msdos) Example

```bash
parted -s /dev/sdb mklabel msdos
parted -s /dev/sdb mkpart primary 1MiB 100%
parted -s /dev/sdb set 1 lvm on
```

This sets partition type **8e**.

---

## 8. Safe Automation Pattern (Abort if Disk Is Used)

```bash
DISK=/dev/sdb

if lsblk "$DISK" | grep -q part; then
  echo "Disk already partitioned — aborting"
  exit 1
fi

parted -s "$DISK" mklabel gpt
parted -s "$DISK" mkpart primary 1MiB 100%
parted -s "$DISK" set 1 lvm on
partprobe "$DISK"
```

---

## 9. Detect Disk Size Dynamically

```bash
SIZE=$(lsblk -b -dn -o SIZE /dev/sdb)

if (( SIZE < 20 * 1024 * 1024 * 1024 )); then
  echo "Disk too small"
  exit 1
fi
```

---

## 10. One‑Liner (Exam Speed)

```bash
parted -s /dev/sdb mklabel gpt mkpart primary 1MiB 100% set 1 lvm on && partprobe /dev/sdb
```

---

## 11. Verification Commands

```bash
lsblk
blkid
pvs
vgs
lvs
```

---

## 12. Common Mistakes (RHCSA Killers)

❌ Forgetting `partprobe`

❌ Forgetting to set the `lvm` flag

❌ Running `pvcreate` on the disk instead of the partition

❌ Creating a filesystem before LVM

❌ Using interactive `fdisk` in scripts

---

## Summary

- `parted` is scriptable and exam‑safe
- Always align with `1MiB`
- Use GPT unless legacy requires MBR
- Mark LVM partitions explicitly
- Follow partition → LVM → filesystem order

---

*Suitable for RHCSA 9 / 10 exam prep and real‑world automation.*

