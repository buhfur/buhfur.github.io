---
title: RHCSA Review
parent: RHEL
grand_parent: Linux
nav_order: 60
layout: default
---

# Table of Contents 
{: .no_toc }

1. TOC 
{:toc}

---

# RHCSA Objectives 

##**Understand and use essential tools**

- Access a shell prompt and issue commands with correct syntax
- Use input-output redirection (>, >>, |, 2>, etc.)
- Use grep and regular expressions to analyze text
- Access remote systems using SSH
- Log in and switch users in multiuser targets
- Archive, compress, unpack, and uncompress files using tar, gzip, and bzip2
- Create and edit text files
- Create, delete, copy, and move files and directories
- Create hard and soft links
- List, set, and change standard ugo/rwx permissions
- Locate, read, and use system documentation including man, info, and files in /usr/share/doc

##**Manage software**

### Configure access to RPM repositories

> Note: Install dnf-utils package to use "repoquery" tool 
> Note: Install yum-utils package to use "yumdownloader" tool 

```bash
# Repo file syntax 
[label]
name=<string> ( Required )
baseurl=<url> ( Required ) 
metalink=<url>( Optional )
gpgcheck=<1|0>( Optional )
# If gpgcheck=1 , add the line below 
# gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-release-version


## DNF snippets 

dnf history 
dnf list 
dnf search 
dnf provides 
dnf update kernel  # updates the kernel 
dnf whatprovides */vim # lists packages w/ repo names that provides the vim command 

## RPM snipets 

rpm -qp # query package from downloaded RPM file 
rpm -qp --scripts # list helper scripts used to install package , good for vetting 
repoquery # query packages from repositories before they are installed 
yumdownloader # download package from repository to local directory  
rpm -qf $(which dnsmasq) # runs RPM file query on file location of dnsmasq command to get package name that provides the command
rpm -qi <package> # gets detailed info on package 
rpm -qi $(rpm -qf $(which dnsmasq)) # gets info on package that provides the dnsmasq command 
rpm -ql <package>
rpm -ql $(rpm -qf $(which vim)) # lists all files in package that provides the vim command 
rpm -qd <# show file locations of package documentation 
rpm -qc <package> # Get configuration files for package 
```



### Install and remove RPM software packages

```bash
dnf remove/install/search packagename
dnf whatprovides */toolname
```

### Configure access to Flatpak repositories

```bash
sudo flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo

flatpak remotes 
flatpak search <app> 
sudo flatpak install flathub <app> 
```

- Install and remove Flatpak software packages

##**Create simple shell scripts**

- Conditionally execute code (use of: if, test, [], etc.)
- Use Looping constructs (for, etc.) to process file, command line input
- Process script inputs ($1, $2, etc.)
- Processing output of shell commands within a script

##**Operate running systems**

- Boot, reboot, and shut down a system normally
- Boot systems into different targets manually
- Interrupt the boot process in order to gain access to a system
- Identify CPU/memory intensive processes and kill processes

### Adjust process scheduling

```bash
# ==== RENICE ====
# Increase processes priority, larger value -> higher priority  
# Normal Processes: 100-139
# Real Time Processes: 0-99 
renice 99 <pid> 

# ==== NICE ====

# -20 -> high prio 
# 19 -> low prio 

nice -n 10 ./script.sh  # Start script with lower priority 

```

### Manage tuning profiles

```bash
tuned-adm list # view all possible profiles  

tuned-adm recommend # view which profile is recommended for the system  
tuned-adm profile <profile-name> # enable specific profile 
```
- Locate and interpret system log files and journals
- Preserve system journals
- Start, stop, and check the status of network services
- Securely transfer files between systems

**Configure local storage**

- List, create, delete partitions on MBR and GPT disks
- Create and remove physical volumes
- Assign physical volumes to volume groups
- Create and delete logical volumes
- Configure systems to mount file systems at boot by universally unique ID (UUID) or label
- Add new partitions and logical volumes, and swap to a system non-destructively

**Create and configure file systems**

- Create, mount, unmount, and use VFAT, ext4, and xfs file systems
- Mount and unmount network file systems using NFS
- Configure autofs
- Extend existing logical volumes
- Diagnose and correct file permission problems

**Deploy, configure, and maintain systems**

- Schedule tasks using at and cron
- Start and stop services and configure services to start automatically at boot
- Configure systems to boot into a specific target automatically
- Configure time service clients
- Install and update software packages from Red Hat Content Delivery Network, a remote repository, or from the local file system
- Modify the system bootloader

**Manage basic networking**

- Configure IPv4 and IPv6 addresses
- Configure hostname resolution
- Configure network services to start automatically at boot
- Restrict network access using firewalld and firewall-cmd

**Manage users and groups**

- Create, delete, and modify local user accounts
- Change passwords and adjust password aging for local user accounts

```bash
#Set password requirements for 

#/etc/pam.d/system-auth or /etc/pam.d/password-auth 
password    requisite     pam_pwquality.so try_first_pass local_users_only minlen=12

#in /etc/security/pwquality.conf    
minlen = 12
dcredit = -1   # require at least 1 digit
ucredit = -1   # require at least 1 uppercase
lcredit = -1   # require at least 1 lowercase
ocredit = -1   # require at least 1 special char
```

- Create, delete, and modify local groups and group memberships
- Configure superuser access

**Manage security**

- Configure firewall settings using firewall-cmd/firewalld
- Manage default file permissions
- Configure key-based authentication for SSH
- Set enforcing and permissive modes for SELinux
- List and identify SELinux file and process context
- Restore default file contexts
- Manage SELinux port labels
- Use boolean settings to modify system SELinux settings

