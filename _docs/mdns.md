---

layout: default
title: "Setting up mDNS and DNS-SD in my lab environment"

---

# Table of Contents 

{: .no_toc }

1. TOC 
{:toc}

---

# mDNS and DNS-SD 

What is mDNS and DNS-SD ? I've pasted the following paragraph below from wikipedia which has a nice description.

> DNS-SD (DNS Service Discovery[16]) allows clients to discover a named list of service instances and 
> to resolve those services to hostnames using standard DNS queries. The specification is compatible with 
> existing unicast DNS server and client software, 
> but works equally well with mDNS in a zero-configuration environment. 
> Each service instance is described using a DNS SRV[17] and DNS TXT[18] record. 
> A client discovers the list of available instances for a given service type by querying the DNS PTR[18] 
> record of that service type's name; the server returns zero or more names of the form <Service>.<Domain>, 
> each corresponding to a SRV/TXT record pair. The SRV record resolves to the domain name providing the 
> instance, while the TXT can contain service-specific configuration parameters. A client can then resolve the
> A/AAAA record for the domain name and connect to the service.


I wanted to learn about this as I wanted a quicker way to access hosts in my LAN without modifying each machines /etc/hosts file. and mDNS coupled with DNS-SD will allow me to do this with the small limited hosts in my network and give me the oppertunity to play around with some network stuff in between studying for my RHCSA. 


## Setup 

In my lab , here are the steps i've taken to get this up and running across all my RHEl and Debian hosts. For the first part of this setup , I decided that I wanted to also install everything on all hosts using ansible. After modifying the ansible config and enabling token-based auth on all hosts. I ran into an issue with the machines I use for my RHCSA lab work. 

In this lab I have 3 machines , one is a dedicated repository server. After searching I found that one of the two packages I need to install is `nss-mdns` hosted in the epel repo. Therefore, my first task is to host this repo on my repo server and install the package on all machines.

## Adding EPEL repo to repo-server

### Get files for EPEL repo

1. Installed epel-release on the repo-server
```bash
sudo dnf install -y \
  https://dl.fedoraproject.org/pub/epel/epel-release-latest-9.noarch.rpm
```

2. Used reposync to get repo files 
```bash
sudo reposync --repoid=epel --download-path=/var/www/html/repo/epel --download-metadata
```


### Host repo 


### Copy client repo file to all RHEL hosts

### Install nss-mdns on all RHEL hosts

> Note: After doing this , all machines should install the package nss


Install avahi and nss-mdns for RHEL 
```
sudo dnf install avahi avahi-tools nss-mdns 
```


