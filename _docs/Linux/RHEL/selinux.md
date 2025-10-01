---
title: SELinux
parent: RHEL
grand_parent: Linux
nav_order: 70
layout: default
---
# Table of Contents  
{: .no_toc }

1. TOC 
{:toc}

---

# SELinux on RHEL / RHCSA 9 — Deep-Dive Notes & Snippets

> A comprehensive, exam-ready reference covering **SELinux** concepts, commands, troubleshooting, and logging for **RHEL 9** and **RHCSA 9**.

> Tip: Download  selinux-policy-doc package, this will allow you to run `man -k _selinux` and view specific man pages for your service in order to know which type to use for a specific directory. 


## **Basics & Concepts**

- **SELinux** = **Security-Enhanced Linux**  
- Implements **Mandatory Access Control (MAC)** — stricter than DAC.
- Uses **contexts** to define permissions for:
  - **Files**
  - **Processes**
  - **Ports**
  - **Users**

A context has **4 fields**:

```
user:role:type:level
```
- **user** → SELinux user (e.g. `system_u`, `unconfined_u`)
- **role** → defines allowed actions (e.g. `object_r`, `system_r`)
- **type** → most important, used for type enforcement (e.g. `httpd_sys_content_t`)
- **level** → optional MLS/MCS (e.g. `s0`)

---

## **SELinux Modes**

| **Mode**       | **Description**                                    | **Command to Set**         |
|---------------|----------------------------------------------------|-----------------------------|
| **Enforcing** | SELinux **blocks** and logs denials                | `sudo setenforce 1`        |
| **Permissive**| SELinux **allows** but logs violations             | `sudo setenforce 0`        |
| **Disabled**  | SELinux completely off (**requires reboot**)       | Edit `/etc/selinux/config` |

Check mode:
```bash
getenforce
# or
sestatus
```

---

## **Checking SELinux Status**

```bash
# Check current status
sestatus

# Check loaded policy
sestatus | grep "Loaded policy"

# Verify contexts of root filesystem
ls -Zd /
```

---

## **Managing Modes**

```bash
# Temporarily switch to permissive mode (until reboot)
sudo setenforce 0

# Switch back to enforcing
sudo setenforce 1

# Persistent change: edit config file
sudo nano /etc/selinux/config
# Set: SELINUX=enforcing | permissive | disabled

# Reload SELinux policy after editing contexts manually
sudo restorecon -Rv /
```

---

## **Contexts: Files, Processes, Ports**

### **View File Contexts**
```bash
ls -Z /var/www/html
ls -Zd /var/www/html/index.html
```

### **View Process Contexts**
```bash
ps auxZ | grep httpd
```

### **View SELinux Port Labels**
```bash
sudo semanage port -l | grep http
```

---

## **Changing Contexts**

### **Add New File Context Mapping**
```bash
sudo semanage fcontext -a -t httpd_sys_content_t "/srv/mywebsite(/.*)?"
```

### **Modify Existing Context Mapping**  
```bash
sudo semanage fcontext -m -t samba_share_t "/data/share(/.*)?"
sudo restorecon -Rv /data/share
```

### **Delete Context Mapping**
```bash
sudo semanage fcontext -d "/srv/oldsite(/.*)?"
sudo restorecon -Rv /srv/oldsite
```

### **View All Custom Context Mappings on directory**
```bash
sudo semanage fcontext -l | grep '/srv'
```

### **Save changes**
```bash
sudo restorecon -Rv /srv/mywebsite
```

> Tip: This command will also remove any non-persistent changes 

### **Change File Context For Specific Files**
```bash
sudo semanage fcontext -a -t ssh_home_t "/home/ryan/.ssh(/.*)?"
sudo restorecon -Rv /home/ryan/.ssh
```

### **Recursively Change Context**
```bash
sudo chcon -R -t samba_share_t /srv/samba
```

### **Restore Default Context**
```bash
sudo restorecon -v /var/www/html/index.html
sudo restorecon -Rv /var/www/html
```

---

## **SELinux Booleans**

Booleans allow or deny specific features without changing policy files.

```bash
# List all SELinux booleans
getsebool -a

# Search for a specific boolean
getsebool -a | grep httpd

# Change a boolean temporarily (until reboot)
sudo setsebool httpd_can_network_connect on

# Set permanently
sudo setsebool -P httpd_can_network_connect on
```