## Time based studying tasks 

- [ ] Setup token based auth on both test servers in 4 minutes or less 

## Resetting Root Password 

1. Reboot to grub menu 

2. Add `init=/bin/sh` to the end of the kernel arguments 

3. `mount -o remount,rw /` , to remount the filesystem 

4. `passwd root` 

5. `touch /.autorelabel` , if using selinux 


### Why Re-Labeling the filesystem is necessary 

If you fail to relabel the filesystem after changing the root password. The /etc/shadow file will be updated , but in this instance the file is update outside of a selinux aware envionrment. If not relabeled , selinux will see an updated file with a wrong or missing label. As a result of this , assuming SElinux is booted into enforcing mode, PAM or login processes will be denied access to /etc/shadow. Which will need to be readable to allow access to the system.

# Managing disks
---

## Labels

### **EXT2/3/4**

**Change label**
```bash
sudo e2label /dev/your_device_name new_label_name
```

**Get label**
```bash
sudo e2label /dev/sdx0 
```


### **DOS/FAT32/FAT16** 

**Change label**
```bash
sudo fatlabel /dev/your_device_name new_label_name
```

**Get label**
```bash
sudo fatlabel /dev/sdx0 
```

> Note: dosfstools package is required to use dosfslabel

### **XFS**

**Change label**
```bash
sudo xfs_admin -L "NEW_XFS_LABEL" /dev/sdb1
```

**Get label**
```bash
sudo xfs_admin -l /dev/sdx0
```


> Note: dosfslabel and fatlabel are the basically the same tool , only difference is that dosfslabel was packaged by canonical.  


## Get UUID of disk 

```bash
blkid -s UUID -o value /dev/sdb1 
```

---

## unrelated helpful snippets  

`echo "password" | sudo -S dnf update` -> pipes password into sudo commandfrom STDIN  

`for x in $(groupmems -l -g groupname); do mkdir /mnt/somedir/$x; done` -> snippet which creates a directory for all users in the "groupname" group 

`for x in /mnt/somedir/*; do chown -R $(basename $x):$(basename $x) $x; done` -> recursively assign ownership for all subdirectories where the subdir is the name of the user. 

`. /usr/share/bash-completion/bash_completion` -> Add this line to your bashrc to enable bash-completion 

### Read password from user input 

`IFS= read -sr -p "Enter password: " password`

## output redirection & piping 

STDIN:
- `<` or `0<` 
- destination: keyboard 
- File Decriptor: 0

STDOUT:
- `>` or `1>` 
- destination: monitor 
- File Decriptor: 1
    

STDERR:
- `2>`
- destination: monitor 
- File Decriptor: 2

### redirection Examples 

`>> or 1>>` -> redirects STDOUT in append mode 

`2>&1` -> redirects STDERR to same dest as STDOUT

`ls /home/ryan 1> /dev/tty1` -> redirects output to console on server

`ls /home/ryan 2>&1 > /dev/null` -> redirects STDERR to same location as stdout and sends to /dev/null device file. 

`passwd manny --stdin < pass.txt` -> use file as stdin for setting password for user 

`for x in $(tail -n6 /etc/passwd | awk -F ":" '{print $1}'); do echo 'password' | passwd $x --stdin` -> Change password for 6 users 
# swap & swapfiles

## Swapfile as file 

Create a 32G swapfile 

```bash
# Create swapfile the size of 32G 
dd if=/dev/zero of=/swapfile bs=1G count=32
# Creates swap area on file 
mkswap /swapfile
# Adds the newly made swapfile to be used as swap 
swapon /swapfile

```
## Swapfile As Partition 

1. Create partition on disk using fdisk or gdisk 

2. Make type of partition 8200 ( gdisk ) , 19 ( fdisk)

3. Run partprobe if the partition doesn't show up in lsblk or blkid 

4. `mkswap /dev/sdxY` , formats partition as swap 

5. Add disk by PARTLABEL or UUID into fstab 

6. `swapon /dev/sdxY to activate swapfile`

## Troubleshooting 

### Swapfile is full , no available memory 

1. Toggle off/on swap if RAM permits 

2. Expand swap space using a swapfile 

3. long term solutions include reducing swappiness or adding physical RAM

### Reducing swappiness 

**Temporary**

```bash
# Reduces swappiness 
sudo sysctl vm.swappiness=10

# Check current swappiness value 
cat /proc/sys/vm/swappiness
# OR 
sysctl vm.swappiness 
```

**Survives Reboot**

```bash
# Create config file  
vim /etc/sysctl.d/99-swappiness.conf
# Add the following line below to the file 

vm.swappiness = 10
```




## crontab 
- /var/spool/cron/username -> crontab for user 
- do man 5 crontab for possible combinations 
- cronnext -i root -> get time in seconds from now when next cronjob from user will run  


### Get date when next cronjob will run 

`date -d "@$(cronnext -i root | awk -F " " '{print $2}')" ` -> get date when next cronjob for root user will run 

## at 

`atq` -> lists pending jobs 

`echo "echo \'this is easy\!'\ >> /some_dir/file.txt" | at now + 10 minutes`
## chmod 

SUID -> chmod u+s , chmod 4XXX

SGID -> chmod g+s , chmod 2xxx /somedir

Sticky Bit -> chmod +t , chmod 1XXX

## history 

`history -c` -> clears current history 

`history -w` -> remove current history and `.bash_history` file 

`history -d <number> ` -> deletes item from history 

## umask 

> Umask values subtract from the max value for files/directories. Files=666, Directories=777

F= Files , D=Directories

