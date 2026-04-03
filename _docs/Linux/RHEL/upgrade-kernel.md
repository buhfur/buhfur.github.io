---
title: Upgrading kernel version 
parent: RHEL
grand_parent: Linux
nav_order: 220
layout: default
---
# Table of Contents  
{: .no_toc }

1. TOC 
{:toc}

---

# AlmaLinux 

## Install newer kernel version 

### 1. Enable ELRepo 

```bash
rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org
dnf install https://www.elrepo.org/elrepo-release-9.el9.elrepo.noarch.rpm
```

### 2. Install latest mainline stable kernel  

```bash
dnf --enablerepo=elrepo-kernel install kernel-ml 
```

### 3. Verify Installed Kernel Version  

```bash
grubby --default-kernel
```

If kernel version shows newer kernel , reboot 

After reboot, verify newer kernel was installed 

```bash
uname -sr  
```

## Update kernel headers & dev tools to newer kernel version  

If not done already , install following packages 

```bash
dnf groupinstall "Development Tools"
# or install packages separately 
dnf install kernel-headers kernel-devel
```
### 1. Check kernel-devel & kernel-headers package matches kernel version 
```bash
rpm -qa | grep -E "kernel-devel|kernel-headers"
```

If version doesn't match current kernel version , proceed to step 2 

### 2. Update kernel headers and development tools 
```bash
# Install development tools 
dnf groupinstall 
```
