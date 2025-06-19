---

layout: default
title: "Android"

---

# Table of Contents 

{: .no_toc }

1. TOC 
{:toc}

---

# MTP Protocol Notes 

- MTP protocol is used with android for transferring files 
- high level file transfer protocol 
- originally used with USB , however implemented TCP / IP , Bluetooth implementations exist.
- implementation can vary across different devices , however it is generally used with mobile phones 
- Clients do not see array of byte blocks that make up data structures that make up a file system
- end device does not expose filesystem and metadata index , therefore ensuring the integrity of both is in full control of the device 
- low risk of file corruption if connection is interrupting during writes/reads 

# Adb snippets 

`adb kill-server` -> stops adb server 

`adb start-server` -> starts adb server

`adb devices` -> list all connected adb devices 


# Mounting on Arch Linux 

1. Install packages 
`sudo pacman -S gvfs-mtp libmtp android-udev mtpfs` 

2. Install tool of your choice , see [Arch Wiki](https://wiki.archlinux.org/title/Media_Transfer_Protocol)

3. Mount using given tool and have fun.

# simple-mtpfs

`fusermount -u mountpoint` -> unmount device at mountpoint 

`simple-mtpfs --device 1 mountpoint` -> mount first connected device at mountpoint 

`simple-mtpfs -l` -> list available devices 
