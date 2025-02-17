
# Linux + study guide 

# Basic Configuration & Installation 

## Time management 

* `timedatectl` -> command used to configure time and timezones 

* `/etc/timezone` -> file used to change timezones on debian systems 

* `/usr/share/zoneinfo ` -> Directory which contains info about timezones 
# Managing the linux boot process 

**Show logs from last boot**

- logs are stored in /var/log/boot.log
- To store logs from previous boots , create the /var/log/journal directory as root 

```bash
journalctl -b 
```

## BIOS POST phase 

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


 

## MBR 

- First 446 bytes of the MBR contain error codes such as "missing operating system". Also contains the code for loading GRUB2 

- the next 64 bytes contain the partition table of the drive 

- the last 2 bytes contain the disk signature which contains the /boot directory 

- Boot partitions should be around 100M to 200M in space 

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
## The Kernel Initialization Phase 

* Bootloader loads vmlinux ( system kernel ) 
* Bootloader then loads the initial RAM filesystem `initramfs` 
* GRUB2 then loads the compressed linux kernel `vmlinuz`
* Then the kernel decompresses and mounts initrd or initramfs 
* the RAM filesystem contains kernel modules and apps needed to boot the operating system 
* initrd.img and initramfs.img are created from mkinitrd 
* mkinitrd uses dracut to generate ram disk files 
* Kernel then initializes memory and configures attached hardware using drivers found in initrd 
* After the drivers are loaded , the kernel mounts the filesystem in read-write mode and begins the OS initialization.  

## System V init 

* Once the kernel is running , initrd is unmounted and the init process starts the system scheduler 
* The system scheduler is assigned the PID of 0. The first process on the system 
* The init process is the last step in booting. 
* `/sbin/init` is started which is assigned the PID of 1. 
* `/sbin/init` reads the `/etc/inittab` configuration file 
* You can use the `init` command to change runlevels 
* Use the `runlevel` command to find out which level you are on currently 


### SystemV Run levels 

* 0 - halt the system 
* 1 - single user ( maint mode )
* 2 - multiuser client ( network client ) 
* 3 - multiuser server ( network server )
* 4 - not used ( user customizable )
* 5 - graphics mode ( multiuser server mode )
* 6 - reboot ( sets system level to 0 and back to default runlevel )

## Shutting down the system 
`init 0 ` - halts the system 
`init 6` - set runlevel to 6 , rebooting the system and setting runlevel to default 
`halt` - Shuts down the system 
`reboot` - Reboots system 
`reboot -f` 
`/sbin/reboot -f` 

The `shutdown` command allows you to shutdown the system at a specific time. 

The syntax for the command is shown below 

`shutdown +m -h|-r <message> `

* `+m` : specifies time in minutes when the system should go down. Can also be replaced with the `now` keyword which will shutdown the system instantly. You can also replace this with a time in the format of `hh:mm`

* `-h` : specifies that the system should be halted instead of shutting down 

* `-r` : specifies that the system should be rebooted.

* `<message>` : text message that will be displayed to other users on the system, alerting them of the shutdown 

* To cancel a shutdown , use the `shutdown -c` command in the prompt

* You can also use the `wall` command to send messages to users about system events. You must send the message to the `stdin` of the wall command. See the example below.

```bash
echo "KNOCK KNOCK SYSTEM SHOCK" | wall
```

## Systemd init phase 

* takes advantage of UUID's and TPM's 
* default units are located in `/usr/lib/systemd/system`
* custom units are in `/etc/systemd/system`
* Units in `/etc/systemd/system` override units in `/usr/lib/systemd/system`
* `list-units --type=<unit-type>` to display units by their type 
* `systemctl list-dependencies <unit_name>` : Lists units the `unit_name` depends on 
* `systemctl list-dependencies --reverse <unit_name>` : lists units that depend on `unit_name`
* `systemctl --failed`: lists all failed units
* `systemctl --failed --type=<unit_type>`: lists all failed units of a specific type 


## Systemd unit types 

* service 
* socket
* busname
* target
* snapshot
* device
* mount
* automount
* swap
* timer
* path
* slice
* scope

## Systemd unit syntax 

```bash
[Unit]

Description=    # Description of unit 
After=          # Unit that should run before this unit runs 
Before=         # Unit that should run after this unit 
Requires=       # Required units that have to be started , else this unit will not run 
Wants=          # Units that are desired but if not started, the unit will continue to run regardless
Conflicts=      # Units that cannot run alongside this unit 

[Service]       # Can change according to type of unit , see Type for examples 

Type=           # target, service, device , mount, slice, timer, socket, scope
ExecStart=      # Absolute Path for commands and arguments used to start service
ExecStartPre=   # commands run before ExecStart
ExecStartPost=  # Commands run after ExecStart
ExecStop=       # Commands run to stop the srevice , if not present, process for unit will be killed
ExecStopPost=   # Commands run after ExecStop 
User=           # User privellege to run service as 


[Install]

Alias=          # list of alternative names for the unit 
RequiredBy=     # List of units required by the unit , aka units that have this one set to Requires in the Unit section   
WantedBy=       # List of units that name this unit in their Wants  
Also=           # Contains list of units that will be installed when the unit is started 
and uninstalled with the unit is removed 

---

# Mount unit directives 

[Mount]         # Mount units also contain Install and Unit stanza 
What=           # Absolute path of device to mount 
Where=          # Absolute path of mount point  
Type=           # Optional setting, filesystem type 
Options=        # Comma separated list of mounting options 


```
## Systemd Unit status definitions 

* loaded: Loaded into memory 
* inactive (dead): unit not running 
* active (running): running with one or more active processes 
* active (exited): Completed configuration 
* active (waiting): Running and listening for request
* enabled: Will start when system boots 
* disabled: Will not start when system boots 
* static: Must be started by another service 

## Systemd Controlling Services 

* `systemctl enable <unit>`: Turns on service when system is booted
* `systemctl disable <unit>`: Does not turn on service at boot 
* `systemctl start <unit>`: Starts service immediately 
* `systemctl stop <unit>`: Stops service immediately 
* `systemctl restart <unit>`: Stops and starts unit 
* `systemctl reload <unit>`: Re-reads configuration and continues running 
* `systemctl mask <unit>`: Makes a unit available by creating a symbolic link to `/dev/null`
* `systemctl unmask <unit>`: Removes mask 

## Systemd runlevels 

* (0) runlevel0.target, poweroff.target
* (1) runlevel1.target, rescue.target
* (2) runlevel2.target, multi-user.target
* (3) runlevel3.target, multi-user.target
* (4) runlevel4.target, multi-user.target ( not used in SystemV )
* (5) runlevel5.target, graphical.target
* (6) runlevel6.target, reboot.target

## Systemd targets

