---
title: Enable SSH 
parent: Windows
nav_order: 40
layout: default
---

# Table of Contents 

{: .no_toc }

1. TOC 
{:toc}

# Enable SSH Access from Linux to Windows (Port 22)

This guide explains how to enable a **Windows machine** to accept **SSH connections on port 22** from a **Linux machine** using **OpenSSH Server**.

---

## 1. Install OpenSSH Server on Windows

### Option A: Windows 10 / 11 (GUI)
1. Open **Settings**
2. Navigate to **Apps → Optional Features**
3. Click **Add a feature**
4. Install:
   - **OpenSSH Server**
   - (Optional) OpenSSH Client

---

### Option B: PowerShell (Run as Administrator)

```powershell
Get-WindowsCapability -Online | Where-Object Name -like 'OpenSSH.Server*'

Add-WindowsCapability -Online -Name OpenSSH.Server~~~~0.0.1.0
```

---

## 2. Start and Enable the SSH Service

```powershell
Start-Service sshd
Set-Service -Name sshd -StartupType Automatic
```

Verify:
```powershell
Get-Service sshd
```

---

## 3. Allow Port 22 Through Windows Firewall

Windows usually adds this rule automatically, but verify it exists.

```powershell
Get-NetFirewallRule -Name *ssh*
```

If missing, create it:

```powershell
New-NetFirewallRule `
  -Name "OpenSSH-Server-In-TCP" `
  -DisplayName "OpenSSH Server (SSH)" `
  -Enabled True `
  -Direction Inbound `
  -Protocol TCP `
  -Action Allow `
  -LocalPort 22
```

---

## 4. Verify SSH Is Listening on Port 22

```powershell
netstat -ano | findstr :22
```

Expected output:
```
TCP    0.0.0.0:22     LISTENING
```

---

## 5. Connect from Linux

From the Linux machine:

```bash
ssh windows_username@windows_ip
```

Example:
```bash
ssh ryan@192.168.1.50
```

Explicit port (optional):
```bash
ssh -p 22 ryan@192.168.1.50
```

---

## 6. Authentication Details

- SSH uses **Windows user credentials**
- Valid username formats:
  - `username`
  - `COMPUTERNAME\username`

If one fails, try the other.

---

## 7. (Optional) Enable SSH Key Authentication

### Generate a Key on Linux
```bash
ssh-keygen
```

### Copy the Key to Windows
```bash
ssh-copy-id ryan@windows_ip
```

Or manually place the public key in:
```
C:\Users\ryan\.ssh\authorized_keys
```

### Fix Permissions on Windows (Required)
```powershell
icacls $env:USERPROFILE\.ssh /inheritance:r
icacls $env:USERPROFILE\.ssh /grant "$env:USERNAME:(F)"
```

Restart SSH:
```powershell
Restart-Service sshd
```

---

## 8. Common Problems and Fixes

### Connection Refused
- `sshd` service not running
- Firewall blocking port 22

### Permission Denied
- Incorrect username format
- Password authentication disabled

Check configuration:
```
C:\ProgramData\ssh\sshd_config
```

Ensure:
```
PasswordAuthentication yes
PubkeyAuthentication yes
```

Restart service after changes:
```powershell
Restart-Service sshd
```

---

## 9. Debug from Linux

```bash
ssh -vvv ryan@windows_ip
```

This provides verbose output showing authentication and connection failures.

---

## Notes

- Port forwarding is required for WAN access
- Consider key-only authentication for security
- You can change the SSH port in `sshd_config` if needed

---
