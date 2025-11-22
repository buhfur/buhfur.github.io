---
title: Testing swap on linux 
parent: Misc
grand_parent: Linux
nav_order: 90
layout: default
---

# System level testing 

## 1. Confirm Swap is enabled but idle 

```bash
free -h
swapon --show
cat /proc/swaps
```

## 2. Configure the kernel to prefer swap more 

```bash
# Save current value so you can put it back later
cat /proc/sys/vm/swappiness

# Temporarily crank it up
sudo sysctl vm.swappiness=100
```

**Restore later with:**

```bash
sudo sysctl vm.swappiness=<original_value>
```


# Stress-ng testing 

```bash
# Allocate a lot of anonymous memory for a short time
sudo stress-ng --vm 1 --vm-bytes 95% --timeout 60s
```