0 -> F: Read and write , D: everything 

1 -> F: Read and write , D: Read and write  

2 -> F: Read, D: Read and Execute  

3 -> F: Read , D: Read 

4 -> F: Write , D: Execute

5 -> F: Write , D: Write

6 -> F: Nothing , D: Write

7 -> F: Nothing, D: Nothing 

**Set default permissions for all newly created files**

In /etc/login.defs as root , change the following line to below.

```bash
umask 007 
```

The change above makes all newly created files with 660 permissions and directories with 770 permissions by default.  

## Find 

`find / -user <user>`

`find / -group <group>`

`find / -perm -u=s ` -> finds all files with SUID permission bit set  

`find / -perm -4000` -> same as above but in octal notation 

`find / -perm -g=s` -> finds all files with SGID perm bit set 

`find / -perm -2000` -> octal notation for above 

`find / -perm -1000` -> finds all files with sticky bit set 

`find / -user <user> -type f -exec cp --parents -a {} /somedir \;` -> copy all files owned by specific user to directory 

`find /usr/share/man -type f -name "*.gz" -exec zgrep 'pattern' {} + ` -> Grep for pattern in files with `*.gz` extension , in this case the man pages.

## Variables 

`$LANG=en_US.UTF-8` -> sets language for system , using UTF-8 encoding 

`env` -> shows environment variables 

## Bash Environment 

`su` -> Runs an **interactive** shell as root.  

`/etc/profile` -> generic file , processed by all users upon login

`/etc/bashrc` -> processed by subshells 

`~/.bash_profile` -> user-specific login shell variables 

`~/.bashrc` -> user-specific file , subshell variables can be defined

> Note: The "su" command **WILL NOT** source a .bashrc in the root directory , according to the documentation of su , only the $SHELL, $HOME, $USER ( if not root user ) , and $LOGNAME are set. To avoid this , use the "--login" option instead of the "-" shortcut. 

## man 

`man -k <keyword` 

`apropos`

`mandb` -> update mandb 

`zgrep -i 'keyword' /usr/share/man/man*/\*.gz` -> grep man pages for keyword , man pages are compressed 

## mount & mount related commands   

df -Th -> Shows available disk space on mounted devices , lists disk type 

findmnt -> shows mounts and relationships , lists mount options 

## ls 

ls -l -> view permissions set on each file 

ls -a -> view all files , including hidden files or "dot" files 

ls -lrt  -> view most recently modified files 

ls -d 

ls -R 

## tar 

`tar -rvf /root/archive.tar /etc/hosts` -> adds /etc/hosts file to the archive 
`tar -tvf archive.tar` -> list contents of archive , shows permissions on each file in archive as well. 

`tar -uvf homes.tar /home/user/` -> updates archive with newer versions of existing files 

`tar -xvf random.tar -C /tmp/` -> Extracts tar archive to temp dir with -C option. 

`tar -xvf random.tar random/file.txt` -> extracts specific file from archive 

`tar -xvf random.tar -C /tmp/ random/file.txt` -> extracts speicifc file to specified directory 

`tar -cvJf /tmp/home.tar /home/user` -> Create xz archive  

`tar -xvJf /tmp/home.tar /home/user` -> Extract xz archive  

`tar -cvjf /tmp/home.tar /home/user` -> Create bzip2 archive  

`tar -xvjf /tmp/home.tar /home/user` -> Extract bzip2 archive  

`tar -czvf /tmp/home.tar /home/user` -> Create gzip archive  

`tar -xzvf /tmp/home.tar /home/user` -> Create gzip archive  

`tar cvzf backup-$(date +%Y-%m-%d).tar.gz ...` -> tar backup with timestamp 

## gzip & bzip2 & xz 

bunzip2 -> decompresses bzip2 archive  

gunzip -> decompresses gzip archive 

## cat & tac 

cat -> outputs contents from beginning to end of file 

tac -> outputs contents of file from end to beginning 

### Write multiple lines of data 

```bash
cat >> multi_line_file.txt << EOF
This is the first line
This is the second line
This is the third line
EOF
```

## head & tail 

head -> shows first 10 lines in file by default 

tail -> shows last 10 lines in file by default 

`tail -<x> file or tail -n<x> file` -> outputs last x lines of file  

`head -<x> file or head -n<x> file` -> outputs last x lines of file  

`tail -f file` -> outputs last lines of file ,updates when new lines are added. 

## cut 

`cut -d :` -> specifies delimeter , in this case it's the ":" character

`cut -d : -f 1` -> "-f" specifies which field to filter out , ":" is used as delimeter. 

`tail /etc/passwd | cut -d : -f 1` -> outputs the last 10 users listed in the /etc/passwd file 

## sort 

sort -> by default sorts content in byte order , aka the order they appear in the ascii text table 

`sort -n` -> sorts in numeric order from least to greatest 

`sort -nr` -> sorts in numeric order from greatest to least 

`sort -k3 -t : /etc/passwd` -> sorts the third column in /etc/passwd in numeric order 

`ps aux | awk 'NR==1{print;next} {print | "sort -k3 -nr"}' | head -n10 ` -> lists top 10 processes with highest CPU usage 

## wc 

wc -> outputs number lines , words , and characters 

`ps aux | wc ` -> outputs total processes are running on the system as each line in the output represents a running process. 

## grep & regex 

`for x in $(ls -a | grep  '^\.' | grep swp); do rm $x; done`

`ls -a | grep  '^\.'` -> list all dotfiles 

`grep -i` -> case insensitive 

`grep -v` -> show only lins that do not contain the regular expression  

