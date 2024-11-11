
# Docker 

Lately i've been wanting to expand my knowledge around containers. They are commonly used in most applicatons I run on VM's. This creates alot of overhead as having an entire kernel , hardware drivers, and desktop environment is a little overkill in comparison to a container that can run the intended applicaton. An example of this jellyfin.  Because of this , I would like to take this oppertunity to expand my skillset. I have been slowly working through my RHCSA exam prep which uses podman instead. However, the two technologies are very similar. 


# First steps into a containerized world 

So what are containers? Containers provide an isolated environment for applications on a host. In short , containers provide an isolated CGROUP and filesystem for indiviual applicatons. Instead of having an entire kernel , you use a smaller slice of the processing pie so to speak. Each container is isolated from the host and other containers ( without extra configuration , more on that later). 

---

# Getting Started 

Once you install docker, run `ip addr`. You will see a docker0 interface listed. This interface is the default bridge. In docker , you can use the docker bridge to network multiple containers. 

If you run `sudo docker network ls` you can see all the active netowrk connections from docker containers. For clarification the term "driver" just means network type. When containers are added , by default they use the docker bridge as a switch ( kinda ).


# Misc notes 

- Per the docker documentation , if you use firewalld or ufw to manage your firewall, exposed ports on docker containers bypass your firewall rules


---

## Torrent docker swarm 

Lately i've been working on converting all my current services hosted on proxmox into docker containers. The idea for docker containers are the quantity of moving parts. Lately i've began to notice more and more issues with torrenting on my current setup. Currently, i'm running a sonarr, radarr, jackett, deluge, and protonvpn locally on a Debian 12 VM. On another virtual machine , jellyfin and copyparty( file sharing google drive like app ) are hosted. This structure has created a very messy configuration that i'm simply tired of dealing with. Therefore , my plan is to convert all these services into docker containers using docker compose with hopes that this **should** be easier to manage. Starting off with deluge and protonvpn, I need something to download my torrents and make sure that no one is spying on me in the process. Therefore, i've decided to run with the "gluetun" docker container for ProtonVPN as there seems to be active support for tuning it to specific VPN providers.


- Networking the two containers 


j




