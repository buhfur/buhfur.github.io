

# Layout of plan to migrate my main desktop from Debian 12 to Arch linux 


## Finalized features 

File manager: Thunar 

Desktop Environment: KDE

Display Manager: sddm 

Terminal emulator: Kitty? 

Text editor: Neovim 

System clipboard: xclip                 

Notification Daemon: idk 

Sound manager: PulseAudio ? 

Font: Ioveska 


## Requested features 

- LVM , /var, /home, /tmp ? 
- Xorg Display server for now but experiment with Wayland display 
- sddm display manager
- encrypted nvme disk 
- desktop password manager 

## Additional packages / software 

- wine 32 & 64 bit  
- ranger 
- git 
- xterm-256color 
- firewalld 
- python3.11 
- rsync 
- openssh 
- docker 
- wikiman 
- tealdeer 
- cifs-utils
- protonvpn 


## How will I test this ? 

I will setup an arch linux VM which will be my testing ground for installing all the new software.

## pacman setup 

/etc/pacman.conf 

**Add multilib for pacman for 32-bit libraries**



## Partitioning scheme 

I plan on using an LVM partitioning scheme for the /home, /tmp, /etc 


### Current partitions on Debian host 


NAME        MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
sda           8:0    0 465.7G  0 disk 
└─sda1        8:1    0 465.7G  0 part /mnt/Todd
sdb           8:16   1  14.9G  0 disk 
├─sdb1        8:17   1  14.9G  0 part /mnt/usb
└─sdb2        8:18   1     1M  0 part 
nvme0n1     259:0    0 931.5G  0 disk 
├─nvme0n1p1 259:1    0   100M  0 part /boot/efi
├─nvme0n1p2 259:2    0    16M  0 part 
├─nvme0n1p3 259:3    0 575.1G  0 part 
├─nvme0n1p4 259:4    0   768M  0 part 
├─nvme0n1p6 259:5    0 325.3G  0 part /
└─nvme0n1p7 259:6    0   977M  0 part [SWAP]


## gdisk partition table listing 

```
Command (? for help): p
Disk /dev/nvme0n1: 1953525168 sectors, 931.5 GiB
Model: CT1000T500SSD8
Sector size (logical/physical): 512/512 bytes
Disk identifier (GUID): 43FAD633-41CE-4C18-8A8C-A68BF0F02253
Partition table holds up to 128 entries
Main partition table begins at sector 2 and ends at sector 33
First usable sector is 34, last usable sector is 1953525134
Partitions will be aligned on 2048-sector boundaries
Total free space is 61445485 sectors (29.3 GiB)

Number  Start (sector)    End (sector)  Size       Code  Name
   1            2048          206847   100.0 MiB   EF00  EFI system partition
   2          206848          239615   16.0 MiB    0C01  Microsoft reserved ...
   3          239616      1206231039   575.1 GiB   0700  windows
   4      1951948800      1953521663   768.0 MiB   2700
   6      1206231040      1888507903   325.3 GiB   8300  debian
   7      1888507904      1890508799   977.0 MiB   8200

```