`grep -r ` -> searches files in the current directory and subdirectories

`grep -e` -> used for lines matching more than one regular expression 

`grep -A<number>` -> shows "number" of lines matching regular expression  

`grep -B<number> ` -> shows lines before the matching regular expression 

`grep <keyword> <file>` -> grep syntax 

`grep ^anna /etc/passwd` -> shows lines that start with text "anna"

`grep nologin$ /etc/passwd` -> shows all lines that end with nologin

`grep ':*/var' /etc/passwd` -> "\*"  matches zero or more of the previous character.  

`grep -E <expr> <file>` -> tell grep to interpet extended grep expressions 
`grep '^#' /etc/services` -> shows lines that start with "#" in file 

`grep -v '^#' /etc/services` -> shows lines that do not start with "#" 

`<text>$` -> shows lines that end with text before "$" 

`^something` -> Shows lines that start with text after "^" 

`r.t` -> wildcard , searches for one character that matches the single character that matches the pattern 

> Note: put regex expression in quotes if you don't wanna escape characters avoid misinterpretation 

`+` -> indicates character should occur one or more times 

`?` -> indicates characters should occur zero or one times 

`\{2\}` -> matches exactly two of the prevous character 

`\{1,3\}` -> matches a minimum of one and a mx of three of the previous character 

`(..)` -> parentheses are used to group multiple characters in the expression  

## lsblk 

`lsblk -f ` -> view info about filesystems 

`lsblk -o +UUID` -> view UUID of disks 

`lsblk -o +UUID,FSTYPE` -> view UUID and filesystem type of disks 

## awk 

`awk -F : '{ print $4 }' /etc/passwd` -> prints out the 4th column of the /etc/passwd file , uses ":" as a delimeter.  

`awk -F ' /ryan/ { print $3 }' /etc/passwd ` -> Gets the UID of the user "ryan" from the /etc/passwd file 

`ps aux | awk -F" " '{print $2"\t"$4}` -> Prints out the PID and MEM columns from `ps aux` command. 

`ps aux --sort=-%mem` -> sorts the mem column of sort 

`lsblk -o +UUID | tail -n1 | awk -F " " '{print $7}'` -> used this snippet to get the UUID of the installation disk 


## emergency reset 

`echo b > /proc/sysrq-trigger`

## Process Management 

Start process with lower priority
```bash
nice -n 10 ./script.sh 
```

Increase process priority  
```bash
renice +5 `pgrep script.sh`
```

Kill process 
```bash
```

## User and Group management 

chmod -> change permissions for directories and files  

chgrp -> change group owner of file  

chage -> change password age of users password 

chown -> change user and group ownership of files   

id -> get UID, GID , and groups of users via their username 

groupmod -> change GID of group 

groupmems -> view users apart of a specific group 

`groupmems -g dudes -l` -> view members of a specific group  

`id -u <username>`  -> just UID 

`id -u <username>`  -> just GID 
- /etc/default/useradd -> defaults for users 

- /etc/security/pwquality.conf -> file for password settings 

- /etc/pam.d/system-auth && /etc/pam.d/password-auth  -> PAM password settings 

### Script to verify if group exists in bash 

```bash
# !/bin/bash

GROUP_NAME="mygroup"

if getent group "$GROUP_NAME" > /dev/null 2>&1; then
    echo "Group '$GROUP_NAME' exists."
else
    echo "Group '$GROUP_NAME' does not exist."
fi
```
## usermod 

`usermod -aG wheel <user>` -> adds user to wheel group to make them admin privs 

## useradd 

`useradd -m -u 1201 -G sales,ops linda` -> creates user with home dir and UID of 1201 , adds user to groups sales & ops 

`useradd -aG <group-name> [user1..userx]` -> syntax for adding one or multiple users to a group  

## sudo file 

`<username> ALL=/usr/sbin/useradd, /usr/sbin/passwd` -> give user the ability to execute useradd and passwd commands without requiring root privs. User still needs to prepend 'sudo' before running command   

`Defaults timestamp_timeout=240` -> sets sudo token timeout  

`ALL=/usr/bin/useradd, /usr/bin/passwd, ! /usr/bin/passwd root` -> prevents users granted passwd execution from changing the password of the root user 

`sudo sh -c "<command> | <command> ` -> syntax for piping sudo commands 

`pkexec visudo` -> allows changing the visudo file if access is unavailable 

`vipw` -> allows changing config files for users directly in /etc/passwd and /etc/shadow 

`vigr` -> same as vipw but for changing configs for groups 

`Cmnd_Alias MESSAGES = /bin/tail -f /var/log/messages` -> sudo command alias that runs tail on /var/log/messages 
- Command aliases allow you to let selected users / groups to launch specific commands without prompting for a password 

`ryan ALL=(ALL) NOPASSWD: MESSAGES` -> Enables the use of the command alias made earlier to be executed without a password 

### Rule syntax 

```bash
# ---
root ALL=(ALL:ALL) ALL
# ---
root         → the user this rule applies to  
ALL          → from any host  
(ALL:ALL)    → may run commands as any user (1st ALL) and any group (2nd ALL)  
ALL          → may run **all commands**
```
## chage & passwd  

`passwd -n 30 -w 3 -x 90 linda ` -> sets minimal password usage to 30 days , with 3 days of warning , expires after 90 days 

`chage -m 30 -W 3 -M 90 linda ` -> same thing but using chage instead of apasswd  

`chage -E 2025-12-31 bob` -> sets bobs account to expire at a certain date 
`chage -l` -> view current password management settings  

### Change users shell and only let them change password 

