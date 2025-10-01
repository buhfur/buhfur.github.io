---
title: Firewalld
parent: Utils
grand_parent: Linux
nav_order: 60
layout: default
---
# Table of Contents 
{: .no_toc }

1. TOC 
{:toc}

---

# Firewalld on RHEL/RHC**SA** 9 — Deep-Dive Notes & Snippets

> Quick, exam-ready reference for administering firewalld (nftables backend) on RHEL 9.


---

## Basics & Concepts

- **firewalld** is a **dynamic** firewall manager. It exposes high-level objects: **zones**, **services**, **ports**, **rich rules**.  
- **Backend** on RHEL 9: **nftables** (not iptables).  
- **Two configuration layers**:
  - **Runtime**: in-memory, immediate, **lost on reload/reboot** unless also made permanent.
  - **Permanent**: stored config, **requires reload** to take effect.

---

## Service Management

```bash
# Install (usually preinstalled on RHEL 9)
sudo dnf install firewalld

# Start & enable at boot
sudo systemctl enable --now firewalld

# Status & logs
sudo systemctl status firewalld
sudo journalctl -u firewalld -b
```

---

## Runtime vs Permanent

```bash
# Add a port runtime-only (lost on reload/reboot)
sudo firewall-cmd --add-port=8080/tcp

# Make it permanent (persists) and apply
sudo firewall-cmd --permanent --add-port=8080/tcp
sudo firewall-cmd --reload

# Verify runtime vs permanent views
sudo firewall-cmd --list-all
sudo firewall-cmd --list-all --permanent
```

**Rule of thumb (exam)**: Do it permanent **and** `--reload`, or do it runtime and remember it won’t survive a reload.

---

## Zones

- **Purpose**: Group rules by **trust level**; each NIC or source address maps to **one** zone.
- Common zones: `public`, `home`, `work`, `internal`, `dmz`, `trusted`, `drop`, `block`.

```bash
# List zones
sudo firewall-cmd --get-zones
sudo firewall-cmd --get-active-zones

# View details of a zone (runtime)
sudo firewall-cmd --zone=public --list-all

# Change default zone
sudo firewall-cmd --set-default-zone=public
sudo firewall-cmd --get-default-zone
```

---

## Binding Interfaces, Sources, and Default Zone

```bash
# Bind an interface to a zone (runtime)
sudo firewall-cmd --zone=internal --change-interface=eth1

# Make it permanent
sudo firewall-cmd --permanent --zone=internal --add-interface=eth1
sudo firewall-cmd --reload

# Bind a source network (CIDR) to a zone
sudo firewall-cmd --permanent --zone=dmz --add-source=192.0.2.0/24
sudo firewall-cmd --reload

# Show which zone a given interface is in
sudo firewall-cmd --get-zone-of-interface=eth0
```

---

## Services & Ports

- **Services** are XML definitions (name + port/protocol tuples, helpers) under `/usr/lib/firewalld/services/` and `/etc/firewalld/services/`.

```bash
# List services known to firewalld
sudo firewall-cmd --get-services

# Allow a predefined service in a zone (runtime)
sudo firewall-cmd --zone=public --add-service=http

# Permanent + apply
sudo firewall-cmd --permanent --zone=public --add-service=http
sudo firewall-cmd --reload

# Open an arbitrary port
sudo firewall-cmd --zone=public --add-port=8443/tcp
sudo firewall-cmd --permanent --zone=public --add-port=8443/tcp
sudo firewall-cmd --reload

# Remove
sudo firewall-cmd --zone=public --remove-service=http
sudo firewall-cmd --permanent --zone=public --remove-port=8443/tcp
sudo firewall-cmd --reload
```

**Custom service** (XML) example (permanent):
```bash
sudo tee /etc/firewalld/services/myapp.xml >/dev/null <<'XML'
<?xml version="1.0" encoding="utf-8"?>
<service>
  <short>myapp</short>
  <description>My custom app</description>
  <port protocol="tcp" port="9000"/>
</service>
XML

sudo firewall-cmd --reload
sudo firewall-cmd --zone=public --add-service=myapp
```

