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

To accomplish this , I created the EPEL repo file and used ansible with a script to add the repo file to every host 

First I created the Client repo file and copied it to a newly made directory /var/www/html/repofiles
```bash
[epel]
name=EPEL RHEL 9 self hosted repo
baseurl=http://192.168.3.180/repo/epel
gpgcheck=0
```


### Copy repo file to all hosts 

Created this small script download-repo.sh which requests the file from the web server and pipes the output into the /etc/yum.repos.d/ directory under the name "epel.repo" 
```bash
#!/bin/bash
# download-repo.sh
curl http://192.168.3.180/repofiles/epel.repo > /etc/yum.repos.d/epel.repo
```

On the ansible master host, I used this ansible command to execute it across all hosts in my lab 
```bash
ansible rhel-hosts -m script -a /root/download-repo.sh -b
```

Then executed another ansible command to download the 'nss-mdns' package! 
```bash
ansible rhel-hosts -m shell -a 'sudo dnf install -y nss-mdns' -b 
```

Checked all hosts now have the installed package ( with ansible of course )
```bash
root@ansible-master:~# ansible rhel-hosts -m shell -a 'sudo dnf list | grep nss-mdns' -b
[WARNING]: Invalid characters were found in group names but not replaced, use -vvvv to see details
192.168.3.111 | CHANGED | rc=0 >>
nss-mdns.x86_64                                                                          0.15.1-3.1.el9                       @epel
192.168.3.112 | CHANGED | rc=0 >>
nss-mdns.x86_64                                                                          0.15.1-3.1.el9                       @epel
192.168.3.180 | CHANGED | rc=0 >>
nss-mdns.x86_64                                                                          0.15.1-3.1.el9                       @epel
```

Also checked to make sure it was using the epel repo file copied ( the repo-server already had and epel repo from cisco , the main goal was to ensure only server1 and server2 are using the epel file )
```bash
root@ansible-master:~# ansible rhel-hosts -m shell -a 'dnf repolist'
[WARNING]: Invalid characters were found in group names but not replaced, use -vvvv to see details
192.168.3.111 | CHANGED | rc=0 >>
repo id                           repo name
AppStream                         Red hat AppStream repo
BaseOS                            Red hat BaseOS repo
192.168.3.112 | CHANGED | rc=0 >>
repo id                           repo name
AppStream                         Red hat AppStream repo
BaseOS                            Red hat BaseOS repo
192.168.3.180 | CHANGED | rc=0 >>
repo id             repo name
AppStream           Red Hat Enterprise Linux 9.0 AppStream RPMs (DVD)
BaseOS              Red Hat Enterprise Linux 9.0 BaseOS RPMs (DVD)
epel                EPEL RHEL 9 self hosted repo
epel-cisco-openh264 Extra Packages for Enterprise Linux 9 openh264 (From Cisco) - x86_64
```

### Install tools on all RHEL hosts


Install avahi
```bash
ansible rhel-hosts -m shell -a 'sudo dnf install avahi avahi-tools -y' -b
```


### Setup avahi and mDNS 

Enabled the service 
```bash
ansible rhel-hosts -m shell -a 'systemctl enable --now avahi-daemon'
```

Added service into firewall 
```bash
ansible rhel-hosts -m shell -a 'firewall-cmd --add-service=mdns --permanent && firewall-cmd --reload' -b
```

Make sure the /etc/nsswitch.conf file contains the following line 
```bash
hosts: files mdns4_minimal [NOTFOUND=return] dns
```

```bash
ansible rhel-hosts -m shell -a 'cat /etc/nsswitch.conf | grep mdns4' 
```

### Test to make sure it works 

```bash
root@ansible-master:~# ansible rhel-hosts -m shell -a 'ping -c 4 server2.local'
[WARNING]: Invalid characters were found in group names but not replaced, use -vvvv to see details
192.168.3.180 | CHANGED | rc=0 >>
PING server2.local (192.168.3.112) 56(84) bytes of data.
64 bytes from 192.168.3.112 (192.168.3.112): icmp_seq=1 ttl=64 time=0.312 ms
64 bytes from 192.168.3.112 (192.168.3.112): icmp_seq=2 ttl=64 time=0.331 ms
64 bytes from 192.168.3.112 (192.168.3.112): icmp_seq=3 ttl=64 time=0.464 ms
64 bytes from 192.168.3.112 (192.168.3.112): icmp_seq=4 ttl=64 time=0.375 ms

--- server2.local ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3004ms
rtt min/avg/max/mdev = 0.312/0.370/0.464/0.058 ms
192.168.3.112 | CHANGED | rc=0 >>
PING server2.local (192.168.3.112) 56(84) bytes of data.
64 bytes from server2 (192.168.3.112): icmp_seq=1 ttl=64 time=0.038 ms
64 bytes from server2 (192.168.3.112): icmp_seq=2 ttl=64 time=0.016 ms
64 bytes from server2 (192.168.3.112): icmp_seq=3 ttl=64 time=0.022 ms
64 bytes from server2 (192.168.3.112): icmp_seq=4 ttl=64 time=0.038 ms

--- server2.local ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3057ms
rtt min/avg/max/mdev = 0.016/0.028/0.038/0.009 ms
192.168.3.111 | CHANGED | rc=0 >>
PING server2.local (192.168.3.112) 56(84) bytes of data.
64 bytes from server2 (192.168.3.112): icmp_seq=1 ttl=64 time=0.378 ms
64 bytes from server2 (192.168.3.112): icmp_seq=2 ttl=64 time=0.296 ms
64 bytes from server2 (192.168.3.112): icmp_seq=3 ttl=64 time=0.293 ms
64 bytes from server2 (192.168.3.112): icmp_seq=4 ttl=64 time=0.244 ms

--- server2.local ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3098ms
rtt min/avg/max/mdev = 0.244/0.302/0.378/0.048 ms
root@ansible-master:~#
```