* Targets are a group of units and other targets  
* These targets provide a specific function 
* `systemctl --type=target`: Lists all activated targets 
* `systemctl --type=target --all`: Lists all available targets 
* `default.target`: Link to default runlevel target file
* `systemctl get-default`: Prints the default runlevel target file 
* `systemctl set-default`: Command that sets the default runlevel target file referenced by `default.target`
* `systemctl isolate <runlevel-target-file>`: Changes runlevel , used to stop anything that is not apart of the target runlevel 

## Systemd boot errors 

* "Can't find hard drive" -> Hard drive is missing or BIOS/UEFI is misconfigured 
* "Can't find bootloader" -> GRUB2 is incorrectly configured, or misconfigured 
* "Can't load kernel" -> GRUB2 misconfiguration or kernel misconfiguration
* "Web service failed to start" -> Boot successful , some applications may be misconfigured 

## Kernel panic 

* Protective measure to prevent data loss / corruption when an unrecoverable error is encountered 
* Can be caused by configuration or software issues : missing, misconfigured, failed hardware , failed hardware drivers , system resource problems  
* `kexec` , `kdump`, `crash` are utilities used for storing logs and troubleshooting kernel panics. These tools may not be installed by default 
* `kdump` saves logs to memory and can even send the logs to a remote machine 
* during the boot process memory is reserved for logs `kdump` generates.
* This process is set by the `crashkernel` directive on the GRUB command line 
* The specific size of memory reserved is dependent on the type of hardware and to the total system memory available. 
* You can change the allocation size of the `crashkernel` directive in the
* The `kexec` boots the kernel from another kernel but does not use BIOS to preserve the system state and allow you to analyze the crash dump datausing the `crash` command.

---

# Managing Hardware Under Linux  

* Only topics "Discovering Devices" and "Managing Kernel Modules" are covered on the exam 

## Discovering Devices 

* Discovery starts during the boot process with searching through system busses for devices.   
* When a device is found , it's properties are discovered and named so applications can access the device. 
* Bus is a system device used to transfer data between devices 
* Ports are physical connections to a Bus 
* Devices are system components capable of receiving or providing data 
* Drivers are files or groups of files used to control a hardware device 
* The kernel ring buffer can be displayed with `dmesg` 
* USB detection with `lsusb`
* PCI detection with `lspci`

## Displaying the Kernel Ring Buffer with dmesg   

* Kernel ring buffer is a cyclical storage space containing kernel log messages.  
* Once all data reserved is used , data is overwritten starting from the beginning of the buffer. 
* Data from the ring buffer is handled by syslog 
* dmesg use term "level" to describe syslog prio 

## Syslog facilities & syslog Priorities 

Left side is facilities , right is priorities or "levels".

* kern   -> emerg 
* user   -> alert 
* mail   -> crit 
* daemon -> err
* auth   -> warn
* syslog -> notice
* lpr    -> info 
* news   -> debug 

## dmesg

```bash
dmest -T -f <FACILITY> -l <PRIO-LEVEL> 
```

* `-T` -> timestamp 
* `-f` -> syslog facility option  
Other options include 
* `-L` -> adds color to output 
* `H` -> Human readable output 

**Search for USB events**

```bash
dmesg | grep usb 
```

## lsusb 

* Allows you to list all available USB devices 
* Scans for USB busses 
* uses udev hardware DB to associated human readable name to vendor ID and product ID 
* `lsusb` will tell about the USB hub , driver , and transfer speed 
* transfer is in megabits 
* man page is the best reference for options 
* Transfer speeds of `12M` indicate USB1.0 
* Transfer speeds of `480M` indicate USB2.0 
* Transfer speeds of `5000M` indicate USB3.0 
* Transfer speeds of `10000M` indicate USB3.1 Gen 2(USB3.2 Gen 2x2) 
* Transfer speeds of `20000M` indicate USB3.2 
* The output of the ID's are in the format `<manufacturer:device_id>`
* Table of known manufacturer and device ID's can be found at http://www.linux-usb.org/usb.ids
* `/dev/bus/usb/` -> location for USB device files and contains a directory for each Bus, 
* In each directory for a USB Bus, there are device files for each Device 
* File of known USB ID's are stored in `/usr/share/hwdata/usb.ids`
 


### View info based on bus number and device number 

```bash
lsusb -D /dev/bus/usb/<bus_number>/<device_number>
```

The output to this command is similar to the output of `lsusb -v`

## The USB Root Hub 

* First USB device on a linux system. 
* responsible for communicating with devices attached to hub ports and the hub controller.
* The hub controller monitors removed and newly added devices 
* manages power for devices on host ports 
* manages communication on the controllers bus. 

## lspci 

* Peripheral Component Interconnect 
* supports 32-bit or 64-bit addressing 
* supports ultra DMA burst mode for PATA. 
* can auto detect PCI peripheral boards 
* multiple PCI busses can be joined using bridges 
* 256 busses max per system 
* Larger systems use domains if more than 256 is needed 
* Each bus can host up to 32 devices 
* PCI bus hierarchy starts at bus(0) 
* `Bus:Device:Function Class Vendor name` -> Format of output from `lspci`
* `lspci -D` -> displays domain number in `Domain:Bus:Device:Function` format
* without `-D` domain is assumed to be 0000.
* File of known USB ID's are stored in `/usr/share/hwdata/pci.ids`
* `lspci -vv` -> displays additional bus information 
* `lspci -t` -> displays tree 
* In the output of `lspci -v` you can see the class ID's for each device in the format of `<vendor:device>`

## Host Bus Adapter (HBA)

* Used to connect multiple devies using a single controller 
* Examples of host adapters : IDE , SCSI, SATA , iSCSI, Fiber channel, Network Controllers
* `lspci -nn | grep -i hba` -> Shows HBA adapters
* `systool -c <adapter-class>` -> Needs `sysfsutils` installed 
* You can also view HBA adapter info in `/sys/class`

## Managing Kernel Modules 

* `lsmod` -> Lists all currently loaded kernel modules 

* `lsmod` pulls data from `/proc/modules` and outputs in human-readble format  

* To blacklist kernel modules,  add them to the `/etc/modprobe.d/blacklist.conf` file

* `echo "blacklist joydev" >> /etc/modprobe.d/blacklist.conf` -> Blacklists `joydev` kernel module 

* `modinfo <module-name>` -> View info about loaded module 

* `depmod` -> Used to build dependency file to load kernel modules 

* `depmod` Builds a file named `modules.dep` and stores in `/lib/modules/<kernel_version>/`

* `ls /lib/modules/$(uname -r)` -> lists kernel modules for current linux kernel version being used.  

* `modules.dep` -> file that shows dependencies between kernel modules 

* This file also helps kernel utilities ensure dependent modules are loaded.  

* Once the `modules.dep` file is generated , you can use `insmod` to load the module 

* `insmod <module_filename>`

* `<module_filename` -> Module file located in `/lib/modules/<kernel_version>/kernel`

* `insmod` Doesn't install module dependencies found by `depmod`

