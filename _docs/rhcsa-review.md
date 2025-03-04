

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