1. Create a script in /usr/local/bin/change-pass.sh

2. put this line in the script "passwd <username> "

3. then change the users shell using the tool of your preference 

```bash
# !/bin/bash
/usr/bin/passwd
exit
```

4. User will only be able to change their own password 
## Special permissions basics 

SUID -> on files , files are executed with the permissions of of the file owner 

SGID -> on files , files are executed with permissions of group owner 
on directories , newly created files are assigned group owner 

Sticky bit -> prevents users from deleting files from other users

> Tip: Don't use sticky bit on files, it's ignored. Use on directories.

## chmod 

4 -> read 

2 -> write 

1 -> execute 

s -> on directories , SGID and execute are set , on files , SUID and execute are set 

S -> on directories,only SGID is set  , on files , only SUID is set 

t -> sticky bit and execute are set 

T -> only sticky bit is set 

`chmod 755 file.txt` -> user has full rwx , group has rx , others has rx 

`chmod -R a+X /home/somedir` -> sets execute perm for all users and sub dirs contained within 

`chmod 4755 /somedir` -> set SUID on dir 

`chmod u+s /somedir` 

`chmod 2755 /somdir` -> set SGID on dir  

`chmod g+s /somedir` 

`chmod 1755 /somedir ` -> set sticky 

`chmod +t /somedir`

## groupmems 

`groupmems -l -g workers` -> lists users in workers group 

## umask 

> Subtract perm values 

Umask changes default permissions for all users 

`umask 777 somedir ` -> defaults for directories 

`umask 666 somefile ` -> defaults for files 

/etc/profile.d/umask.sh -> file for setting default umask permissions for all users that login to the server , root user created 

You could also change the default umask by changing /etc/profile 

For individual users you could also add a .profile file in the /etc/skel directory and change the umask there 

## User-extended attributes 

> Package: e2fsprogs 

A -> file access time , makes sure it's not modified 

a -> allows file to be added but not removed, can even prevent root user from deleting file 

c -> if volume-level compression is supported, assures files are compressed when the compression engine starts  

D -> changes to files are written to disk immediately and not cached first, important for databases  

d -> makes sure the file is not backed up when the dump utility is used 

I -> enables indexing for the directory where it's enabled 

i -> makes file immutable , good for security , also can't be changed by root user 

s -> overwrites blocks where file was stored with 0's after file is deleted , makes sure recovery of the file is not possible after deletion. 

u -> saves undelete info , allows a utility that can work with that info to salvage deleted files  

`chattr +s somefile` -> adds s attribute to file 

`chattr -s somefile` -> removes s attribute from file 

`lsattr <somefile>` -> view attributes of file or all files in current directory  

## classed networks   

10.0.0.0/8 -> a single A class network 

172.16.0.0/12 -> 16 class B networks 

192.168.0.0/16 -> 256 Class C networks 

## nmcli

> Tip: make sure the bash-completion package is installed and the lines mentioned in "unrelated helpful snippets" is put into your bashrc 

> Tip: man nmcli-examples 

> Tip: use nm-connection-editor

> Configs for network cards : /etc/NetworkManager/system-connections

`nmcli` -> basic network interface overview 

`nmcli general permissions` -> view gen perms 

`nmcli con show` -> show specific connections  

**Add ipv4 address and gateway to existing connection**

`nmcli con mod <connection-name> ipv4.addresses <ip-address>/<prefix> ipv4.gateway <gateway-ip> ipv4.method manual`

`nmcli con down <con-name> && nmcli con up <con-name>`

**Create ipv4 connection using DHCP** 

`nmcli con add con-name <name> type <type> ifname <ifname> ipv4.method auto` -> creates a connection using the ipv4.method which uses DHCP

**Create static ipv4 connection with gateway** 

`nmcli con add con-name <name> type <type> ifname <ifname> autoconnect no type ethernet ip4 <ip>/<cidr> gw4 <gw-ip> ipv4.method manual` -> creates a new network connection with static IP and gateway 

**Configure DNS to not use DHCP**

`nmcli con mod <con-name> ipv4.ignore-auto-dns yes`

**Add dns IP to existing connection**

`nmcli con mod <con-name> +ipv4.dns <ip>`

**Make changes persist after reboot**

Change the config in either /etc/sysconfig/network-scripts/ or /etc/NetworkManager/system-connections 

## hostname 

`hostnamectl set-hostname <hostname>` -> changes hostname 

`hostnamectl` -> shows basic info about machine , including hostname and kernel version 

## ip 

`ip -s link show ` -> show links and packet transmissions 

`ip addr add 10.0.0.10/24 dev <devname>` -> add new ip to interface 

`ip route add default via <ip> dev <interface>` -> add default route using ip tool

## ss 

`ss -lt` -> listening TCP sockets 

`ss -tul` -> listening TCP and UDP ports 

## dnf 
---

- Only required fields when creating repo server are the name, baseurl , 

### Repo file Syntax 
---

```bash
[label]
name=<string> ( Required )
baseurl=<url> ( Required ) 
metalink=<url>( Optional )
gpgcheck=<1|0>( Optional )
# If gpgcheck=1 , add the line below 
# gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-release-version
```

### Steps to Create Repo with Mounted Installation Media
---

1. Setup directory to mount ROM 
    `mkdir /repo`

2. Mount ROM onto newly created Directory 
    `mount /dev/sr0`

3. Generate repo file using newly created directory as source
    `dnf config-manager --add-repo=file:///repo/BaseOS`

    After doing this , change the [label] to match the repo name. Then add gpgcheck=0 if not using signed packages.
    
