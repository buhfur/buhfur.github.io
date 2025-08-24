---

layout: default
title: "TODO list"

---

# Table of Contents  
{: .no_toc }

1. TOC  
{:toc}

---

## Server TODO 

- [ ] disable power button at home , either disconnect or something in bios  

## Studying TODO 

- [ ] Create busted VM snapshots of test VM's 
    - [ ] Selinux damage
    - [ ] File system damage 
    - [ ] SSH damage 
    - [ ] Stop root user from making tar archives somehow 
    - [ ] Ways to stop user from connecting to repo server  
    - [ ] Something to increase memory load 
        - [ ] create systemd unit to run command `yes > /dev/null`
    - [ ] change file permissions on default ssh keys directory /etc/ssh


- [ ] document all changes into the wiki 
- [ ] ways to break SELinux that fall in line with exam objectives ? 
- [x] generate script to change selinux context types on random files 
    - [x] use script 
- [ ] configure firewalld to send logging to rsyslog file 

- [ ] learn how to review and troubleshoot firewalld and selinux.  



## Server TODO
 
- [ ] point free-shit.pro to the media server again 
- [ ] forward port to jellyfin nginx proxy in router 


## SDDM theme todo

- [ ] add install script to sddm theme main branch 

## buhfur-pc todo 

- [x] make vim the default editor when running sudo ( for example when opening ranger as sudo )
- [x] remove openrgb 
- [ ] setup windows 10 vm in virtualbox 
    - [ ] install thermaltake plus 
    - [ ] turn off fan leds 

## wiki todo 

- [x] add snippets and generate document for info about firewalld
- [x] add section in firewalld on how to stop console spam from log denied packets 
- [ ] add section for how to read logging for firewalld when things go wrong 
