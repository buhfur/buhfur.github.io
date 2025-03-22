

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


