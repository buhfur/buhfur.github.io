---

layout: default
title: "NMAP Snippets"
nav_order: 14

---
# Useful `nmap` Snippets

---

### **Basic Scans**
1. **Perform a simple scan:**
   ```bash
   nmap 192.168.1.1
   ```
2. **Scan multiple targets:**
   ```bash
   nmap 192.168.1.1 192.168.1.2
   ```
3. **Scan a range of IPs:**
   ```bash
   nmap 192.168.1.1-254
   ```
4. **Scan an entire subnet:**
   ```bash
   nmap 192.168.1.0/24
   ```

---

### **Port Scanning**
5. **Scan specific ports:**
   ```bash
   nmap -p 80,443 192.168.1.1
   ```
6. **Scan a range of ports:**
   ```bash
   nmap -p 1-1000 192.168.1.1
   ```
7. **Scan all 65535 ports:**
   ```bash
   nmap -p- 192.168.1.1
   ```

---

### **Service and Version Detection**
8. **Detect services and versions:**
   ```bash
   nmap -sV 192.168.1.1
   ```
9. **Aggressive service detection:**
   ```bash
   nmap -sV --version-intensity 5 192.168.1.1
   ```

---

### **Operating System Detection**
10. **Detect OS of a host:**
    ```bash
    nmap -O 192.168.1.1
    ```
11. **Combine OS detection with version detection:**
    ```bash
    nmap -A 192.168.1.1
    ```

---

### **Stealth Scanning**
12. **Perform a TCP SYN scan:**
    ```bash
    nmap -sS 192.168.1.1
    ```
13. **Perform a TCP connect scan:**
    ```bash
    nmap -sT 192.168.1.1
    ```

---

### **UDP Scanning**
14. **Scan UDP ports:**
    ```bash
    nmap -sU 192.168.1.1
    ```

---

### **Script Scanning**
15. **Run default scripts:**
    ```bash
    nmap -sC 192.168.1.1
    ```
16. **Run specific scripts:**
    ```bash
    nmap --script=http-title 192.168.1.1
    ```
17. **Run multiple scripts:**
    ```bash
    nmap --script=http-title,ssl-cert 192.168.1.1
    ```

---

### **Output Control**
18. **Save output to a file:**
    ```bash
    nmap -oN output.txt 192.168.1.1
    ```
19. **Save output in all formats:**
    ```bash
    nmap -oA output 192.168.1.1
    ```
20. **Generate machine-readable output:**
    ```bash
    nmap -oX output.xml 192.168.1.1
    ```

---

### **Firewall Evasion**
21. **Use decoys to mask scan origin:**
    ```bash
    nmap -D RND:10 192.168.1.1
    ```
22. **Use a specific source port:**
    ```bash
    nmap --source-port 80 192.168.1.1
    ```
23. **Fragment packets to bypass firewalls:**
    ```bash
    nmap -f 192.168.1.1
    ```

---

### **Network Discovery**
24. **Ping scan to find live hosts:**
    ```bash
    nmap -sn 192.168.1.0/24
    ```
25. **Scan without pinging:**
    ```bash
    nmap -Pn 192.168.1.1
    ```

---

### **Advanced Options**
26. **Scan with maximum speed:**
    ```bash
    nmap -T5 192.168.1.1
    ```
27. **Save a list of targets:**
    ```bash
    nmap -iL targets.txt
    ```
28. **Scan IPv6 targets:**
    ```bash
    nmap -6 2001:db8::1
    ```

---

### **Vulnerability Scanning**
29. **Use vulnerability scanning scripts:**
    ```bash
    nmap --script vuln 192.168.1.1
    ```

---

