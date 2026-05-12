---
title: Iso utilities 
parent: Utils
grand_parent: Linux
nav_order: 210
layout: default
---

# Table of Contents 
{: .no_toc }

1. TOC 
{:toc}

---

# Tools 

genisoimage 
```bash
sudo apt install genisoimage
```


# Create ISO image 

```bash
genisoimage -V ISO_IMAGE_NAME -o output.iso -R -J /path/to/directory 
```

# Isoinfo snippets

```bash
# List files in the ISO
isoinfo -l -i file.iso

# List files in a simpler find-like format
isoinfo -f -i file.iso

# Search for a file inside the ISO
isoinfo -f -i file.iso | grep -i 'filename'

# Extract a file from the ISO
isoinfo -i file.iso -x /PATH/TO/FILE > output_file

# Check if ISO is bootable
isoinfo -d -i file.iso | grep -i 'boot'

# List only top-level directories/files
isoinfo -f -i file.iso | awk -F/ 'NF==2'

# Find RPM packages inside an ISO
isoinfo -f -i file.iso | grep -i '\.rpm$'

# Find kickstart-related files
isoinfo -f -i file.iso | grep -Ei 'ks\.cfg|kickstart|anaconda'

# Find bootloader files
isoinfo -f -i file.iso | grep -Ei 'isolinux|grub|efi|vmlinuz|initrd'
```
