

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
