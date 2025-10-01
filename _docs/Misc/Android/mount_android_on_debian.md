---

layout: default
title: "Mounting Android device on ubuntu"

---

# Table of Contents 
{: .no_toc }

1. TOC 
{:toc}

---
# 📱 Mounting Android Filesystem on Debian

This guide covers multiple methods for accessing an Android device's filesystem from a Debian-based Linux system.

---

## ✅ Method 1: Using `jmtpfs` (MTP File Access)

Most Android phones use **MTP (Media Transfer Protocol)** when connected via USB.

### 📦 Install Required Packages:
```bash
sudo apt update
sudo apt install jmtpfs mtp-tools gvfs-backends gvfs-fuse
```

### 🔌 Connect & Authorize:
1. Plug in your Android phone via USB.
2. On the phone, select **File Transfer (MTP)** mode.
3. Run:
   ```bash
   mtp-detect
   ```
   If the phone is detected, continue.

### 📂 Mount the Phone:
```bash
mkdir ~/android
jmtpfs ~/android
```

### 🧹 To Unmount:
```bash
fusermount -u ~/android
```

---

## ✅ Method 2: Using `simple-mtpfs` (Alternative FUSE-Based)

Sometimes works better than `jmtpfs`.

### 📦 Install:
```bash
sudo apt install simple-mtpfs
```

### 📂 Mount:
```bash
mkdir ~/android
simple-mtpfs ~/android
```

### 🧹 Unmount:
```bash
fusermount -u ~/android
```

---

## ✅ Method 3: Using `adb` (Android Debug Bridge)

Gives shell-level access to the device (like root access), useful for advanced users and scripting.

### 📦 Install ADB:
```bash
sudo apt install android-tools-adb
```

### 🔧 Enable on Android:
1. Enable **Developer Options** on your Android phone.
2. Enable **USB debugging**.

### 🔌 Connect:
```bash
adb devices
adb shell
```

### 📁 Pull/Push Files:
```bash
adb pull /sdcard/DCIM/Camera/ ~/Pictures/
adb push myfile.txt /sdcard/
```

---

## ✅ GUI Option (Nautilus / KDE)

If using GNOME or KDE, the file manager may auto-mount the phone under “Devices” when connected via USB in File Transfer mode.

---

## 🛠️ Troubleshooting

- Use `mtp-detect` or `adb devices` to confirm the phone is detected.
- Check USB mode is set to **File Transfer** or **USB Debugging**.
- Use `dmesg | tail` to see kernel logs.
- Try a different USB cable or port if detection fails.
- If `jmtpfs` hangs or fails, try `simple-mtpfs`.

---

Let me know if you need info on wireless ADB access or rooted device mounting options.