* `modprobe` -> Preferred Alternative to `insmod`, loads kernel module dependencies by default 

* `modprobe <module_name>` -> Loads kernel module 

* modules loaded with `modprobe` are not persistent after restarts 

* modprobe is run every time the kernel loads to automatically detect devices 

* `/etc/modprobe.d/*.conf` -> Files that determine which kernel modules are loaded at startup

* `rmmod <module_name>` -> Unloads a kernel module , will not work if device using the module is in use.

* `rmmod` does not take dependencies into account 

* `modprobe -r <module_name>` -> unloads module and takes dependencies into account 

## Modprobe directives 

These are lines you can add into your `/etc/modprobe.d/*.conf` files to manage kernel modules during startup 

* `install <module-name>` -> tells `modprobe` to load specified module  

* `alias <alias_name> <module_name>` -> alias which can be referenced from shell prompt 

* `options <module_name> options` -> List of options for the `<module_name>`. examples are `irq=` and `io=` which are used when the module loads 

## Finding block devices 

```bash 
lshw -short -class volume
```

```bash 
ls -l /sys/block
```

```bash 
lshw -short -class volume
```

# Shell scripting 

## Highlights 

* `exit 0` -> 0 means success 

* `PATH=$PATH:.` -> sets path to current working directory, this poses a security risk as hackers could place malicious applications with similar names as common util scripts `passwd` for examples,  in your current directory.

* `cat /etc/shells or chsh -l` -> outputs list of available command line interpreters 

* Interpreter is defined by login account.

* Strings defined in Integer variables are assigned the value of 0

## Globbing Wildcard Characters 

* Allows you to filter for different output based on patterns , very similar to regex  

## Wildcards 

`*` -> matches any one character , or the beginning character 

`?` -> matches any single character after the expression 

`[ahp]` -> Matches single character between the brackets 

`[D-H]` -> Matches single character within the range  

### Globbing snippets 

```bash
ls [nf]*0 
```
Only lists files that start with n or f , and end with zero.

```bash
ls f*1 
```
Only files that start with f and end with 1.

```bash
ls ????
```
Only lists files with 4 characters 

```bash
touch file note {file,note}{0..20}
```
Generates 20 files with filenames matching `file0..file20` and `note0..note20`.

## Sequencing 

`||` -> runs command if previous command fails 

`&&` -> runs command if previous command was successful

`;` -> runs next command regardless of success or failure

`echo $?` -> retrieves the exit code of the last command run 
* Unsuccessful programs send exit code of 1 

## Command substitution 

* \`command\` usable 

* `$()` -> usable 

### Examples 

```bash
ls /lib/modules/$(uname -r) 
```
Outputs the kernel modules directory with a substituted command that retrieves the kernel version.

```bash
echo "Current time : `date +%m/%d/%y" "%R`"
```
Outputs the time with the current date in 24-hour time. 

## Variables 

* Can use the `declare -p` command to print out the attributes of a defined variable 

* `declare -i <variable_name>=<value>` -> declares integer variable

* Output of `declare` is in the format of `typset -<type> <var_name>=<value>`

## Reading user input 

* `read` -> command used to read user input 
* If no variable argument is supplied, the response from the user is stored in the variable REPLY

## Positional parameters 

* Command line arguments can be referenced in the order they are sent , see examples below 

* `$0` -> Name of command or script 

* `$#` -> number of command line arguments 

* `$*` -> list of command line arguments 

* `$$` -> Current process PID 

* `$!` -> PID of last background job 

* `$?` -> Command exit status code  


  
### Examples 

```bash
#!/bin/bash
echo "$1"
```

## File testing operators 

* -a or -e Does the file exist?  [ -a /root/George ]

* -s Is the file size greater than 0?  [ -s /etc/bashrc ]

* -f Is the file a text file?  [ -f /etc/bashrc ]

* -d Is the file a directory?  [ -d /etc ]

* -b Is the file a block device?  [ -b /dev/sda ]

* -c Is the file a character device?  [ -c /dev/tty1 ]

* -p Is the file a named pipe?  [ -p /run/dmeventd-client ]

* -L or -h Is the file a symbolic link? 

* -r Does the file have read permissions for the user executing the test?

* -w Does the file have write permissions for the user executing the test?  -x Does the file have execute permissions for the user executing the test?

* -u Does the file have SUID applied?

* -g Does the file have SGID applied?

* -k Does the file have sticky bit applied?

* -O <username> Is the file owned by the user specified by the argument <username>?

* -G <group_name> Is the file owned by the user specified by the argument <group_name>?

## Testing conditionals 

* In bash , the `test` statement can be used to test conditions

* `test <expression>` -> syntax 

### Examples 

```bash
test $EUID -eq 0; echo 
```

## Functions 

* Function paramater arguments are referenced the same way command line arguments are for example `$1`

```bash
function function_name(){
}
    
```
## Text processing 


# Configuring Network Settings 

* FTP listens on port 20 ( data transfer ) and 21 ( control connection ) 

* Ports range from 0-65536 , host ephemeral ports 

* Reserved ports range from 0-1023 , well known ports 

* ICANN reserves ports 1024-49151, special purposes for orgs  

* Dynamic ports are 49152-65535, used for any network service 

* `/etc/services` ->  List of default ports used for services

## Known ports 

* Ports 20 and 21: FTP

* Port 22: Secure Shell (SSH, SCP, SFTP, STelnet)

* Port 23: Telnet

* Port 25: SMTP

* Port 53: DNS

* Port 80: HTTP

* Port 110: POP3

* Port 123: NTP (time synchronization)

* Ports 137, 138, and 139: NetBIOS

* Port 143: IMAP

* Port 389: LDAP

* Port 443: HTTPS

* Port 514: Syslog remote logging

## IP classes 

* Class A : 126 possible networks 16.7 million possible addresses , first octet must be between 1-126

* Class B : 16,384 possible networks , 65,534 possible hosts 

* Class C : values of 192-223 , 2,097,152 possible networks , 254 hosts per network. 

* To calculate number of hosts per network , the zeroes in the subnet as "Z" use this formula 

> `N = 2^Z - 2`

* Subnet mask CIDR notation integers represent the total 1-bits used for the subnet mask 
 
* If you're given the CIDR notation , use the formula below 

`N  = 2^(32-CIDR) - 2`


## Configuring Network Addressing Parameters 

* Onboard network adapter is assigned an index number provided by the BIOS

* `ethtool` -> allows you to list and alter NIC settings 

## Network Config Snippets And Commands  

* `ip addr add <ip> dev <device>` -> Adds IP to existing network interface 

* `ip addr del <ip> dev <device>` -> Removes IP from existing network interface  

* `ip link set <interface> down` -> Disables network interface 

* `ip link set <interface> up` -> Enables network interface 

* `ip addr flush dev <interface` -> Resets changes 

* `systemctl restart network` -> Restarts network interfaces on Red hat systems 

