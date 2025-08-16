---

layout: default
title: "SSH notes"

---

# Table of Contents  
{: .no_toc }

1. TOC 
{:toc}

---


# SSH 


## **Enable X11 forwarding on remote host**

On remote host, change these settings in your /etc/ssh/sshd\_config

```bash
X11Forwarding yes
X11DisplayOffset 10
X11UseLocalhost yes
```


On remote host , install xauth 

```bash
sudo apt install xauth     # Debian/Ubuntu
sudo yum install xorg-x11-xauth  # RHEL/CentOS
```

Restart SSH systemd daemon 

```bash
systemctl restart sshd
```

Connect on local host with `-X` or `-Y` option 

```bash
ssh -X user@remote_host
ssh -Y user@remote_host ( less strict )
```

After connected , test with xclock 

```bash
xclock
```

# Configuring SSH 
---

dictionary attacks are commonly used to break into ssh servers , from what i've seen on my machine they typically use usernames and passwords that might be setup by default on the server. 

These are things you can do to harden your ssh server 
- Disable root login ( disabled by default) 
- Disable password login ( not disabled by default ) 
- Configure a non-default port for SSH  ( already done for most of my servers )
- Allow specific users only to login to ssh ( I haven't done this yet )


To configure these things , you can make all configuration changes in the /etc/ssh/sshd_config file. This config file is the default config used for an ssh server.

## Config option for disallowing the root user to login to ssh 

PermitRootLogin prohibit-password 

This only allows the root user to login if they have a valid pub/private key pair 


## Changing default ssh port 

Edit config file in /etc/ssh/sshd_config 

Add port to SELinux label 
`semanage port -a -t ssh_port_t -p tcp <PORTNUMBER>`

Allow port through firewall-cmd 

`firewall-cmd --add-port=<port>/tcp --permanent `

Then reload the configuration for the service 

`systemctl reload sshd`


Then reload the firewall-cmd config 

`firewall-cmd --reload`

You shoulden't really use the MaxAuthTries option since it's suseptible to ddos attacks. When this option is enabled it locks out the specified user trying to login. So someone could just try to login as that usera bunch to lock up the account. It's still good for monitoring security events as this option starts logging the failed attempts. 

Logs are written to the AUTHPRIV syslog utility. By default it writes the logs to /var/log/secure

## Restriciting which users can login to your ssh server 

You can enable access to your server by the usernames of the users. To do this , you must add the AllowUsers field in the /etc/ssh/sshd_config file 

## Other useful sshd options 

UseDNS -> Very inefficient if other users connections are slow , turn off for performance. This option verifies remote hostname is same as remote address 

MaxSessions -> specifies max number of sessions from the same remote IP. If you have users connecting to the server with multiple sessions it's a good practice to increase the amount of max sessions.  

TCPKeepAlive -> ensures clients that are unavailable are released 

ClientAliveInterval -> time before the server sends a packet to the client when no activity is detected. 

ClientAliveCountMax -> specifies how many of these packets are sent. if set to 30 and ClientAliveCountMax is set to 10 , connections are kept alive for about 5 minutes. 

The two options above can only be used for the server side of ssh connections. 
You can try using two options for clients which try to do the same thing 

ServerAliveInterval & ServerAliveCountMax 

modify the ~/.ssh/config for local users 

I'd also like to note other options that are used with ssh as well 

HostBasedAuthentication -> Allows only users who's keys are already present in /etc/ssh/ssh_known_hosts. To enable this , add the key in the users .ssh/authorized_keys and copy it over to the directory mentioned previously.


## Key locations 

Client/Sever public/private keys -> /etc/ssh

Clients pub pri key -> /home/user/.ssh

## Key pair authentication 

Generate public / private key 
`ssh-keygen`

Then copy the key over to your server , make sure password auth is enabled 
`ssh-copy-id -p 1234 user@192.1.1.1`

If you set a passphrase , cache the passphrase to save time 

`ssh-agent /bin/bash`

`ssh-add`

You can set a passphrase for the private key which is in most cases more secure, however it is inconveinient for the user as they will have to enter this key everytime they attempt to connect to the server. However you can cache the key for a short amount of time using the ssh-agent and ssh-add commands. To do so , see below 

