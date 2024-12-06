# Tutorial ideas for the youtube channel ( Full Duplex ) 


This document lists out all ideas for tutorials on my channel that I would like to make regarding linux. Not all ideas here are organized by their type , however I wanted to use this document to list ideas as they come to me. In each idea , I will list out the objectives and topics I would like to cover, as well as some smaller ideas for making the tutorial easiser to digest. This includes links and resources to the material I am basing these videos off of. 


# Tutorial: LVM's in linux , A brief introduction

- explain the concept of LVM's and their practical application 
- arch of LVM's ( from RHCSA book )
- mention the different filesystems and their limitations with LVM's
- Mention how XFS filesystems can be increased , but not shrunk , unlike EXT4 which can do both. 
- Apply the conecepts and work through an example using the commands listed : pvcreate , vgcreate, vgs , lvs , lvremove , vgremove etc


# Tutorial : How to create and manage swap partitions on RHEL 

- work through a live example on the hypervisor by creating and managing a swap partition , this tutorial will be very short 


# Tutorial : Automating tasks with Systemd units

- show how to setup a systemd unit to automatically backup /var/syslog

# Tutorial : Setting up rclone on linux 

- work through example on how to setup rclone
- show an example copying backups to your google cloud 
- send files and copy files using the software to a local directory
- demonstrate how to setup a systemd service file that does this automatically for you 


# Tutorial : How to setup ProtonVPN on a headless server

- demonstrate how to use the protonvpn .ovpn files to automate connecting to your VPN connection 


# Tutorial : How to setup Network Manager on Debian 12

- install a fresh debian VM and setup network manager 
- point out how to switch to NetworkManager by deleting the /etc/interfaces file 


# Tutorial : User management in linux 

- review over the topics discussed in the RHCSA book and work through users , groups. 


# Tutorial : Managing File Permissions in Linux 

- cover the material in the RHCSA book and extra tidbits you feel like discussing 

systemd service : 

Run qm backup service and send backups to /tmp 

use rclone to copy files over

delete files in /tmp once finished 
