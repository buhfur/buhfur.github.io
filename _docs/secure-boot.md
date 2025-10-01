---

layout: default
title: "Secure boot setup"

---

# Table of Contents  
{: .no_toc }

1. TOC 
{:toc}

---


## System info  

BIOS Version: ASRock B650 PG Riptide Wifi 5G 
Linux Version: Ubuntu 24.04.3 LTS

Steps that are specific to windows will be labeled with "(windows)" 

## BIOS Steps 

In bios settings: 

> 1. Boot -> CSM = off  
> 2. Advanced -> AMD fTPM Switch = amd cpu ftpm 
> 3. Security -> Secure Boot -> Secure Boot Mode = Custom 
> 4. Security -> Install Default Security Boot Keys 
> 5. Save changes & reboot 
> 6. Security -> Secure Boot = Enabled 


## OS Steps:

### **(Linux)Step 1. Verify you are booted into UEFI mode** 
```bash
lsblk -o NAME,PTTYPE # partitions should show "gpt" 

ls /sys/firmware/efi # if folder is present / populated , then you have verified you are in UEFI mode
```

### **Step 2. If not GPT , convert partition to MBR** 

## (windows) Convert disk to MBR 

**Run in admin command prompt**
```bash
mbr2gpt /convert /allowfullos
```

# Convert MBR → GPT in Linux (for Secure Boot)

Secure Boot requires **UEFI boot mode + GPT partitioning**. If your Linux disk uses **MBR (msdos)**, you must convert it to **GPT**.

⚠️ **Warning**: Always back up important data. Partition table conversion is risky.

---

## 1. Check if your disk is MBR
Run:
```bash
lsblk -o NAME,PTTYPE
```
- If it shows `dos` → MBR (needs conversion).  
- If it shows `gpt` → you’re already set.

---

## 2. Install `gdisk`
```bash
# Debian/Ubuntu
sudo apt install gdisk

# Fedora/RHEL/Alma
sudo dnf install gdisk
```

---

## 3. Run `gdisk` on your disk
Replace `/dev/sdX` with your actual disk (**not a partition**):
```bash
sudo gdisk /dev/sdX
```

You’ll see a message like:
```
MBR detected! This disk has an MBR partition table.
Converted to GPT. This is a potentially destructive operation.
```

---

## 4. Convert with Recovery & Transformation menu
Inside `gdisk`:
```
r   # recovery & transformation menu
g   # convert MBR -> GPT
p   # print partition table to confirm
w   # write changes and exit
```

---

## 5. Create / Verify EFI System Partition
Check existing partitions:
```bash
lsblk -f
```

- If `/boot/efi` already exists (FAT32) → good.  
- If not, create one (~100–500MB FAT32):

```bash
sudo parted /dev/sdX
(parted) mkpart primary fat32 1MiB 513MiB
(parted) set 1 esp on
```

Format it:
```bash
sudo mkfs.fat -F32 /dev/sdX1
```
Mount at `/boot/efi`.

---

## 6. Reinstall GRUB in UEFI mode
```bash
# Debian/Ubuntu
sudo grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=GRUB
sudo update-grub

# Fedora/RHEL/Alma
sudo grub2-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=GRUB
sudo grub2-mkconfig -o /boot/grub2/grub.cfg
```

---

## 7. Reboot & Enable Secure Boot
1. Reboot into BIOS  
2. Disable **CSM**  
3. Enable **Secure Boot**  
4. Install default Secure Boot keys if prompted

Your Linux should now boot in **UEFI mode with Secure Boot available**.





