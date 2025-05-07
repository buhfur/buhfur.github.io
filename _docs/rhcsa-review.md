---

layout: default
title: "RHCSA Review"

---

# Table of Contents 

{: .no_toc }

1. TOC 
{:toc}

# Review notes for RHCSA 

This document mainly contains command snippets of the various CLI tools used for the exam 


## Resetting Root Password 

1. Reboot to grub menu 

2. Add `init=/bin/sh` to the end of the kernel arguments 

3. `mount -o remount,rw /` , to remount the filesystem 

4. `passwd root` 

5. `touch /.autorelabel` , if using selinux 

## Managing disks

### Change label of disk 

`dosfslabel` -> Used for changing labels on dos filesystems 

`e2label` -> used for changing labels on ext[1-4] disks 

`fatlabel` -> set or get label on dos filesystem 



## unrelated helpful snippets  

`echo "password" | sudo -S dnf update` -> pipes password into sudo commandfrom STDIN  

`for x in $(groupmems -l -g groupname); do mkdir /mnt/somedir/$x; done` -> snippet which creates a directory for all users in the "groupname" group 

`for x in /mnt/somedir/*; do chown -R $(basename $x):$(basename $x) $x; done` -> recursively assign ownership for all subdirectories where the subdir is the name of the user. 


`. /usr/share/bash-completion/bash_completion` -> Add this line to your bashrc to enable bash-completion 


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

## swapfiles

### Swapfile as file 

Create a 32G swapfile 

`dd if=/dev/zero of=/swapfile bs=1G count=32` 

`mkswap /swapfile`

`swapon /swapfile`


### Swapfile As Partition 

1. Create partition on disk using fdisk or gdisk 

2. Make type of partition 8200 ( gdisk ) , 19 ( fdisk)

3. Run partprobe if the partition doesn't show up in lsblk or blkid 

4. `mkswap /dev/sdxY` , formats partition as swap 

5. Add disk by PARTLABEL or UUID into fstab 

6. `swapon /dev/sdxY to activate swapfile`



## crontab 

- /var/spool/cron/username -> crontab for user 
- do man 5 crontab for possible combinations 

## chmod 

SUID -> chmod u+s , chmod 4XXX

SGID -> chmod g+s , chmod 2xxx /somedir

Sticky Bit -> chmod +t , chmod 1XXX

## history 

`history -c` -> clears current history 

`history -w` -> remove current history and `.bash_history` file 

`history -d <number> ` -> deletes item from history 

## umask 

F= Files , D=Directories

0 -> F: Read and write , D: everything 

1 -> F: Read and write , D: Read and write  

2 -> F: Read, D: Read and Execute  

3 -> F: Read , D: Read 

4 -> F: Write , D: Execute

5 -> F: Write , D: Write

6 -> F: Nothing , D: Write

7 -> F: Nothing, D: Nothing 


## Find 

`find / -user <user>`

`find / -group <group>`


`find / -perm -u=s ` -> finds all files with SUID permission bit set  

`find / -perm -4000` -> same as above but in octal notation 


`find / -perm -g=s` -> finds all files with SGID perm bit set 

`find / -perm -2000` -> octal notation for above 


`find / -perm -1000` -> finds all files with sticky bit set 

`find / -user <user> -type f -exec cp --parents -a {} /somedir \;` -> copy all files owned by specific user to directory 

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



## gzip & bzip2 & xz 

bunzip2 -> decompresses bzip2 archive  

gunzip -> decompresses gzip archive 


## cat & tac 

cat -> outputs contents from beginning to end of file 

tac -> outputs contents of file from end to beginning 

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

## wc 

wc -> outputs number lines , words , and characters 

`ps aux | wc ` -> outputs total processes are running on the system as each line in the output represents a running process. 


## grep & regex 

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


`lsblk -o +UUID | tail -n1 | awk -F " " '{print $7}'` -> used this snippet to get the UUID of the installation disk 


## sed 

`sed -n 5p /etc/passwd` -> prints fifth line from /etc/passwd file 

`sed -i s/<old-text>/<new-text>/g` -> replaces "old-text" in file with "new-text" , "g" means changes are added to all matching lines 

`sed -i` -> options writes changes to file 

`sed -i -e '2d' file.txt ` -> deletes specific line from file , in this example line 2 

`sed -i -e '2d;5,10d' file.txt` -> deletes lines 2 and 5 through 10 

`ps aux --sort=-%mem` -> sorts the mem column of sort 



## emergency reset 

`echo b > /proc/sysrq-trigger`


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

* /etc/default/useradd -> defaults for users 

* /etc/login.defs  -> defaults for users 

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
#!/bin/bash
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

## firewalld && firewall-cmd 

- `/usr/lib/firewalld/services` -> default location for xml service files for built in services 

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

**basic repo file syntax**

```bash
[label]
name=<string>
baseurl=<url>
metalink=<url>
gpgcheck=<1|0>
```

`dnf config-manager` -> command to create repo client file 


