---
title: Chrony & Time 
parent: RHEL
grand_parent: Linux
nav_order: 130
layout: default
---


# Chrony 

- `/etc/chrony.conf` -> chronyd config file 
- ensure ntp service is added to firewall 
- disable lines beginning with "pool" if not using internet time servers 

## Config file snippets 

```bash
#Ensure local time server advertises if no internet time servers are available 
local stratum 8 
#Pool is a collection of servers resolved under a DNS domain , server is individual server 
pool time.google.com iburst  #iburst -> sends out burst of packets when attempting to sync   

```

## Sync time manually 

```bash
chronyc -a makestep 
chronyc tracking 

# check at the system level 
timedatectl status
```

> Note:  Make sure that the Last offset is very small , verify correct time server being used 

## Make machine time server 

1. add NTP service to firewall-cmd 

2. uncomment "allow 192.168.0.0" in `/etc/chrony.conf`, replace IP with subnet of choice, chrony blocks all incoming connections by default   

3. restart chronyd 

## Set time manually 

```bash
#Add this line below into your /etc/chrony.conf file 
manual 

#Restart chronyd, then use the command below to set the time , see chronyc manual for various date formats that can be used
settime 2025-10-30 16:10 
```