* `systemctl restart networking` -> Restarts network interfaces on debian systems 

* `ip link` -> Shows MAC addresses , L2 info 

* `ip route` -> Shows configured L3 routes, default gateways  

* `/etc/sysconfig/network-scripts/ifcfg-<interface>/` -> Location of network interface configuration file 

* `/etc/network/interfaces` -> Network interface configuration on debian systems 

* `route` -> shows kernel IP routing table , stored in RAM 

## Interface configuration 

> Add these changes to the config file in the `/etc/sysconfig/network-scripts/ifcfg-<interface>` file. 


**To configure DHCP**

* IPADDR , NETMASK , NETWORK, BROADCAST are not required if using DHCP 

```bash
BOOTPROTO='dhcp'
NAME='<name>'
NETMASK=''
NETWORK=''
```

## FreeBSD Network Interface Configuration 

* `/etc/rc.conf` -> Config file for interfaces 

* `sudo service netif restart` -> restarts networking service 

* `sudo service routing restart` -> restarts routing service.

### Config file syntax 

```bash
hostname="trueNAS"
ifconfig_vtnet1="inet 192.168.x.x netmask 255.255.255.0"
defaultrouter="192.168.x.1"
```

This configures the `vtnet0` interface with a static IP and a persistent default route.

## DHCP 

* /etc/hostname -> config file for hostname , can be made persistent through using `hostnamectl set-hostname <hostname>`

* /etc/dhcpd.conf -> Config file for DHCP , including the lease time. 

* In /etc/dhcpd.conf  , modify `max-lease-time` to get shorter or longer lease times. 

* If DHCP server cannot be reached , run `dhclient <interface> -v` to retrieve IP 


## Nmcli 

* `nmcli con show <interface>` -> shows info about specified interface 

* `nmcli con modify <interface> +ipv4.addresses <ip_addr>/<CIDR>` -> adds new IPv4 address to interface. 

* `nmcli con modify <interface> -ipv4.addresses <ip_addr>/<CIDR>` -> removes IPv4 address on interface. 

* `nmcli con up <interface>` -> brings up network interface 

## Routing 

* `route add -net <network_address> netmask <netmask> gw <router_address>` -> adds new L3 route 

* `route add -net <network_address> netmask <netmask> gw <router_address>`-> removes L3 route

* `route add default gw <ip>` -> adds default route 

* Changes made with `route` and `ip route` are not persistent after reboot 
* `ip route add <ip>/<CIDR> via <router_addr> dev <interface>` -> adds route 

* `ip route del <ip>/<CIDR>` -> deletes route

## DNS 

* `/etc/hosts` -> first file used for name resolution. 

* DNS is only used if record for the domain is not in the `/etc/hosts` file. 

* `<IP_address>     <host_name>     <alias>` -> Syntax for `/etc/hosts` file.

* A dns server is authoritive if it contains a record for the requested domain in it's DB of name mappings.

* DNS servers cache previously resolved domains for a period of time so they can respond directly to the client than repeat the resolution process.

* `/etc/resolv.conf` -> configuration file which specifies DNS servers to be used 

* There are two keywords used in the `/etc/resolv.conf` file , `search` and `nameserver`

* `search` -> entry which specifies how to handle imcomplete domain names 

* `nameserver` -> entry which specifies IP for DNS server 

* `/etc/nsswitch.conf` -> defines the order of which services will be used for name resolution 

### DNS process 

1. Client sends DNS request to DNS server on port 53. If server is authoritive , the IP of the domain is sent to the host. If not , continue to step 2.  

2. DNS server sends request to root level DNS server, which are able to resolve top level domains like .gov , .com , .edu etc.

3. DNS server sends IP of domain to host , which then contacted using this IP 

## /etc/nsswitch.conf

file determines priority and method for which services are used for name resolution. 

```bash
hosts:     files dns
networks:  files dns
```
These two lines dictate that the `/etc/hosts` file will be consulted first , afterwards `/etc/resolv.conf` will be consulted for name resolution. 

## IPv6 

* uses 128-bit addresses 

* 8 4-character hexadecimal number make up each of the hextets

* Zeroe's in the addresses can be abbreviated , but a hextet of `0000` can only be abbreviated once.

* IPv6 uses DHCPv6 

* each hextet can be represented between 0-FFFF.

# Troubleshooting Network problems 

## Ping 

If you can ping another host and receive a timely response you know 3 things. 

1. The network interface is working correctly 

2. The destination system is up and working correctly 

3. The network hardware on both ends is working correctly  


## Netstat 

* can be used to list network connections 

* Can display the routing table 

* Can display info about the network interface 

* Has been replaced by `ss` or "show sockets"

### Nestat snippets 

* `netstat -a` -> lists all listening and nonlistening sockets 

* `netstat -i` -> display statistics for network interface 

* `netstat -r` -> displays routing table 

## nc 

* `nc -l 2388` -> opens listening socket on port 2388 

* `nc 192.168.3.104 2388` -> initiates connection to IP on port 2388. 

## Network based filesystems 

* `showmount -e system5` -> lists possible shares on "server5" server that's available to clients  

* `mount -t nfs <hostname>:/<share_name> /mnt/<mount_dir>` -> mounts the `<share_name>` share on `/mnt/<mount_dir>`.

* Nfs has a feature called automounting which automatically remounts the shared filesystem once it's availble. 

## samba 

* `smbd -V` -> outputs SMB version ( not proto version for clients ).

* `/etc/local/smb4.conf` -> config file on TrueNAS for SMB. 

* `/conf/base/etc/local/smb4.conf` -> global base config file for SMB 

* `grep "server min protocol" /etc/local/smb4.conf` -> Command to find minimum SMB version used by clients.

* `smbclient -L <server_hostname> -U <username> ` -> view shared filesystems 

* `mount -t cifs -o //192.168.x.x/Share /mnt/point -o username=<username>,...,password=<password> ...` -> command to mount smb share 

---

# Understanding Network Security 


## Symmetric Encryption 

* Single private key 

* Same key used for encryption and de-cryption 

* Both sender and receiver must have exact same key 

* Examples : Blowfish , 3DES , AES 

* Also called "Secret Key Encryption" 

## Asymmetric Encryption 

* Uses 2 keys , public & private 

* The public key is known to everyone , while the private key is only known to the owner 

* Public key is used for encryption 

* Private key is used for decryption 

* Data is signed with the public key 

* Data is verified with the private key 

* Examples : RSA , DSA , Diffie-Hellman 

* You can use the senders public key to verify the data did come from the actual sender 

* Data is sent with the users private key 

* Very difficult to break  

* Commonly used in online shopping to hide users payment info or social security number. 

* Certificate Authorities are used to verify a website is legit 

* Examples: Verisign , Entrust, DigiCert 

* Verifies site is not a spoofed site 

* PKI: Public Key infrastructure 

* PII: Personally Identifiable Information 

