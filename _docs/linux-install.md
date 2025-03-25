

# Layout of plan to migrate my main desktop from Debian 12 to Arch linux 


## Finalized features 

File system: BTRFS 

File manager: Thunar 

Desktop Environment: Bwpsm 

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


`/`: 
    - Type: 8304 "Linux"
    - Size: 50Gib
    - File System: Btrfs , zstd compression, COW , 

    Subvolumes:

        - /@
        - /@home
        - /@var
        - /@steam -> for steam apps , to not be included in snapshots 
        - /@snapshots
         

`/boot`: 
    - Type: ea00 "XBOOTLDR"
    - Size: 4GiB






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



## Execution plan 

1. Download Arch image and burn to USB 

2. Backup EFI system partition 

3. Begin arch installation process 

4. Follow installation guide and adhere to partition scheme 

5. Before anything else, create boot part and copy over contents from backup of /boot from earlier 


---


# Problems installing arch 

I've encountered a serious issue while installing arch , the issue is during the initiation which never boots to the root shell. It hangs while performing start jobs for udev init , Network config. In this document I will write out all steps i've taken to mend the issue.


## Known errors 


### Host System USB Errors 

Anytime during boot I see the following errors during boot for the USB hub 1-5 , which shows a -110 error. This occurs during boot with both debian and Arch. This never interrupts boot with debian , however it has made me suspicious if it's interfering with the launch.  




## Actions taken 

### Installation Medium Actions ( can be skipped ) 

For my installation medium , i've been using two different USB sticks. From all my tests, none except for the most obvious issues ( partition table mismatch with BIOS/UEFI ), have indicated any issues. 

1. San Cruze Glide 16GB , No ventoy 

2. Pny USB 16GB , with Ventoy installed on non EFI partition  


### USB Actions  

I found an article which could offer another attempt at fixing this , the link is below , the error might stem from AMD USB issues , which can allegedly , be turned off in the UEFI menu. See the quote below 

"

I had the same error message. If you examine your ports (buses?), you may find that more are indicated than you expect. I found a setting in my UEFI related to AMD that had a USB submenu. I turned off the last one and that solved it for me. I will reboot and report back with some more specifics.

In my UEFI I found it here:

AMD CBS > FCH Common Options > USB Configuration Options

There is a vertical list:
USB0
USB1
USB2
etc

I set USB2 to disabled and my system no longer includes the offending bus (bus 12 and bus 11 are gone).

Essentially, it looks like the UEFI is trying to assign ports to a hub that doesn't exist. If anyone has more info please advise.
"




### Ventoy Actions 

1. Attempted booting all combinations of installation mediums in memdisk 

2. Attempted booting all combinations of installation mediums in Grub2  

3. Attempted booting all combinations of installation mediums in normal  


### BIOS/UEFI/Hardware Actions 

1. Disabled Fast Boot 

2. Enabled/Disabled XHCI passthrough 

3. Enabled/Disabled CSM Compatibility mode  

4. Enabled/Disabled PCIe power state settings, ASPM 

5. Updated ASRock UEFI to version 3.20 ( latest ) using Instant Flash 

6. Checked physical connections to TT controller , enabled 4 switches for fans 


### Kernel Option Actions

- iommu=soft 

- pcie\_aspm=off

- pci=noaer

- nomodeset  

- panic=0 ( not tried ) 

- loglevel=7 ( not tried )

- maxcpus=1 ( not tried )

- bluetooth.blacklist=yes ( not tried )

- modprobe.blacklist=mt7921e ( not tried )
