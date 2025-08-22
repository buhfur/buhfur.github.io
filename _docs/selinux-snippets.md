---

layout: default
title: "SELinux"

---

# Table of Contents  
{: .no_toc }

1. TOC 
{:toc}

---

# RHCSA 9 – SELinux Snippets Cheat Sheet

A compact, practical set of commands and patterns to meet these objectives:

- List and identify SELinux file and process context
- Restore default file contexts
- Manage SELinux port labels
- Use boolean settings to modify system SELinux settings

> All commands assume RHEL 9 or compatible. Use `sudo` as needed.

---

## 1) List and identify SELinux **file** and **process** context

### Quick status

```bash
# Current mode
getenforce     # Enforcing | Permissive | Disabled

# Detailed status (policy, modes, context root)
sestatus
```

### Identity and context of current user/session

```bash
id -Z          # SELinux user:role:type
ps -Z          # Append SELinux context to ps output
```

### Processes

```bash
# Show context for specific services/processes
ps -eZ | grep -E "(httpd|sshd)"
ps -C httpd -Z -o pid,user,cmd

# Read domain/type for a running process
cat /proc/$(pidof httpd)/attr/current
```

### Files and directories

```bash
# List with context labels
ls -lZ /var/www/html
ls -dZ /var/www

# Inspect a single file’s context
stat -Z /var/www/html/index.html

# Determine the *default* context SELinux expects (without changing it)
matchpathcon /var/www/html/index.html
matchpathcon /srv/www          # Works on dirs too
```

### Common context types you’ll see

- `*_t` ending = **type** (most important part for access decisions)
- Examples:
  - `httpd_t` (process domain for Apache)
  - `httpd_sys_content_t` (files served by Apache)
  - `ssh_port_t` (label used for SSH ports)

---

## 2) Restore default file contexts

> Use this when labels drift from expected defaults, or after defining custom file context rules.

### One-off restore to default policy labels

```bash
# Recursively restore labels to their defaults for a path
restorecon -Rv /var/www/html

# Preview changes only (no modifications)
restorecon -nRv /var/www/html
```

### Persistent custom file context rules

```bash
# 1) Add a rule (regex accepted); 2) apply it with restorecon
# Example: Serve content from /srv/www under Apache
semanage fcontext -a -t httpd_sys_content_t \
  "/srv/www(/.*)?"
restorecon -Rv /srv/www

# View / modify / delete rules
semanage fcontext -l | grep /srv/www
semanage fcontext -m -t httpd_sys_content_t "/srv/www(/.*)?"
semanage fcontext -d "/srv/www(/.*)?"
```

### Avoid `chcon` for permanence

```bash
# Temporary label change (NOT persistent across restorecon or relabel)
chcon -t httpd_sys_content_t /path/to/file
# Prefer semanage fcontext + restorecon for durability.
```

### Full system relabel (last resort)

```bash
# Create the autorelabel flag and reboot
sudo touch /.autorelabel
sudo reboot
```

---

## 3) Manage SELinux **port labels**

> Map TCP/UDP ports to SELinux port types so the right domains can bind/listen.

### List and search current mappings

```bash
# All port mappings
semanage port -l

# Filter by type or port
semanage port -l | grep http_port_t
semanage port -l | grep 8080
```

### Add, modify, delete a mapping

```bash
# Allow Apache (httpd_t via http_port_t) to listen on TCP 8080
semanage port -a -t http_port_t -p tcp 8080

# Change existing mapping
semanage port -m -t http_port_t -p tcp 8080

# Remove mapping
semanage port -d -t http_port_t -p tcp 8080
```

### Common examples

```bash
# SSH on an alternate port (e.g., 2222)
semanage port -a -t ssh_port_t -p tcp 2222

# Named (DNS) allowing TCP 5353, for example
semanage port -a -t dns_port_t -p tcp 5353
```

---

## 4) Use **boolean** settings to modify system SELinux behavior

> Booleans toggle conditional policy. Persistent changes survive reboots with `-P`.

### Discover booleans

```bash
# List all booleans
semanage boolean -l

# Show with active/on/off values
getsebool -a

# Narrow down by service
getsebool -a | grep httpd
semanage boolean -l | grep samba
```

### Set booleans

```bash
# Temporarily enable (until next reload/reboot)
setsebool httpd_can_network_connect on

# Persistently enable (recommended for system config)
setsebool -P httpd_can_network_connect on
```

### Handy examples

```bash
# Allow Apache to initiate outbound network connections (e.g., proxy, updates)
setsebool -P httpd_can_network_connect on

# Allow Apache to send mail
setsebool -P httpd_can_sendmail on

# Allow Samba to read/write any shared content labeled for Samba
setsebool -P samba_export_all_rw on

# Allow NFS home directories
setsebool -P use_nfs_home_dirs on

# Permit FTP to read user home dirs
setsebool -P ftp_home_dir on
```

---

## Troubleshooting AVC denials quickly

> Tip: you should also enable the auditd systemd unit 

```bash
# View denials live
ausearch -m AVC,USER_AVC -ts recent

# Journal (setroubleshootd)
journalctl -t setroubleshoot --since "-1h"

# Generate human-readable suggestions
sealert -a /var/log/audit/audit.log
```

> Tip: If a denial suggests a boolean, prefer toggling the boolean. If it suggests a file label, use `semanage fcontext` + `restorecon`. Avoid `chcon` for long-term fixes.


---

## Mini-labs (RHCSA-style practice)

### A) Serve custom content from `/srv/www` on port 8080

```bash
# 1) File context for custom docroot
semanage fcontext -a -t httpd_sys_content_t "/srv/www(/.*)?"
restorecon -Rv /srv/www

# 2) Permit httpd to bind 8080
semanage port -a -t http_port_t -p tcp 8080
systemctl restart httpd

# 3) Verify
ps -C httpd -Z -o pid,context,cmd
ss -lntp | grep :8080
ls -lZ /srv/www
```

### B) Enable Apache outbound connections (e.g., reverse proxy)

```bash
setsebool -P httpd_can_network_connect on
# Test by curling through Apache or checking AVCs if blocked
```

### C) Fix mislabeled web content

```bash
# Find mismatches between actual and default
find /var/www -context "*" -print0 | xargs -0 matchpathcon -n 2>/dev/null | grep -v ' matches '

# Restore defaults
restorecon -Rv /var/www
```

---

## Reference quick-glossary

- **Context**: `user:role:type:level` (type is most important)
- **Type enforcement**: Policy allowing a *domain* (process type) to access *types* (file/port).
- **Boolean**: On/off switch enabling optional policy branches.
- **Port label**: Mapping of (proto,port) → SELinux type used by domains to bind/listen.
- **restorecon**: Restore files to default labels.
- **semanage fcontext**: Define persistent file context rules.
- **semanage port**: Define port type mappings.
- **setsebool/-P**: Toggle booleans (persistent with `-P`).

