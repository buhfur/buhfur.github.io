---
title: NMAP snippets
parent: Utils
grand_parent: Linux
nav_order: 120
layout: default
---
# Table of Contents 
{: .no_toc }

1. TOC 
{:toc}

---

# Common Nmap Command Snippets

A collection of useful Nmap commands for quick reference.

---

## Basic Scans

### Scan a Single Host
```bash
nmap 192.168.1.1
```

### Scan Multiple Hosts
```bash
nmap 192.168.1.1 192.168.1.2 192.168.1.3
```

### Scan a Range of IPs
```bash
nmap 192.168.1.1-20
```

### Scan a Subnet
```bash
nmap 192.168.1.0/24
```

---

## Port Scanning

### Scan Specific Ports
```bash
nmap -p 22,80,443 192.168.1.1
```

### Scan a Range of Ports
```bash
nmap -p 1-1000 192.168.1.1
```

### Scan All 65535 Ports
```bash
nmap -p- 192.168.1.1
```

---

## Scan Types

### TCP SYN Scan (Default, Stealth)
```bash
nmap -sS 192.168.1.1
```

### TCP Connect Scan
```bash
nmap -sT 192.168.1.1
```

### UDP Scan
```bash
nmap -sU 192.168.1.1
```

### Aggressive Scan (Version Detection, OS Detection, Script Scanning, Traceroute)
```bash
nmap -A 192.168.1.1
```

### Service and Version Detection
```bash
nmap -sV 192.168.1.1
```

### OS Detection
```bash
nmap -O 192.168.1.1
```

---

## Output Options

### Save Output to File
```bash
nmap -oN output.txt 192.168.1.1
```

### Save Output in Grepable Format
```bash
nmap -oG output.gnmap 192.168.1.1
```

### Save All Formats
```bash
nmap -oA scan_results 192.168.1.1
```

---

## Useful Nmap Scripting Engine (NSE) Examples

### Run Vulnerability Scripts
```bash
nmap --script vuln 192.168.1.1
```

### Run Default Scripts
```bash
nmap -sC 192.168.1.1
```

### Detect HTTP Methods
```bash
nmap --script http-methods 192.168.1.1
```

### Detect SSL/TLS Information
```bash
nmap --script ssl-cert,ssl-enum-ciphers -p 443 192.168.1.1
```

---

## Performance Tweaks

### Increase Speed of Scan
```bash
nmap -T4 192.168.1.1
```

### Maximum Speed (May Cause Inaccuracy)
```bash
nmap -T5 192.168.1.1
```

### Limit Scan to Active Hosts Only
```bash
nmap -sn 192.168.1.0/24
```

---

## Firewall Evasion

### Decoy Scan
```bash
nmap -D RND:10 192.168.1.1
```

### Fragment Packets
```bash
nmap -f 192.168.1.1
```

### Source Port Manipulation
```bash
nmap --source-port 53 192.168.1.1
```

---

## Combining Options

### Fast, Aggressive, All Ports, Save Output
```bash
nmap -T4 -A -p- -oA full_scan 192.168.1.1
```