* Some examples of PII are: Account numbers, Residential Addresses, Tax identification numbers. 

* PII is encrypted with SSL ( Secure Sockets Layer) / TLS ( Transport Layer Security )

* When you see a lock on a webpage you are visiting , this indicates the site is being accessed through HTTPS. Which uses Transport Layer Security ( TLS ) or formerly ( Secure Sockets Layer ). 

* `openssl s_client -connect mysite.com:443` -> command which retrieves info on hashing algorithms or digital certificates being used by the site. Also verifies that it's using either SSL/TLS to begin with.

* Admins can mint their own certs which can be used to encrypt both network transmissions and files in the filesystem. These certificates are called "self-signed certificates"  

* Wildcard certificates can be a cost effective way to sign certificates for both domains and subdomains

* For example a wildcard certificate for "suss.com" can be used for "cap.suss.com" and "amogus.suss.com"

## Integrity Checking Via Hashing 

* Used to verify a file that has not been altered 

* One way encryption or hashing is used 

* File integrity checking is done by ensuring the downloaded files hash matches the vendors hash  

* MD5 & SHA are the two most popular tools 

* Use `md5sum` to output the MD5 hash of a file 

* SHA-256 is less likely to produce the same hash for two different files , also called a collision 

* If the hash differs from the vendors , this may indicate the file is only a partial download or infected with malware ( altered )

* `sha256sum` -> command to output SHA-256 hash of a file.

* `sha1sum` -> command to output SHA hash of a file.

* `sha512sum` -> command to output SHA hash of a file.


# Implementing Secured Tunnel Networks 

* older tools which lacked encryption and no longer used on public networks 
    - telnet 

    - rlogin 

    - rsh 

    - rcp or FTP 

## Chapter highlights

* sshd -> ssh daemon 

* ssh -> ssh client used to connect to SSH server 

* scp -> secure copy files between systems 

* sftp -> secure copy , imitates FTP 

* slogin -> LIke SSH , access shell prompt remotely 

* ssh-keygen -> used to create users public / private keys. Also makes self signed certificates 

* ssh uses both asymmetric and symmetric encryption 

* The privelleged port for ssh is defined in the `/etc/services` file.

* `ssh -l <username> <server-ip> [OPTIONS] /sbin/ip addr` -> runs a command on a remote system through ssh. 

* `scp file.txt <user-name>@<ip-or-hostname-of-server>:/install/dir` -> Syntax for scp 

* `sftp <username>@<ip-or-hostame>` -> sftp syntax 

* `get /file/on/server /host/dir` -> sftp CLI syntax 

* `rsync` -> backup tool which uploads file updates rather than duplicates. Uses SSH to encrypt data transfers

* `rsync -av /file/dir/* user@remote:/filedir` -> Syntax template for syncing the contents of a directory to a remote machine using SSH. 

## Key locations 

* `/etc/ssh/ssh_host_key` -> SSH server private key. Should never be shared. 

* `/etc/ssh/ssh_host_key.pub` -> SSH server public key. Given to clients 

* `/etc/ssh/ssh_known_hosts` -> Client Public keys for SSH servers. 

* `~/.ssh/known_hosts` -> Public keys for SSH clients that have connected to the system. Located in users home dir. Can be added when connecting to host server 

* `/etc/ssh/id_rsa` -> SSH Clients private key using RSA encryption .  

* `/etc/ssh/id_rsa.pub` -> SSH Clients public key using RSA encryption .  

* `~/.ssh/authorized_keys` -> File on Client which contains authorized public keys for various SSH servers such as `id_rsa.pub` and similar keys. Stored in users home directory on client machine. 


### SSH connection process 

1. SSH client creates connection to SSH server. The SSH server is assumed to be listening on Port 22.

2. SSH server sends public key to client 

3. Client receives public key and checks if it already has a copy of the servers public key. 

4. If the Client does not have public key from the server in either `/etc/ssh/ssh_known_hosts` or `~/.ssh/known_hosts`. The user client is prompted to add it.

5. After the public key from the server is found or added , the client now trusts the remote system and generates the symmetric key.

6. Client then uses the servers public key to encrypt the new secret key and sends it to the server.

7. The server descrypts the symmetric key using it's own private key. The result of this is now both systems now have the same secret key. 

8. Now that both the client and server have the same secret key , they can now use faster asymmetric encryption during the rest of the SSH session.

9. The user is then prompted to login securely now that everything the user types is sent in an encrypted format.

10. After the secure channel is negotiated , and the user has been authenticated. Data can now be sent securely. 

## Configuring SSH 

* openssh package is required , includes the sshd daemon and ssh client.

* `/etc/ssh/sshd_config` -> config file for ssh servers 

* `/etc/ssh/ssh_config` or `~/.ssh/config` -> Config file for ssh clients

### Server configuration options 

* AllowUsers -> Restricts login to specified users , space separated list

* DenyUsers -> Opposite of AllowUsers , space separated list.

* PermitRootLogin -> Yes or no value which allows or disallows the root user to authenticate through ssh server

### Client configuration options 

* Port -> Port used by server to connect through.

* User -> Specified user to authenticate as.

* `/etc/ssh/ssh_config` applies to all users, can be overridden by `~/.ssh/config` for a specific user 

* Can also configure ssh client options through ssh command line options  
* If no user is specified in command line arguments like `-l` , the system will try to authenticate as $USER.

* Type `exit` to terminate the connection 

## Logging into SSH without a password

* Token based authentication 

* Use `ssh-copy-id` to copy the clients public key to the `/etc/authorized_keys` on the servers filesystem.  

* `ssh-keygen` -> command used to create public/private keys on client system.

### Public key authentication setup 

### Step by step process 

1. On client , use `ssh-keygen -t [rsa,dsa]` to create keys. Could use both , RSA for encryption and DSA for decryption.  

2. Use default directory for storing keys , `~/.ssh/id_rsa` and `~/.ssh/id_dsa`. 

3. Assign passphrase to the key , making the key useless if the passphrase is not known to an attacker.

4. Securely copy key to server using the command in step 5.

5. `scp ~/.ssh/<keyname>.pub user@remote:/filename`

6. Then append contents of public key to `~/.ssh/authorized_keys` file on the server.

7. To avoid entering the passphrase everytime you authenticate with the server, use the `ssh-agent` command which caches the key passphrase with the `ssh-add` command. This passphrase is cached in memory.  

8. You can also use `ssh-copy-id` which will automatically add the public key to the servers `~/.ssh/authorized_keys` file  

---

# Virtual Private Networks 

* Allows users to connect to remote servers over untrusted networks. 

* Encryption and tunneling protocols are used. 

* IPSec -> tunneling protocol 

* DTLS -> Datagram Transport Layer Security. 

* VPN's can be used for workers at home to connect to the office's network securly over an untrusted public network.  

* Types of VPN's -> SSH , SSL( Not really SSL , Uses TLS but the term stayed the same )