---

## **Managing Ports**

### **View Existing Labels**
```bash
sudo semanage port -l | grep http
```

### **Add a New Port Label**
```bash
sudo semanage port -a -t http_port_t -p tcp 8080
```

### **Modify Existing Port Label**
```bash
sudo semanage port -m -t http_port_t -p tcp 8080
```

### **Remove Port Label**
```bash
sudo semanage port -d -t http_port_t -p tcp 8080
```

---

## **Policy Modules**

SELinux policy is modular on RHEL.

```bash
# List all modules
sudo semodule -l

# Install a custom module
sudo semodule -i mypolicy.pp

# Remove a module
sudo semodule -r mypolicy
```

---

## **SELinux Configuration Files**

| **File**                     | **Purpose**                               |
|------------------------------|-------------------------------------------|
| `/etc/selinux/config`        | Global SELinux mode & policy settings    |
| `/etc/selinux/targeted/`     | Targeted policy configs                  |
| `/var/log/audit/audit.log`   | Logs SELinux denials                     |
| `/etc/selinux/semanage.conf` | `semanage` defaults                      |

---

## **Troubleshooting SELinux**

### **1. Check Audit Logs**
```bash
sudo ausearch -m avc -ts recent

journalctl | grep sealert
```

### **2. Generate Human-Readable Reports**

> Tip: Install setroubleshoot-server to get the sealert command 

```bash
sudo sealert -a /var/log/audit/audit.log
```

### **3. Common Fix Commands**
```bash
# Restore default contexts recursively
sudo restorecon -Rv /var/www/html

# Set correct SELinux port for Apache on 8080
sudo semanage port -a -t http_port_t -p tcp 8080

# Allow Apache to make outbound connections
sudo setsebool -P httpd_can_network_connect on
```

---

## **Viewing & Configuring Logging**

> Tip: enable auditd daemon 

By default, SELinux logs denials to:
```
/var/log/audit/audit.log
```

### **View Logs in Real-Time**
```bash
sudo tail -f /var/log/audit/audit.log
```

### **Search for AVC Denials**
```bash
sudo ausearch -m avc
```

### **Better Human-Readable Reports**
```bash
sudo yum install setroubleshoot-server
sudo sealert -a /var/log/audit/audit.log
```

### **Separate SELinux Logs to a Custom File**

1. Create a new rsyslog config:
   ```bash
   sudo nano /etc/rsyslog.d/selinux-denials.conf
   ```
2. Add:
   ```
   :msg, contains, "avc:  denied" -/var/log/selinux-denials.log
   & stop
   ```
3. Restart rsyslog:
   ```bash
   sudo systemctl restart rsyslog
   ```
4. View:
   ```bash
   sudo tail -f /var/log/selinux-denials.log
   ```

---

## **Common RHCSA Exam Tasks**

```bash
# Check SELinux mode and policy
sestatus

# Set SELinux to permissive mode temporarily
sudo setenforce 0

# Set SELinux enforcing permanently
sudo sed -i 's/^SELINUX=.*/SELINUX=enforcing/' /etc/selinux/config

# View contexts on files
ls -Z /var/www/html

# Restore contexts for a directory
sudo restorecon -Rv /var/www/html

# Allow httpd to bind to TCP 8080
sudo semanage port -a -t http_port_t -p tcp 8080

# Enable Apache outbound network connections
sudo setsebool -P httpd_can_network_connect on

# Troubleshoot SELinux denials
sudo ausearch -m avc -ts recent
sudo sealert -a /var/log/audit/audit.log
```

---

## **Summary Table**

| **Task**                          | **Command**                                 |
|----------------------------------|--------------------------------------------|
| Check SELinux status            | `sestatus`                                  |
| Temporary mode change          | `setenforce 0` / `setenforce 1`             |
| Persistent mode change         | Edit `/etc/selinux/config`                  |
| View file context              | `ls -Z file`                                |
| Restore context                | `restorecon -Rv path`                       |
| Manage SELinux ports           | `semanage port -l / -a / -m / -d`           |
| Enable feature via boolean     | `setsebool -P boolean_name on`              |
| View denials                  | `ausearch -m avc`                           |
| Human-readable audit logs      | `sealert -a /var/log/audit/audit.log`       |
