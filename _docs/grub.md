---

layout: default
title: "GRUB"

---


# Table of Contents 
{: .no_toc }

1. TOC
{:toc}

---


# GRUB Configuration Guide

The **GRUB (GRand Unified Bootloader)** is the default bootloader for most Linux distributions. It loads the kernel, passes arguments, and can manage multi-boot environments with Linux, Windows, and other OSes.

---

## File Locations

- **Main config file (auto-generated):**
  - `/boot/grub2/grub.cfg` (BIOS systems, RHEL/Fedora)
  - `/boot/efi/EFI/<distro>/grub.cfg` (UEFI systems)

- **Templates (used to generate grub.cfg):**
  - `/etc/default/grub`
  - `/etc/grub.d/*`

- **Command to regenerate config:**
  ```bash
  grub-mkconfig -o /boot/grub2/grub.cfg     # BIOS
  grub-mkconfig -o /boot/efi/EFI/<distro>/grub.cfg   # UEFI
  ```

---

## `/etc/default/grub` Options

This file controls **global GRUB behavior**. Example:

```ini
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(lsb_release -i -s 2> /dev/null || echo Debian)"
GRUB_DEFAULT=0
GRUB_DISABLE_SUBMENU=y
GRUB_TERMINAL=console
GRUB_CMDLINE_LINUX="quiet splash"
GRUB_GFXMODE=1920x1080
GRUB_GFXPAYLOAD_LINUX=keep
```

### Common Parameters:
- `GRUB_TIMEOUT` → Seconds before default boot.
- `GRUB_DEFAULT` → Default menu entry (0 = first).
- `GRUB_SAVEDEFAULT=true` → Save last booted entry.
- `GRUB_CMDLINE_LINUX` → Kernel options (e.g., `quiet`, `nomodeset`).
- `GRUB_GFXMODE` → Resolution of GRUB menu.

---

## `/etc/grub.d/*` Scripts

Scripts in this directory generate GRUB menu entries. Key files:

- `00_header` → Sets up environment.
- `10_linux` → Detects Linux kernels.
- `20_linux_xen` → For Xen kernels.
- `30_os-prober` → Detects other OSes (Windows, BSD).
- `40_custom` → Place for manual entries.

---

## Adding a Custom Entry

Edit `/etc/grub.d/40_custom`:

```bash
menuentry "Windows 11 (UEFI)" {
    insmod part_gpt
    insmod fat
    set root='(hd0,gpt2)'   # EFI partition
    chainloader /EFI/Microsoft/Boot/bootmgfw.efi
}
```

Then regenerate config:

```bash
grub-mkconfig -o /boot/efi/EFI/<distro>/grub.cfg
```

---

## Secure Boot & UEFI Notes

- **UEFI Systems** store GRUB in the EFI System Partition (ESP).
- Secure Boot requires signed GRUB and kernel images.
- If dual-booting with Windows:
  - Disable **Fast Boot** in Windows.
  - Ensure GRUB chainloads `bootmgfw.efi`.
- `mokutil` is used to manage custom keys if signing kernels.

---

## Troubleshooting

### GRUB Rescue Mode
- Triggered if GRUB can’t find its modules/config.
- Example recovery commands:
  ```grub
  ls                      # list drives/partitions
  set root=(hd0,gpt2)     # set correct root partition
  set prefix=(hd0,gpt2)/boot/grub
  insmod normal
  normal
  ```
  Then boot into Linux and reinstall GRUB:
  ```bash
  grub-install /dev/sda
  grub-mkconfig -o /boot/grub2/grub.cfg
  ```

### Common Issues:
- **Black screen after boot** → Add `nomodeset` to `GRUB_CMDLINE_LINUX`.
- **Windows not detected** → Ensure `os-prober` is installed and `GRUB_DISABLE_OS_PROBER=false` in `/etc/default/grub`.
- **Wrong resolution** → Check `GRUB_GFXMODE`.

---

## Useful Commands

- **Regenerate config:**
  ```bash
  grub-mkconfig -o /boot/grub2/grub.cfg
  ```
- **Install GRUB (BIOS):**
  ```bash
  grub-install /dev/sda
  ```
- **Install GRUB (UEFI):**
  ```bash
  grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=GRUB
  ```

---

## References
- GNU GRUB Manual: https://www.gnu.org/software/grub/manual/grub
- Arch Wiki: https://wiki.archlinux.org/title/GRUB

---

# Adding Windows to GRUB on UEFI/GPT Systems

This guide explains how to add Windows entries to GRUB when both Linux and Windows are installed on GPT partitions with UEFI. It also covers Secure Boot caveats.

---

## 1. Verify Windows Bootloader Entry Exists in EFI Partition

In UEFI systems, Windows installs its bootloader (`bootmgfw.efi`) into the **EFI System Partition (ESP)**:

```
/boot/efi/EFI/Microsoft/Boot/bootmgfw.efi
```

Check if it exists:

```bash
ls /boot/efi/EFI/Microsoft/Boot/bootmgfw.efi
```

If it does not exist, Windows boot repair may be needed.

---

## 2. Install/Enable `os-prober`

Most Linux distributions rely on `os-prober` to detect Windows automatically.

```bash
sudo apt install os-prober   # Debian/Ubuntu
sudo dnf install os-prober   # Fedora/RHEL/AlmaLinux
```

Enable it if disabled (common in newer distros). Edit `/etc/default/grub` and set:

```ini
GRUB_DISABLE_OS_PROBER=false
```

Then regenerate GRUB:

```bash
sudo grub-mkconfig -o /boot/grub/grub.cfg   # Debian/Ubuntu
sudo grub2-mkconfig -o /boot/efi/EFI/<distro>/grub.cfg   # Fedora/RHEL
```

---

## 3. Manual Entry (if os-prober fails)

Add a custom entry to `/etc/grub.d/40_custom`:

```bash
menuentry "Windows Boot Manager (UEFI)" {
    insmod part_gpt
    insmod fat
    insmod chain
    search --no-floppy --fs-uuid --set=root <UUID-of-ESP>
    chainloader /EFI/Microsoft/Boot/bootmgfw.efi
}
```

Find the EFI partition UUID:

```bash
blkid | grep EFI
```

Then regenerate GRUB again:

```bash
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

---

## 4. Secure Boot Caveats

- **Windows boot manager** (`bootmgfw.efi`) is signed by Microsoft, so it will boot fine with Secure Boot enabled.
- **Linux bootloader**: your GRUB and kernel must be signed. Most distros provide a signed GRUB and kernel via **shimx64.efi**.
- If you build your own GRUB or kernel, they may **not boot under Secure Boot** unless signed with your own keys and enrolled in UEFI.
- Some motherboards require GRUB to chainload Windows through `shim → grubx64.efi → bootmgfw.efi`. Otherwise, you may see a **“Security Violation”**.

---

## 5. Common Nuances

- If you installed Windows **after** Linux, Windows may overwrite the boot order. Fix it with:

```bash
sudo efibootmgr -v
sudo efibootmgr -o <linux-entry>,<windows-entry>
```

- If GRUB does not detect Windows even with os-prober, ensure the EFI partition is mounted at `/boot/efi` when running `grub-mkconfig`.

---

## ✅ Summary

- Use `os-prober` + `grub-mkconfig` for automatic detection.
- If that fails, add a manual chainloader entry pointing to `bootmgfw.efi`.
- With **Secure Boot**, Windows boots fine, but Linux must use signed GRUB/kernel (via shim).