---

## Masquerading & Forwarding/NAT

```bash
# Enable outbound NAT (masquerade) in a zone (typical for gateways)
sudo firewall-cmd --zone=internal --add-masquerade
sudo firewall-cmd --permanent --zone=internal --add-masquerade
sudo firewall-cmd --reload

# Port forwarding (DNAT) within a zone
sudo firewall-cmd --zone=public --add-forward-port=port=80:proto=tcp:toaddr=192.0.2.10:toport=8080
sudo firewall-cmd --permanent --zone=public --add-forward-port=port=53:proto=udp:toport=5300
sudo firewall-cmd --reload

# IP forwarding (kernel)
echo net.ipv4.ip_forward=1 | sudo tee /etc/sysctl.d/99-ipforward.conf
sudo sysctl --system
```

> **Exam tip**: For NAT/gateway tasks you usually need **both** masquerade and kernel ip_forward.

---

## Rich Rules

- Fine-grained policies beyond simple service/port allows.

```bash
# Log then drop SSH from a host
sudo firewall-cmd --permanent   --add-rich-rule='rule family="ipv4" source address="203.0.113.5" service name="ssh" log prefix="FW:" level="info" drop'
sudo firewall-cmd --reload

# Allow HTTP only from a subnet
sudo firewall-cmd --permanent   --add-rich-rule='rule family="ipv4" source address="192.0.2.0/24" service name="http" accept'
sudo firewall-cmd --reload

# Timeouts (runtime only)
sudo firewall-cmd --add-rich-rule='rule service name="ssh" accept' --timeout=600
```

---

## IPSets

- Maintain large lists of IPs and reference them in rich rules.

```bash
# Create ipset and populate
sudo firewall-cmd --permanent --new-ipset=badhosts --type=hash:ip
sudo firewall-cmd --permanent --ipset=badhosts --add-entry=203.0.113.5
sudo firewall-cmd --permanent --ipset=badhosts --add-entry=198.51.100.7
sudo firewall-cmd --reload

# Use ipset in a rich rule
sudo firewall-cmd --permanent   --add-rich-rule='rule family="ipv4" source ipset="badhosts" drop'
sudo firewall-cmd --reload

# Inspect
sudo firewall-cmd --info-ipset=badhosts
```

---

## Direct Interface (advanced)

- Bypass abstractions and write **raw nft** rules via firewalld’s “direct” layer. Use sparingly on exams.

```bash
# List current direct rules
sudo firewall-cmd --direct --get-all-rules

# Add a raw rule (example: log TCP 2222)
sudo firewall-cmd --permanent --direct --add-rule ipv4 filter INPUT 0   nft "tcp dport 2222 log prefix "RAW:" accept"
sudo firewall-cmd --reload
```

> Prefer **rich rules** unless the task explicitly asks for direct/raw rules.

---

## Logging & Diagnostics

```bash
# Log denied packets (kernel)
sudo firewall-cmd --set-log-denied=all
sudo firewall-cmd --get-log-denied

# View kernel messages containing rejects/logs
sudo journalctl -k | grep -E 'FINAL_REJECT|FW:|FIREWALL_LOG|RAW:'

# Follow firewalld service logs
sudo journalctl -fu firewalld

# See what a zone is actually allowing right now
sudo firewall-cmd --zone=public --list-all
```

**Add a logging rich rule** (example):
```bash
sudo firewall-cmd --permanent   --add-rich-rule='rule family="ipv4" port port="8080" protocol="tcp" log prefix="FIREWALL_LOG: " level="info" drop'
sudo firewall-cmd --reload
```

## Configure firewalld logging to rsyslog file 

**Create rsyslog config file** 
```bash
sudo vim /etc/rsyslog.d/firewalld-denied.conf
```

**Add content** 
```bash
:msg, contains, "FINAL_REJECT" -/var/log/firewalld-denied.log
& stop
```

**Create logfile**
```bash
sudo touch /var/log/firewalld-denied.log
sudo chmod 600 /var/log/firewalld-denied.log
```

**Restart rsyslog**
```bash
sudo systemctl restart rsyslog
```

