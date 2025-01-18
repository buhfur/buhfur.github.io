
# Linux + study guide 

# Managing the linux boot process 

**Show logs from last boot**

- logs are stored in /var/log/boot.log
- To store logs from previous boots , create the /var/log/journal directory as root 

```bash
journalctl -b 
```

# BIOS POST phase 

System Boot Process : 

BIOS: 
    - bootstrap
    - searches for MBR in bootable devices 
     

GRUB: 
    - bootloader 
    - loads the kernel 

Kernel: 
    - Mount root filesystem 
    - Load /sbin/init ( SystemV ) or default.target ( Systemd )

SystemD:
    - initialize system 


- 

## MBR 

- First 446 bytes of the MBR contain error codes such as "missing operating system". Also contains the code for loading GRUB2 

- the next 64 bytes contain the partition table of the drive 

- the last 2 bytes contain the disk signature which contains the /boot directory 

## PXE ( Preboot Execution Environment )

Client / Server facility used to remotely boot operating systems or provision computer systems from a network server. 

Machines are required to have a network card that supports PXE and has a boot device in flash memory 

the NIC looks for a DHCP server to receive an address and then provides a location to one or more TFTP servers 

## UEFI 

* Operates in 32 or 64 bit mode 
* Can exectue applications prior to the OS loading 
* Can read and mount FAT12, FAT16, FAT32 , and VFAT file systems 
* Contains network capabilities without the OS being loaded 
* Can boot drives larger than 2.1TB 
* Allows you to add applicatons to be excuted during the boot process from the UEFI shell 
* Supports remote diagnostics and storage backup 
* Can be configured using `efibootmgr`
* If UEFI is implemented , /sys/firmware/efi will exist an efibootmgr will be available 
* Provides a connection between the OS and the firmware 
* After hardware init , UEFI looks for storage device with and ESP partition 
* ESP partitions are FAT32 partitions 
* ESP partitions contain bootloader code , applications, and device drivers 
* ESP partitions are mounted on `/boot/efi`
* vendor specific applications or EFI utilities are stored in `/boot/efi`
* redhat stores efi in `/boot/efi/EFI/redhat`
* CentOS stores efi in `/boot/efi/EFI/centos`
* Each OS has it's own directory in `/boot/efi`
* Contents of the ESP dictate whether to load a UEFI application , operating system, or automatically load GRUB2


## Grub2 

**Grub notes**

* Grub2 starts naming devices at 1 , while legacy grub starts at 0 
* main config is located in `/boot/grub2/grub.cfg`
* default variables for GRUB2 are located in `/etc/default/grub`
* GRUB2 scripts are located in `/etc/grub.d`
* `grub2-install /dev/sda` reinstalls config files to the `/boot/grub2` directory on the first hard drive
* `/etc/default/grub` file is used for build the `/boot/grub2/grub.cfg` file when `grub2-mkconfig` is ran

**GRUB2 variables**

`GRUB_TIMEOUT`: Boots default entry in *n* number of seconds if no options are selected. 

`GRUB_HIDDEN_TIMEOUT`: Displays blank or splash screen for a specified number of seconds. After this timeframe expires , the system will begin to boot.  

`GRUB_DEFAULT`: Variable which determines the default OS to boot after `GRUB_TIMEOUT` expires. This can be a number or menu entry title.

`GRUB_SAVED_DEFAULT`: Boolean (true/false) which tells GRUB2 to boot the last selected operating system from the menu as the default. This can conflict with `GRUB_DEFAULT` so you can only use one or the other. 

`GRUB_DISTRIBUTOR`: Used when creating menu titles, sets the name of the OS. 

`GRUB_CMDLINE_LINUX`: Linux kernel parameters 

**/etc/grub.d scripts**

`00_header`: Loads GRUB2 settings from `/etc/default/grub`

`10_linux`:  Identifies linux kernels and places them in the menu 

`30_os_prober`: Searches for non-linux operating systems and puts the results in memory 

`40_custom`: Template for custom menu entries 

**Changing GRUB2 Boot operations**

* Make changes by modifying the `/etc/default/grub` or the `40_custom` script in `/etc/grub.d`
* After making changes, run `grub2-mkconfig -i <config-filename>`
* Always make a backup of `/boot/grub2/grub.cfg` before making changes 
* To update grub after making changes , run `grub2-mkconfig -o /boot/grub2/grub.cfg` or run `update-grub2`
* Optional but for consistency you can modify the name of the command by running `ln /usr/sbin/update-grub2 /usr/sbin/grub2-update`. This makes the command start with `grub2` which is common for all grub2 scripts/tools. 

**Check version of GRUB**

```bash
grub-install -V
grub2-install -V
```
**Configuration files for GRUB**

/boot/grub2/grub.cfg -> replaced /boot/grub/menu.lst as GRUB2's main config file. This file is created using the `grub2-mkconfig` command 

/etc/default/grub -> contains default variables used when configuring grub2 

/etc/grub.d/ -> Contains grub2 scripts 


**Boot grub into single user mode**

- Change the `linux64` or `linux` GRUB2 setting by adding "systemd.boot=rescue.target"
- Or at the end of the `ro` option , in the `linux` or `linux16` line , change to the options listed below 

```bash
rw init=/sysroot/bin/sh
```
- Then press Ctrl+x to start the system into shell mode, shell mode doesn't ask for root password 

If the system is using SysV , you can add the `single` to the end of the command line arguments after `linux16` see below. 

```bash
linux16 /vmlinuz-3.10.0-957.el7.x86_64 root=/dev/mapper/rhel-root ro crashkernel=auto single
```

**Change forgotten root password**

1. Press `Esc` when the GRUB2 menu appears 
2. Press `e` to edit the default linux options which will open the GRUB2 boot script for that specific menu entry 
3. Locate the `ro` option in the `linux` or `linux16` line and change to `rw init=/sysroot/bin/sh`. If you're on a Systemd system modify the `linux64` or `linux` GRUB2 setting by adding `systemd.boot=rescue.target`.
4. Press `Ctrl+x` to start the system into shell mode. Which is similar to emergency mode except it doesn't ask for a root password 
5. Once you've reached the shell , run the following commands below to change the root password

```bash

chroot /sysroot
passwd root
touch /.autorelabel # Tells selinux to update new settings 
exit
logout or /sbin/reboot -f 

```
## Systemctl 

**Get default target**

```bash
systemctl get-default
```

## UEFI

* Uses FAT32 partitions which contain bootloader code ,apps , device drivers 

**Where is EFI mounted**

The EFI partition are usually mounted on /boot/efi


## Mount 

Syntax : mount /dev/sdX  /mount/point


# Managing Kernel Modules 

## lsmod 

* lsmod shows currently loaded kernel modules , this data is pulled from /proc/modules 
* If a device driver is loaded and and working , but not showing up in lsmod , that means the device has support compiled into the kernel. 

## rmmod 