> Note: The directory name might be different , on the AlmaLinux minimal installation , it's named "Minimal" , look out for the directory in the installation media that contains the "Packages" and "repodata" directories

4. Search repos to validate it's working 
    ```
    dnf search httpd --repo BaseOS
    dnf search httpd --repo AppStream
    ```

5. ( Optional ) Further Steps to create a Repo server

6. Install httpd and createrepo\_c 
    `dnf install -y httpd createrepo_c`

7. Create HTML directory for repositories 
    `mkdir /var/www/html/repo`

8. Copy repos to the html directory 
    ```
    cp -r -v /repo/BaseOS /var/www/html/repo
    cp -r -v /repo/AppStream /var/www/html/repo
    ```

### DNF Snippets 
---

`dnf history rollback <id>` -> rollback all changes from the ID. ( fresh re install ) 

`dnf config-manager` -> command to create repo client file 

`dnf repoquery -l packagename` -> lists all files included in the repo 

`dnf repoquery -l packagename | grep bin/` -> lists all binaries installed by repo 

`dnf config-manager --add-repo=file:///repo/BaseOS` -> if the installation disk files have been copied to /repo , use this command to create a client repo file using the BaseOS 

`dnf repolist all` -> lists all enabled/disabled repos being used on the server

`dnf repolist` -> only lists active repos being used on the server 

`dnf info <package>`

`dnf group list` -> list diff packages groups which are bundles of packages you can install at the same time 

`dnf list installed` -> lists all installed packages 

`dnf clean all` -> remove stored meta data 

`dnf provides <name>` -> use to search what packages supply a specific file 

`dnf list kernel` -> get kernel version installed and show versions of the kernel that can be installed. 

`dnf update kernel` -> does not directly update the kernel but installs new versions which can be selected during boot.

`dnf groupinstall <package-group-name>` -> install package group 

`dnf history` -> shows recent history of package installs 

`dnf history undo <number>` -> undo's a recent installation in the dnf history 

`dnf module list` -> list modules 

`dnf module info <module-name> ` -> get info about specific module , like dependencies and versioning. 

`/etc/pki/rpm-gpg/RPM-GPG-KEY-DISTRONAMEHERE-VERSION` -> Location for gpg key , this is used when a repo has 'gpgcheck=1' , you must also add this path to the repo file 

## RPM 

`rpm -q --scripts ` -> query for scripts in package 

`rpm -ql packagename` -> list all files installed by package 

`rpm -ql packagename | grep bin/` -> list all binaries installed by package 

`rpm -qp <pkg>` -> query RPM files instead of the RPM database 

`rpm -qR` -> show dependencies for a specific package 

`rpm -V ` -> shows which parts of a package have been changed since installation 

`rpm -Va` -> verifies all installed packages and shows which parts of each package have changed since installation.   

`rpm -qa` -> list all packages that are installed on the server. 

`yumdownloader` -> download package to local directory  

`rpm -qp --scripts httpd-2.4.6-19.el7.centosx86_64.rpm` -> queries RPM package for scripts.  

> Packages: dnf-utils for repoquery tool

`repoquery <repo-or-package>` -> scans repo or package before installing it. 

## ps 

`ps -ef ` -> show command used to start process  

`ps fax` -> show  parent child relationships between processes 

`echo 0 > /sys/bus/cpu/devices/cpu1/online` -> shut down CPU core

`systemctl set-property system.slice CPUWeight=800` -> sets CPUWeight of all processes in the system slice to be 8x higher

`ps aux | grep defunct` -> checks for zombie processes 

## tuned 

`systemctl enable --now tuned` -> enables tuned daemon 

`tuned-adm list` -> view all possible profiles  

`tuned-adm recommend` -> view which profile is recommended for the system  
`tuned-adm profile <profile-name>` -> enable specific profile 

## systemctl

`systemctl --failed -t service` -> lists failed services 

`systemctl set-default name.target` -> Configures default target when system boots 

`systemctl list-units --type target` -> list all target units 

`systemctl list-units --state=masked` -> list all masked units 

`systemd-resolve --flush-caches` -> flush DNS using systemd-resolved

`sudo resolvectl flush-caches` -> flush DNS 

## Journalctl 

`journalctl -f` -> Live view of logs 

`journalctl -n 20` -> last 20 lines in journal  

`journalctl --since yesterday -p err ` -> get error logs from the day before up to now 

`journalctl -o verbose` -> verbose logs from journal

`journalcttl _SYSTEMD_UNIT=sshd.service -o verbose` -> get verbose logging from specified systemd unit. 

`journalctl -b` -> show logs from last boot

`journalctl -u sshd` -> see logs for specific unit 
- journal is stored in /run/log/journal, cleared when system reboots 
- to   make journal persistent through reboots , create /var/log/journal directory 
- /etc/systemd/journald.conf -> config file for journal 

---

# LVM Snippets  

## pvcreate 

`pvcreate /dev/sdxY` -> mark partition as physical volume 

## lvcreate 

`lvcreate -n <lvname> -l <size> <vgname> ` -> create logical volume with absolute size 

## vgcreate 

`vgcreate <name> /dev/sdxY` -> create volume group on LVM partition 

## vgextend 

> Tip: this is used to add another physical volume to a volume group 

`vgextend myvg /dev/sdXY`

`vgextend myvg /dev/sdXY /dev/sdZY` -> adds multiple physical volumes to the volume group 

# Volume naming scheme 

Volumes are named by the device mapper and can be referenced two ways shown below 

`/dev/mapper/<vgname>-<lvname>`

or 

`/dev/<vgname>/<lvname>`

# Steps to create a LVM 
---

