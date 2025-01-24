
# Linux + study guide 

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