* SSL VPN's use TLS for encryption 

* If VPN settings are correct but the system won't connect , check firewall rules and make sure the ports used are open. 

## SSH Port Forwarding 

* Allows tunneling of unencrypted services over encrypted SSH channel. 

* Example: Accessing web server in DMZ via a jump server 

* An SSH jumpserver is a hardened server with intrusion detection that serves protocols running in a DMZ more efficiently.

* The `-L` option in `ssh` command allows you to connect through a jump server and forward requests to the intended destination. 

* `ssh -L 80:web.server.com:80 jump.server.com` -> connects to SSH jump server which then forwards requests to web server. 

## SSH Dynammic Port Forwarding. 

* Allows forwarding requests to a server through an SSH proxy. Which would use an encrypted SSH connection.

* For example , you can use this to forward requests to a web server through an encrypted SSH connection. Using the command below 

* `ssh -D 55555 shah@ssh.server.com` -> Opens proxy on local machine which forwards requests to a web server using port 55555.

## High Availability Networking   

* Network bridging can enhance availability. Converts local LAN adapter.  

* Dual homes two or more netowrks for fault tolerance.  

* Bonding dual-homes network segments to boost network throughput. 


## Redundant Networks 

* Network Bridge Control -> bridge two networks together ( fault tolerance )

* Network Bonding -> Bond network segments ( Boost throughput )

* Overlay networks allow admins to configure mutliple L3 addresses to a local network card. Making it act like act like multiple network cards.  

* On both debian and centos systems , install the "net-tools" and "bridge-utils" packages. 

* On centos systems,  make sure to install the "epel-release" package. 

## Configure overlay network IP's ( Legacy )

Please note that these are legacy methods, `nmcli` and `ip` have replaced ifconfig and therefore do not use the older aliases e.g "eth0:0" , current tools list the newly added IP's under the interface rather than use aliases for each.

```bash
ifconfig <interface> inet <ip-address>/<CIDR>
```


```bash
ifconfig eth0:0 inet 192.168.2.2/24
ifconfig eth0:1 inet 192.168.4.2/24
ifconfig eth0:2 inet 192.168.5.2/24
```

## Bridging configuration 

* `brctl` -> base command to configure bridging , is installed with `bridge-utils` package. 

* `brctl addbr <name>` -> create bridge and assign name 

* `brctl addif <bridge-name> <interface> ` -> Adds interface to network bridge, can add overlay network IP. 

* `ip link set <bridge-name> up ` -> enables bridge 

### Steps to create and add a bridge for two hosts  

**On host 1**

1. `ifconfig eth0:0 inet 192.168.2.2/24`

2. `brctl addbr br0`

3. `brctl addif br0 eth0:0`

4. `ip link set br0 up`

5. `ping 192.168.2.3`

**On host 2**

1. `ifconfig eth0:0 inet 192.168.2.3/24`

2. `brctl addbr br0`

3. `brctl addif br0 eth0:0`

4. `ip link set br0 up`

5. `ping 192.168.2.2`


## Network Bonding

* `/etc/sysconfig/network-scripts/ifcfg-bond0` -> script for defining interface bond 

* Make sure to add these parameters below to configure the bond 

```
MASTER=bond0
SLAVE=yes 
```

### Bond Interface configuration 

```bash
DEVICE=bond0
NAME=bond0
TYPE=Bond
BONDING_MASTER=yes
IPADDR=x.x.x.x
PREFIX=24
ONBOOT=yes
BOOTPROTO=none
BONDING_OPTS="mode=1 miimon=100"
```

## Bonding modes 

* Round-Robin -> 0, Packets are sent in order across all NIC's , default

* Active-Passive -> 1 , One NIC sleeps and activates if another fails 

* Aggregation -> 4, NIC's act as one , resulting in higher throughput 

* Load-balancing -> 5, Traffic is equally balanced over all NIC's 


## Single Sign ON ( SSO )

* Uses one-time passwords  

### RADIUS 


* RADIUS -> Remote Authentication Dial-In service

* RADIUS authentication is done by a central server  

* Once a user authenticates , they are granted an IP and access to the network  

* RADIUS uses ports 1812/udp , 1813/udp

### LDAP 

* LDAP -> Open source active directory 

* LDAP uses the X.500 standard 

* LDAPv3 uses TLS 

* LDAP uses ports 389 , 636 if using TLS 

### Kerberos 
* Kerberos -> Preferred on public networks 

* No passwords , encrypted or otherwise are sent across the network 

* Kerberos uses tickets which expire after a set time. 

* Time must by synced between clients/servers to work. 

* Kerberos uses port 88

* `kinit <username>@<hostname>` -> starts kerberos session 

* `kinit -a` -> View addresses associated with current kerberos tickets 

* `kinit -v` -> details on tickets, length , expiration times , renewal times. 

### TACACS+ 

* TACACS+ -> combines auth , authorization , and accounting 

* Entire transaction is encrypted 

* Can be placed on 3 different servers 

* not compatible with older versions 

* Uses TCP port 49 , 

* originally designed for Cisco 


## Defending against Network Attacks 

* Disabling unused services 

* Installing security updates   

* `systemctl list-unit-files` -> list each service and status 

* `chkconfig <service> off` -> legacy tool used for disabling services 

* `netstat -l` -> shows listening ports 

* `lsof -i` -> lists network services and their ports 

* Firewalls filter incoming and outgoing traffic between private and public networks

* Firewalls compare the origin address, origin port, destination address, destination port, type of packet, and protocol used against an Access Control List ( ACL ) to decide whether to forward or drop the traffic. 

* Packet filtering firewalls are stateless, they do not know the state of the connection.  

* Stateful firwalls maintain state of connection , commonly used. 

* `echo 1 > /proc/sys/net/ipv4/ip_forward` -> enables Ipv4 port forwarding 

* `echo 1 > /proc/sys/net/ipv6/ip_forward` -> enables Ipv4 port forwarding 

* `echo 1 > /proc/sys/net/ipv6/conf/all/forwarding` -> enables forwarding for all network interfaces on system 


## Firwalld Notes and Snippets 

* `firewall-cmd --get-default-zone` -> gets default zone 

* `firewall-cmd --set-default-zone=internal` -> sets default zone 

* `firewall-cmd --list-all` 

* Review firewall-cmd zones in book , page 508 

## Iptables 

* netfilter -> used by kernel to filter packets 

* chain -> firewall rule for incoming packets 

* Filter table -> used for creating packet filtering rules 

* iptables can add, delete , insert , append rules 

* First rule in chain is labeled "1" 

* `iptables -t <table> <command> <chain> <options>` -> iptables syntax 

* `iptables -L` -> list all rules in chain 

* `iptables -N <chain_name>` -> Create new chain 

* `iptables -I <chain_name>` -> Inserts rule in chain 

* `iptables -R <chain_name>` -> Replaces rule in chain 