1. Create a LVM partition on the block device with type "8e00" if using gdisk , and "8E" if using fdisk , or in fdisk just type "LVM"

2. `pvcreate /dev/sdxY` to Mark partition as physical volume.

3. Run `pvs` command to verify physical volume was created. 

4. `vgcreate <name> /dev/sdxY` to Create and assign physical volume to volume group using command.

5. (optional) `vgcreate <name> /dev/sdxY -s 8M` to change the physical extent size of the VG. 

6. Create the logical volume with an absolute size or relative size using commands below 
    1. `lvcreate -n <lvname> -L 100M <vgname> ` to create a volume with an absolute size
    2. `lvcreate -n <lvname> -l 100 <vgname> ` to create a volume and specify the amount of extents 
    3. `lvcreate -n <lvname> -l 50%FREE <vgname> ` to create a volume with an relative size

7. After creating the logical volume, use `mkfs` to create a filesystem on top of it , using the naming scheme of the logical volume `/dev/<vgname>/<lvname>`

# Resizing LVM's  
---

> Tip: unmount volumes that are being shrunk 

## Resizing Physical Volumes 

`pvresize /dev/sdX` -> resize physical volume 

> Tip: Make sure to expand the filesystem using the logical volume 

## Resizing Logical Volumes 

`lvextend -L +10G /dev/vgname-lvname` -> extend size of logical volume by 10G. 

`lvextend -l +100%FREE /dev/vgname-lvname` -> extend size of logical volume with all remaining freespace.

`lvreduce -L 10G /dev/vgname/lvname` -> Shrink logical volume. 

## Resizing Root Logical Volume 


### If using Proxmox 

1. After updating size of disk , rescan the disk for the updated space , replace 'sdx' with the name of the block device. 

    `echo 1 > /sys/class/block/sdx/device/rescan`

### Normal resizing 


1. Delete the partition , DON'T format , create new partition with LVM type ( use fdisk , cfdisk , parted)

> Tip: before using gdisk , check to make sure your boot partition is MBR or GPT. gdisk should say at the top after opening it.   

2. Expand the physical volume 

    `pvresize /dev/sdxY`

3. Expand the logical volume using lvextend 

    `lvextend -l +100%FREE /dev/mapper/vgname-lvname`

4. expand the filesystem 

    `resize2fs /dev/mapper/vgname-lvname` 

    `xfs_growfs /`

5. reboot and verify changes have been made 

## Resizing Filesystems 

### Ext4 

`resize2fs /dev/mapper/vgname-lvname` -> Grow ext4 filesystem 

`sudo e2fsck -f /dev/vgname/lvname` -> Check ext4 filesystem

`sudo resize2fs /dev/vgname/lvname 10G` -> Shrink filesystem 

### Xfs 

`xfs_growfs /mount/point`

### Brief explanation of LVM's 

Physical volumes are broken into chunks , these chunks are called *physical extents* , these physical extents map one to one to *logical extents*. The size determines how big these chunks are. A logical volume size is always a size that is a multiple of the physical extent size. Therefore if you require bigger logical volumes , it is more efficient to create larger physical extent size since the LVM would require less physical extents to store larger data, thus leading to quicker seek times( how long it takes the LVM to locate the relevant extents needed to store the data )

---

# NFS 

## Summary 
- download nfs-utils package 
- default version is version 4 
- mount option 'nfsvers'
- use /etc/exports for defining exported directories 
- start nfs-server service 
- add nfs, rpc-bind, mountd services to firewall-cmd, should be builtin
- in nfs version 4 you can use root mounts to mount the root directory of the server and only see shares you have access to 

## /etc/exports example

```bash
/nfsdata *(rw,no_root_squash)
/users *(rw,no_root_squash)
```

`no_root_squash` -> maps root UID "0" to nobody or anonymous UID , this option disables that feature, therefore the root user on the client would have root privilleges on files in the shares. Be very careful with this.  

## Get list of exported directories from NFS server  

`showmount -e nfsserver` -> show shares that are available  

## Mounting NFS share through fstab 

```bash
servername:/sharename /nfs/mount/point nfs sync 0 0 
```

## autofs summary 
- no root perms required 
- setup on both client and server 
- wildcard mounts supported 
- define mount points and secondary file for configuration in /etc/auto.master 
- in secondary file , you specify name of subdir to be created , mount opts , and servername:share
- use wildcard mounts for mounting home directories 

- **IF SERVER/CLIENT ARE THE SAME MACHINE** Use "nobind" as a mount option in secondary config file to prevent the system from binding the share as a mount on the underlying filesystem 

## Autofs Configuration Example 

### /etc/auto.master ( non home exported directory )

```bash
# /dirname -> directory on local host where share will be mounted 
# /etc/auto.dirname -> name of secondary configuration file 
/dirname    /etc/auto.dirname
```

### /etc/auto.dirname  

```bash
# SUBDIR NAME -OPTIONS REMOTEHOST:/REMOTE_PATH
subdirname -rw servername:/sharename
```
### Wildcard Mount Setup on Server Machine 

**/etc/exports**

```bash
# /users -> directory to be shared or "exported" , options follow after
# no_root_squash allows root user on client to have root privilleges on files in share directory
/users *(rw,no_root_squash)
```

### Wildcard Mount Setup on Client Machine 
**/etc/auto.master**

```bash
/users  /etc/auto.users
```

**/etc/auto.users**

```bash
# "*" -> wildcard , used to match any name of a directory on the local machine attempting to be accessed 
# "&" -> wildcard used to match the name of the matching item on the remote server  
 *  -rw     servername:/users/& 
```

