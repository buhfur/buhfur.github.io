---

layout: default
title: "Android Backup"

---

# Table of Contents 

{: .no_toc }

1. TOC 
{:toc}

---


# What do I want backed up 

- secure vault photos , pictures , 
- everything in gallery
- contacts 
- samsung notes.  


# Project setup 

- FTP server installed on LXC container hosted on proxmox 
- FTP client installed on android phone 
- forwarded port in eero router 
- IPv4 Policy for SFTP in firewall. 


# Project todo 

- [x] install fdroid on android phone 
- [x] setup android FTP client 
- [x] setup LXC container 
- [ ] connect android to sftp server 
- [ ] copy files to SMB share 
- [ ] rclone copy files to cloud share

## SFTP server taks   

### SFTP setup 

- [x] create user for android client on sftp server 
- [x] configure android user to not login
- [ ] restrict permissions to singular directory where files will be copied 
- [ ] only allow android user to read+write to storage location and nowhere else 
- [x] configure ssh port to 5160 
- [x] install firewalld  
- [x] make sure openssh is installed 
- [ ] add ssh to firewall-cmd services 
- [x] generate ssh key for server
- [x] install terminus on android 
- [ ] generate ssh key for android phone 
- [ ] copy ssh key to server

- [ ] copy ssh key to servers authorized_keys 
- [ ] disable password based auth after android public key has been added 
- [ ] add sftp service port to firwall-cmd 
- [ ] start SFTP daemon on LXC container
- [ ] only allow trusted IP from android phone ?
- [ ] add sftp port 5160 to ipv4 policy in fortigate 
- [ ] add sftp port to forwarding in eero router.  

### CIFS setup 
- [ ] copy over fstab from another container
- [ ] install cifs-utils 
- [ ] add cifs to firewall-cmd 

