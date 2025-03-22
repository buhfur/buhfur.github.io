

# Jellyfin reverse proxy setup with registered domain 

( WORK IN PROGRESS ) 

In this guide, I will detail the steps i've taken to setup my jellyfin LXC container with a reverse proxy using nginx with HTTPS support. 

In the past , i've always used a hosted VM on a proxmox HV , which is behind my Fortigate 61-E. I've configured my firewall to forward the HTTP port to allow external access. The only issue is , most people don't remember my public IP address...

This project originally started from my main goal of moving off other services on a Debian 12 VM , and moving them over to individual Debian 12 LXC containers to keep everything separate, and reduce load on the server as LXC containers are usually less performance intensive as the HV isn't trying to emulate an entire OS while running performance intensive media sharing , torrent downloading , and etc. 


To help my friends and family find my media-server easier , in addition to moving the jellyfin service to a LXC container,  i've also decided to setup a reverse proxy and registered domain so my pals won't have to remember a long public IP. 

Below I will document all the steps i've taken and notes of what i've learned along the way for not only myself , but others to reference this document should they wish to avoid relying purely on a GPT model to do most of the thinking for them ( no offense ). I will leave out the details of registering a domain , however for those that wish to know , I registered my domain with namecheap , which I can recommend as the prices are very reasonable for how short some of the domains can be. 

## Configuring Jellyfin service 

1. Modified /etc/systemd/system/jellyfin.service.d/jellyfin.service.conf

2. Copied /etc/default/jellyfin to /home/jellyfin/

3. chown -R jellyfin:jellyfin /home/jellyfin/jellyfin

4. chmod jellyfin -s /sbin/nologin 

6. firewall-cmd --add-port=8096/tcp --permanent ( temp for debugging )

## Setting up the reverse proxy  

1. Created A record pointing to my public IP. 

2. 

## setting up the certificate 

## Configuring the firewall 