> Note: the '\*' represents the name of the subdir that will be created on the local machine, you can use the asterisk wildcard if you do not want a subdirectory created. The "&" is a placeholder that gets replaced by the key of the current mount request. Which can be the username of a local user if you're using an NFS share to mount user home directories   



### Tips 

- use findmnt to verify how the share is mounted 

- showmount -e with nfs-server for finding which directories are being exported 

# SELinux 
---

**Use this new page for more up to date info on SELinux snippets**

![SELinux snippets]('./selinux-snippets.md')

## Objectives 

- List and identify SELinux file and process context
- Restore default file contexts
- Manage SELinux port labels
- Use boolean settings to modify system SELinux settings

## Terminology 

**Source Domain:** In a SELinux Policy , represents the object trying to access something. For example a process( PID ) or user( UID ).

**Target Domain:** Object that the source domain is attempting to access. Can represent a file , directory , or network port. 

**Context:** Security label that is used to categorize objects in SELinux.  

**Labels( Context Labels ):** Used to define which source domain has access to which target domain.

**Rule:** Part of a policy that determines which source domain has which access permissions to which target domain.  

## Notes 

By default , all system calls are denied when SELinux is enabled. To allow a system call through , SELinux policies are used. Each policy defines which source domains are allowed to access which target domains.

Up to RHEL 8 , SELINUX=disabled would also disable selinux at boot. You can also use grubby to persistently set the bootloader to boot with selinux=0. You can also add selinux=0 at the end of the kernel command line arguments to disable selinux at boot

For the exam , you will be expected to know how to work with context types, working with roles and users is not a requirement for the exam.  

### SELinux Modes

Enforcing Mode: 
    SELinux is fully operational and enforcing all SELinux rules in the policy.
 
Permissive Mode: 
    All SELinux activity is logged, however no access is blocked. Best used for troubleshooting , however your system will be insecure temporarily.   


### SELinux types 

Targeted: 
    Targeted processes are protected 

Minimum: 
    Modification of targeted policy. Only selected processes are protected.

Mls:
    Multi Level Security Protection.  

## General Commands / Snippets 

> Note: seinfo is installed with the setools-console package 

> Note: policycoreutils is mandatory for managing SELinux at all from the command line.

`seinfo -u`:
    Lists all available users on the system 

`seinfo -r`:
    Lists all available roles on the system 

`seinfo -t`: 
    Lists all available types on the system 

`seinfo -b`
    Lists all available SELinux booleans on the system 
    

`semanage`:
    SELinux policy management tool
    Install setroubleshoot package through DNF to acquire this tool 

`semanage port -l`:
    Lists all SELinux ports 

`semanage port -a -t ssh_port_t -p tcp <port>`:
    enable non-traditional port for ssh 

`getsebool -a`:
    Get booleans for specific service 

`getsebool -l`:
    Same as previous command , with more info.

`setsebool <type> <on|off>`:
    Syntax for disabling or enabling a boolean. 

`setsebool <boolean_name> <on(1) | off(0)> -P` -> write boolean changes to disk to make persistent across reboots.

`ls -Z`:
    view selinux context label on object. 

`ps Zaux`: 
    List processes and their context labels 

`ss -Ztul`:
    List ports and their context labels 
`user:role:type:level`:
    full security context label , or context. "User" is the SELinux user ( not linux user , They're different ). "Role" is the role the selinux user can transition to. "Type" is the SELinux type , which is used for access control decisions. "Level" is the optional field used for an MLS label. 

`/var/log/audit/audit.log`:
    Location where SELinux logs are stored. 

`/etc/sysconfig/selinux`:
    File used to change default selinux mode when booting.

`grubby --update-kernel ALL --args selinux=0`:
    disables SELinux post RHEL 8 at boot

`grubby --update-kernel ALL --remove-args selinux`:
    reverts change from previous command.

`selinux=0`:
    Kernel argument for disabling selinux at boot.

`enforcing=0`:
    Kernel argument for setting permissive mode at boot.

`getenforce`:
    View current mode of SELinux if enabled 

`setenforce 0`:
    Temporarily puts SELinux in permissive mode. 

`setenforce 1`:
    Temporarily puts SELinux in enforcing mode. 

`sestatus -v`:
    If used with -v , this command shows detailed info about the current status of SELinux. Shows the current version of the policy and the context labels for some critical parts of the system.

`sepolicy generate`:
    Tool used to allow almost any application to run in a SELinux enabled environment. Note , you must install an additional package to support this , past RHEL 7 , you can install the "policycoreutils-python-utils" package.

## Add Context type to directory 

### Make directory used for storing HTML files for Web Server 

`semanage fcontext -a -t httpd_sys_content_t "/mydir/(/.*)?"`:
    Adds httpd_sys_content_t context type to directory. the **-a** is used to add a context type , **-t** is used to change the context type. Once this is done , the changes will only be written to the policy and not the filesystem. To finalize this change , you will need to run the restorecon command below 

`restorecon -R -v /mydir`: 
    Follow up from the previous command adding the httpd_sys_content_t context type to the specified directory, to finalize the changes and write this change to the filesystem , use the command above to do so.

### Alternate home directory under root directory 

```bash
semanage fcontext -a -t home_root_t "/newhome"
semanage fcontext -a -e /home /newhome/home
restorecon -R -v /newhome
```

### Change port used for Web Server ( Apache )

```bash
semanage port -a -t http_port_t -p tcp 8008 
```

> Tip: When changing ports,  you do not have to run the restorecon command. The changes will be effective immediately. However , you still should restart the service whose port you are changing to make sure it still works.    