* `iptables -D <chain_name>` -> Deletes rule in chain 

* `iptables -F <chain_name>` -> Deletes all rules in chain 

* `iptables -P <chain_name>` -> Sets dfault policy for chain 

* `iptables -p` -> specifies protocol , can use "all", "tcp", "udp", "icmp"

* `iptables -s <ip/mask>` -> specify source address 

* `iptables -s 0/0` -> Checks all source addresses 

* `iptables -d <ip/mask>` -> specify destination address 

* `iptables -d 0/0` -> Checks all destination addresses 

* `iptables -j <target>` -> tells iptables what to do when packet matches a rule , ACCEPT, REJECT, DROP, LOG

* `iptables -i <interface>` -> Specifies interface where packet is received. Only applies to INPUT and FORWARD chains. 

* `iptables -o <interface>` -> Specifies interface where packet is to be sent , only applies to OUTPUT and FORWARD chains 

* `iptables-save` -> save changes to firewall tables 

* `iptables-restore` -> resotre tables from file created 

* `iptables -D FORWARD 1` -> Delete first rule in FORWARD chain 

* `iptables -t filter -F` -> deletes all rules from filter table 

* `iptables -P INPUT DROP` -> Sets default policy for INPUT chain to DROP all incoming packets 

* `iptables -P FORWARD DROP` -> Sets default policy for FORWARD chain to drop all forwarded packets

* `iptables -A INPUT -s 0/0 -p icmp -j DROP` -> Appens rule to chain for all incoming ICMP packets for any address to be dropped. Tl;dr all incoming ICMP packets from any address are dropped.

* `iptables -A INPUT -i eth0 -s 192.168.2.0/24 -j DROP` -> Appends rule to INPUT chain which drops packets received on the eth0 interface from any address on the 192.168.2.0/24 subnet. 


### Default chains ( rules )  

* FORWARD: Packets transferred between networks  

* INPUT: Packets sent to local linux system 

* OUTPUT: Packets sent from local linux system 

Each chain has 4 policies 

* ACCEPT 

* DROP

* QUEUE

* REJECT


## UFW 

* `/etc/ufw` -> location for before.rules and after.rules files which define firewall rules. 



## nftables 

* made to improve performance issues with iptables 

### nftables snippets 

* `iptables-translate` -> tool to translate iptables rules into nftables rules.

* `nft add rule ip filter INPUT tcp dport 22 ct state new,established counter accept` -> allow ssh 

* `nft add rule ip filter INPUT ip protocol tcp dport {80,443} ct state new,established counter accept` -> allows HTTP & HTTPS traffic 


## GPG 

* Gnu Privacy Guard , open source version of OpenPGP 

* `~/.gnupg` -> local directory , stores user keys and config file. 

* `gpg --gen-key` -> generates key 

* secring.gpg -> secret key 

* pubring.gpg -> public key 

* trustdb.gpg -> GPG trust database o


## Securing the OS 

* `nohup <command> &` -> command that specifies a process to ignore any hangup signals it receives , such as a user logging out. 

* `find -name named &` -> finds command loaded with `nohup`

* Disabling USB ports , protects data. 

* `rm /lib/modules/$(uname -r)/kernel/drivers/usb/storage/usb-storage.ko.xz` -> Disables USB ports by removing USB driver. 

* Perform security scans 

* Disable FTP and Telnet ( unencrypted communication protocols )

* Remove unused packages and applications 

* Use host firewalls , UFW , firewalld 

* Secure service accounts with `nologin` enabled in `/etc/passwd`

## Controlling User Access 

* Password attempts -> Lockout 

* Failed auth -> Lockout 

* User limits 

* Disabling user login

* Security auditing using `find`

## Root account configuration 

* pkexec 

* only use root when absolutely necessary 

* su -> substitute user 

* `su <options> <user-account>`

* `su -` -> loads target users profile. 

* `su -c ` -> temporarily switches to target users account , runs command , and returns to original account 

* `su -c "echo 'hello'" ryan` -> example , runs echo "hello" as user ryan 

* `su -m` -> switches to target user account but preserves existing profile 



* sudo  

* `/etc/sudoers` -> File which configures which users are authorized to run commands. 

## Sudoers file 

* `User_Alias` -> Specifies which user accounts are allowed to run commands  

* `User_Alias <alias> = <user1> <user2>...`

* All alias names in /etc/sudoers must start with a capital letter 

* `Cmnd_Alias` -> Specifies which commands the users can run 

* `Cmnd_Alias KILLPROCS = /bin/kill, /usr/bin/kilall` ->  Syntax , allows sudoers to use kill and killall commands, must specify absolute path. 

* `Host_Alias` -> Specifies the hosts that users can run commands on 

* `Runas_Alias` -> Specifies the usernames that commands can be run as 

* Then once aliases are done , tie them together as shown below 

* `User_Alias Host_Alias = (<user>) Cmnd_Alias` -> Syntax for defining aliases behvior  

* `amos ALL = NOPASSWD: /bin/kill, PASSWD: /usr/bin/lprm` -> Allows the user amos to run kill command without password 

* By default , sudo is configured so users only need to enter their password instead of the root account password ( e.g `su`)

* `sudo -e /etc/sudoers` 

* `sudoedit /etc/sudoers`

* `visudo`

* `EDITOR`, `VISUAL` , `SUDO_EDITOR` -> environment variables for changing editor used with the 3 above commands to edit the sudoers file 


## pkexec 

* `pkexec -user allen cat /etc/hosts ` -> Outputs contents of /etc/hosts file as user "allen". The user running this command will need to enter their account

## Setting strong passwords 

* 12 or more characters 

* Combo of letters , numbers , special characters 

* Words not in the dictionary 

* For password ageing , average time span before changes is 27-30 days , 90 days at max. 

* with 2fa , annual password changes are recommended 

* `chage` -> command to change lifetime of passwords 

* `chage -m` -> minimum days before changes 

* `chage -M` -> maximum days before changes 

* `chage -W` -> warning days before a password change is required 

## PAM 

* Pluggable Authentication Modules 

* Can be used to enforce password requirements such as strength & length , characters.  

* Can also disallow users from logging in from specific terminals 

* `/etc/pam.d` -> directory with configuration files for services 

## PAM Configuration files 

### Auth tasks ( first column ) 

* account -> account verification , access to services 

* auth -> auth user , request password , setup creds.

* password -> request user for replacement password when updating password 

* session -> setup , cleanup of services , resource limits 


### Control keywords ( second column )

* required -> if fails ,entire operation fails after running through all other modules 

* requisite -> if fails , op fails immediately 

* sufficient -> if successful , enough to satisfy reqs of service 

* optional -> op will fail if only module in stack 

* The third column displays module that gets invoked 

---

## PAM Continued 

* `pam_nologin.so` -> if /etc/nologin exists , prevents non-admin users from logging in 

* `pam_selinux.so` -> default security context with selinux 