`dnf config-manager --add-repo=file:///repo/BaseOS` -> if the installation disk files have been copied to /repo , use this command to create a client repo file using the BaseOS 


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

## rpm 

`rpm -q --scripts ` -> query for scripts in package 

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

## Resizing Root Logical Volume on Proxmox 

1. After updating size of disk , rescan the disk for the updated space , replace 'sdx' with the name of the block device. 

    `echo 1 > /sys/class/block/sdx/device/rescan`

2. Delete the partition , DON'T format , create new partition with LVM type ( use fdisk , cfdisk , parted)

> Tip: before using gdisk , check to make sure your boot partition is MBR or GPT. gdisk should say at the top after opening it.   

3. Expand the physical volume 

    `pvresize /dev/sdxY`

4. Expand the logical volume using lvextend 

    `lvextend -l +100%FREE /dev/mapper/vgname-lvname`

5. expand the filesystem 

    `resize2fs /dev/mapper/vgname-lvname` 

    `xfs_growfs /`

6. reboot and verify changes have been made 

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


# Podman

- Registries config file location 
    ```bash
    /etc/containers/registries.conf

    ~/.config/containers/registries.conf
    ```

- Run container in detached mode
    ```bash
    podman run -d nginx
    ```

- Run container in TTY mode
    ```bash
    podman run -it nginx /bin/sh
    ```

- View running containers
    ```bash
    podman ps
    ```

- View all inactive and active containers
    ```bash
    podman ps -a
    ```

- Attach to running container
    ```bash
    podman attach <name>
    ```

- Stop running container
    ```bash
    podman stop <name>
    ```

- Search which registries are currently used
    ```bash
    podman info
    ```

- Filter images in search
    ```bash
    podman search --filter official=true alpine

    or 

    podman search --filter stars=5 alpine
    ```

- Pull image
    ```bash
    podman pull <image>
    ```


- Build custom image
    ```bash
    podman build -t imagename:tag -f /path/to/Containerfile
    ```

    - Example  
        ```bash
        podman build -t mymap:1.0
        ```


- Verify custom image was built
    ```bash
    podman images
    ```

- Remove images with None tag
    ```bash
    podman image prune
    ```



- Send SIGTERM signal to the container , 
        ```bash
        podman stop
        ``` 

> Note : If no results after 10 seconds , the SIGKILL signal is sent.

- Kill container
    ```bash
    podman kill
    ``` 

> Note: Immediately sends the SIGKILL command 

- Restart container
    ```bash
    podman restart
    ``` 

- Remove container
    ```bash
    podman rm
    ``` 

> Note: Removes container files written to the writable layer 

- Run container and delete container files automatically 
    ```bash
    podman run --rm
    ``` 

- Running Commands inside containers
    ```bash
    podman exec mycontainer uname -r
    ```

    - TTY MODE
        ```bash
        podman exec -it mycontainer /bin/bash
        ```

- Managing Container Ports
    ```bash
    podman run --name nginxport -d -p <hostport>:<containerport> nginx
    ```

- Bind directory to container for persistent storage 
    ```bash
    podman run -it -v /hostdir:/container/dir:Z ( use ":Z" if container is owned by user running container )
    ```

> Note: bind options must come before image name is passed in order of arguments 

- Configure bind mount to be owned by running user for mounting storage 
    ```bash
    semanage fcontext -a -t container_file_t "hostdir(/.*)?"; restorecon
    ```

- Rename container 
    ```bash
    podman container rename <id> 
    ```


- Generate systemd service for container , then copy to /etc/systemd/system
    ```bash
    podman generate systemd --name containername --files 
    ```

> Note: You must first generate the  ~/.config/systemd/"user" directory before running the command. 


- Run container under specific user 
    ```bash
    podman run --user <uid-or-username
    ```

- Change environment variable of container 
    ```bash
    podman run -e SOME_VARIABLE=something ...
    ```

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

## Autofs Configuration Example 

### /etc/auto.master ( non home exported directory )

```bash
/dirname    /etc/auto.dirname
```

### /etc/auto.dirname  

```bash
subdirname -rw servername:/sharename
```
### Wildcard Mount Setup on Server Machine 

- /etc/exports
    ```bash
    /users *(rw,no_root_squash)
    ```

### Wildcard Mount Setup on Client Machine 

- /etc/auto.master
    ```bash
    /users  /etc/auto.users
    ```

- /etc/auto.users 
    ```bash
    *   -rw     servername:/users/& 
    ```

> Note: the '\*' represents the local mount point on the client machine configured in auto.users, the "&" is a placeholder that gets replaced by the key of the current mount request. 


# SELinux 
---

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

`seinfo -t`: 
    Requires the setools-console package , the command above shows you all selinux context types. 

`semanage`:
    SELinux policy management tool 

`semanage port -a -t ssh_port_t -p tcp <port>`:
    enable non-traditional port for ssh 

`getsebool -a`:
    Get booleans for specific service 

`getsebool -l`:
    Same as previous command , with more info.

`setsebool <type> <on|off>`:
    Syntax for disabling or enabling a boolean. 

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


