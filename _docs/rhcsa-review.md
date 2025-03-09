

# Review notes for RHCSA 

This document mainly contains command snippets of the various CLI tools used for the exam 



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


## Variables 

`$LANG=en_US.UTF-8` -> sets language for system , using UTF-8 encoding 

`env` -> shows environment variables 

## Bash environment 

`/etc/profile` -> generic file , processed by all users upon login

`/etc/bashrc` -> processed by subshells 

`~/.bash_profile` -> user-specific login shell variables 

`~/.bashrc` -> user-specific file , subshell variables can be defined

## man 

`man -k <keyword` 

`apropos`

`mandb` -> updated mandb 


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


## awk 

`awk -F : '{ print $4 }' /etc/passwd` -> prints out the 4th column of the /etc/passwd file , uses ":" as a delimeter.  

`awk -F ' /ryan/ { print $3 }' /etc/passwd ` -> Gets the UID of the user "ryan" from the /etc/passwd file 

`ps aux | awk -F" " '{print $2"\t"$4}` -> Prints out the PID and MEM columns from `ps aux` command. 


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


## Special permissions basics 

SUID -> on files , files are executed with the permissions of of the file owner 

SGID -> on files , files are executed with permissions of group owner 
on directories , newly created files are assigned group owner 

Sticky bit -> prevents users from deleting files from other users 

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


## ss 

`ss -lt` -> listening TCP sockets 

`ss -tul` -> listening TCP and UDP ports 


