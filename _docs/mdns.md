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

mDNS( Multicast DNS ) and DNS-SD ( DNS Service Discovery) is used to allow hosts to resolve `.local` hostnames without a local dedicated DNS server. I wanted to learn about this as I wanted a quicker way to access hosts in my LAN without modifying each machines /etc/hosts file. 

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


