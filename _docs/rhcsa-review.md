

# Review notes for RHCSA 

This document mainly contains command snippets of the various CLI tools used for the exam 



## unsorted 

vigr -> opens /etc/group config file 

type -> gets what type the command is , like an alias 

which -> get path of command being used 

time -> gets system time used for command 

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