**Read log file**
```bash
sudo tail -f /var/log/firewalld-denied.log
```

## Prevent Console Spam Globally   

Kernel messages get printed to the console if their log level is less than or equal to the console log level , firewalld logs denied packets with KERN_WARNING or higher by default so they will output to the console.

Check current log level 
```bash
cat /proc/sys/kernel/printk
```

If file looks like this 
```
7   4   1   7
^
└─ console log level
```

Use this command to supress kernel logs on the console 
```bash
sysctl -w kernel.printk="3 4 1 3"
```

---

## Inspecting nftables Rules

```bash
# Full ruleset (useful to confirm what firewalld generated)
sudo nft list ruleset

# Grep for logging or a specific port
sudo nft list ruleset | grep -i 8080
sudo nft list ruleset | grep -i log
```

---

## Files & Persistence

- **Zones** (sys & local):
  - System: `/usr/lib/firewalld/zones/*.xml`
  - Local:  `/etc/firewalld/zones/*.xml`
- **Services**:
  - System: `/usr/lib/firewalld/services/*.xml`
  - Local:  `/etc/firewalld/services/*.xml`
- **Main config**: `/etc/firewalld/firewalld.conf`
- **IPSets**: `/etc/firewalld/ipsets/*.xml`

> Local definitions in `/etc/firewalld/` override system ones.  
> After editing files directly, **reload**: `sudo firewall-cmd --reload`.

---

## Troubleshooting Flow

1. **Scope the problem**
   - Is it this host’s firewall? Network path? Service not bound?
2. **Check runtime state**
   ```bash
   sudo firewall-cmd --state
   sudo firewall-cmd --get-active-zones
   sudo firewall-cmd --list-all       # (default zone)
   sudo firewall-cmd --zone=public --list-all
   ```
3. **Confirm interface/zone mapping**
   ```bash
   ip -br a
   sudo firewall-cmd --get-zone-of-interface=eth0
   ```
4. **Check service/port present in the correct zone**
   ```bash
   sudo firewall-cmd --zone=public --list-services
   sudo firewall-cmd --zone=public --list-ports
   ```
5. **Look for drops**
   ```bash
   sudo firewall-cmd --set-log-denied=all
   sudo journalctl -k | grep FINAL_REJECT
   ```
6. **Inspect nftables**
   ```bash
   sudo nft list ruleset | less
   ```
7. **Reload and retest**
   ```bash
   sudo firewall-cmd --reload
   sudo ss -tulpn | grep -E ':(80|443|22)'
   ```

---

## Common Exam Tasks (Cheat Sheet)

```bash
# Allow HTTP/HTTPS in public zone permanently
sudo firewall-cmd --permanent --zone=public --add-service=http
sudo firewall-cmd --permanent --zone=public --add-service=https
sudo firewall-cmd --reload

# Open custom TCP port 9090 permanently
sudo firewall-cmd --permanent --zone=public --add-port=9090/tcp
sudo firewall-cmd --reload

# Bind NIC to a zone permanently
sudo firewall-cmd --permanent --zone=internal --add-interface=eth1
sudo firewall-cmd --reload

# Allow SSH from a specific subnet only
sudo firewall-cmd --permanent   --add-rich-rule='rule family="ipv4" source address="10.10.10.0/24" service name="ssh" accept'
sudo firewall-cmd --permanent   --add-rich-rule='rule family="ipv4" service name="ssh" drop'
sudo firewall-cmd --reload

# Enable masquerade on internal zone for NAT
sudo firewall-cmd --permanent --zone=internal --add-masquerade
sudo firewall-cmd --reload

# Forward port 80 -> 192.0.2.10:8080
sudo firewall-cmd --permanent --zone=public   --add-forward-port=port=80:proto=tcp:toaddr=192.0.2.10:toport=8080
sudo firewall-cmd --reload

# Temporary access with timeout (runtime)
sudo firewall-cmd --zone=public --add-service=mysql --timeout=600

# Show differences runtime vs permanent
sudo firewall-cmd --list-all
sudo firewall-cmd --list-all --permanent
```