* `pam_tally2.so` -> login counter for failed login attempts 

* `onerr=fail` -> locks user account after 3 failures with `deny=3`

* `pam_tally2 -u <user> -r` -> resets account to allow login 

### Deny access after 3 failed login attempts through ssh

```bash
auth required pam_tally2.so deny=3 onerr=fail
account required pam_tally2.so 
```

## faillock 

* can track password attempts on screensavers 

* `faillock --user <user> ` -> displays failed login attempts 

* `faillock --user <user> --reset` -> resets user account to allow logins 
* ssh and login need to use `pam_faillock.so` 

## Configuring User limits 

* ulimit , `pam_limits` -> restricts access to resources   

* ulimit is restricted to programs launched from the shell prompt 

* `ulimit <options> <limit>` -> ulimit syntax 

* `ulimit -c ` -> max size of core files in blocks. if 0 , core dumps for the user are disabled 

* `ulimit -f` -> max size (in blocks ) of files created by the shell 

* `ulimit -n` -> limit on max number of open file descriptors 

* `ulimit -t` -> limit on max ammount of CPU time a process may use 

* `ulimit -u` -> limit on max number of processes available to single user 

* `ulimit -a` -> view current value for all resource limits 

### PAM limits module 

* /etc/security/limits.conf -> configures pam module for resource limiting 

* `<domain>  <type>  <item>  <value> ` -> syntax for limits.conf 

* domain -> can be a `user` or `@group_name` , `*` specifies all users 

* type -> defines hard or soft limit , soft limits can be temporarily exceeded 

* item -> resource being limited 

* value -> value for limit 


## Disabling User Login 

* `w` -> shows all user currently logged in 

* `lsof +D <directory>` -> show processes being run on a specific directory 

* `pkill -KILL -u <username>` -> forcefully logoff user 

* `/etc/nologin` -> if this file exists , only root user will be allowed to login. Any text in this file will be displayed when any other user tries to login  

* `printf "System is going down today"` -> print out message to all users on the system to notify them the server will be shutting down. 

* `auth requisite pam_nologin.so` -> checks if /etc/nologin file exists 

## Security Auditing using find 

* Check for files for SUID and SGID set on files owned by root.

* files owned by root with SUID and SGID are security vulnerabilities , very dangerous. Processes created with these files execute with root permissions  

* `rwSr-xr-x` -> files with SUID set 

* `rw-r-Sr-x` -> files with SGID set 

* `systemctl mask ctrl-alt-del.target` -> prevent users from shutting down the server using the famous key combo 

* `find / -type f -perm -u=s -ls ` -> find files on system with SUID 

* `find / -type f -perm -g=s -ls ` -> find files on system with SGID 


## Managing System Logs 

Log files are stored in /var/log 

## /var/log files 

* faillog -> failed auth attempts 

* firewalld -> Firewall log entries 

* kern.log -> Kernel log entries 

* lastlog -> Last login info 

* maillog -> logging messages generated from sendmail and postfix daemons 

* messages -> logs from running processes 

* secure -> ssh login auth , failed ssh logins 

* wtmp -> list of users who have already authenticated.  

## rsyslogd 

* `/etc/rsyslog.conf` -> Config file for syslog 

* init systems use rsyslogd , while other systems may use syslog-ng or syslogd 

### rsyslog.conf syntax 

```bash
<facility>.<priority>   <file>
```

### Facilities 

* authpriv -> used by services associated with system security and authorization 

* cron -> messages from cron and at 

* daemon -> daemons that have do not have their own facility  

* kern -> kernel log messages 

* lpr -> handles messages from printing system 

* mail -> mail logging , postfix , sendmail 

* news -> news daemon 

* rsyslog -> internal messages from rsyslog daemon 

* user -> user-related log messages , failed login attempts 

* uucp -> logging messages from uucp daemon 

* local0-7 -> log messages from user-created application 


### Priorities 

* debug 

* info 

* notice 

* warn

* err

* crit 

* alert 

* emerg 

* none 

## logrotate 

* `/etc/logrotate.conf` -> contains defaults for logrotate utility , such as how often the logs are rotated 

* cron daemon is used by default to rotate logs 

* The defaults in the /etc/logrotate.conf can be overridden by scripts for specific daemons in the /etc/logrotate.d  directory 

* maxage -> defines lifetime of logfiles before they are removed 

* dataext -> configures logfiles to use a data extension 

* rotate -> configures max rotations of logfiles 

* size -> defines max or min size of logfiles 

* notifempty -> Will not be rotated if non-empty 

* missingok -> No error will be generated 

* create -> defines permissons and user/group ownership 

* postrotate -> scripts executed after logs are rotated 

* `logger -p <facility>.<priority> "<log_message>"` -> CLI tool which can be used to manually make entries and test. 

## Journald 

* Used by systemd systems by default 

* journal log entries are lost at reboot 

* `journalctl -b` -> Outputs logs from most recent boot 

* `journalctl -b <int>` -> Outpus logs from a specified boot 

* `journalctl -u <service>` -> Outputs logs from a specified service.  

* `journalctl -f <service>` -> Outputs last few entries from the journal 

* `/etc/systemd/journald.conf` -> Config file for journald. 

* ForwardToSyslog -> forwards logs from journald to rsyslog

* MaxLevelStore -> max log level to be logged in journal , log levels less than or equal to are logged while anything greater is dropped. 

* `/run/log/journal` is a binary file and therefore cannot be read by text manipulation utilities.  


### Make journal logs persistent 

1. `mkdir -p /var/log/journal `

2. `systemd-tmpfiles --create --prefix /var/log/journal`

## Using log files to Detect Intruders 

* `/var/log/wtmp` -> log file of users who have authenticated to the system 

* `last` -> outputs logging of recently authenticated users , sourced from /var/log/wtmp. 

* `/var/log/faillog` -> contains list of failed auth attempts 

* Can view login attempts in /var/log/messages or /var/log/journal 

* `grep login /var/log/messages | more `


## More on security 

* confidentiality , integrity , availability 

* Most systems prefer availability 

* Discretionary Access Control ( DAC ) -> rules set at admins discretion 

* DAC works for most corporate environments 

* Importance on delivering data ASAP 

* Mandatory Access Control ( MAC ) 

* MAC , used for military environments  

* Unclassified -> Confidential -> Secret -> Top Secret. 


## SELinux 

* Made by the NSA 

* Comes with Red Hat 7 , Centos 7 and above by default 

* operations , least priviellege possible 

* actions not explicitly allowed , are denied by default 

* Subject -> User , applications 

* Object -> Socket , file , app 

* Reference Monitor -> Checks rights dependent on context label  

* `ls -Z` -> Command to show selinux contexts on files 

* `ps -Z` -> Command to show selinux contexts on commands  

* `/etc/selinux/config` -> config file for selinux 

* `setenforce` -> Command , sets mode for selinux 
