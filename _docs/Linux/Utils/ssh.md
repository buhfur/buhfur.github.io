---
title: SSH
parent: Utils
grand_parent: Linux
nav_order: 150
layout: default
---
# Table of Contents  
{: .no_toc }

1. TOC  
{:toc}

---

# SSH

## **Enable X11 forwarding on remote host**

On the remote host, change these settings in your `/etc/ssh/sshd_config`:

```bash
X11Forwarding yes
X11DisplayOffset 10
X11UseLocalhost yes
```

Install `xauth` on the remote host:

```bash
sudo apt install xauth              # Debian/Ubuntu
sudo yum install xorg-x11-xauth    # RHEL/CentOS
```

Restart the SSH systemd daemon:

```bash
systemctl restart sshd
```

Connect on the local host with the `-X` or `-Y` option:

```bash
ssh -X user@remote_host
ssh -Y user@remote_host    # Less strict
```

After connecting, test with `xclock`:

```bash
xclock
```

---

# Configuring SSH

Dictionary attacks are commonly used to break into SSH servers. From what I've seen on my machine, they typically use usernames and passwords that might be set up by default on the server.

These are things you can do to harden your SSH server:

- Disable root login (disabled by default)
- Disable password login (not disabled by default)
- Configure a non-default port for SSH (already done for most of my servers)
- Allow specific users only to log in to SSH (I haven't done this yet)

To configure these things, you can make all configuration changes in the `/etc/ssh/sshd_config` file. This config file is the default config used for an SSH server.

## Config option for disallowing the root user to log in to SSH

```bash
PermitRootLogin prohibit-password
```

This only allows the root user to log in if they have a valid public/private key pair.

## Changing the default SSH port

Edit the config file in `/etc/ssh/sshd_config`.

Add port to SELinux label:

```bash
semanage port -a -t ssh_port_t -p tcp <PORTNUMBER>
```

Allow port through `firewall-cmd`:

```bash
firewall-cmd --add-port=<port>/tcp --permanent
```

Then reload the configuration for the service:

```bash
systemctl reload sshd
```

Then reload the `firewall-cmd` config:

```bash
firewall-cmd --reload
```

You shouldn't really use the `MaxAuthTries` option since it's susceptible to DDoS attacks. When this option is enabled, it locks out the specified user trying to log in. So someone could just try to log in as that user a bunch to lock up the account. It's still good for monitoring security events as this option starts logging the failed attempts.

Logs are written to the `AUTHPRIV` syslog utility. By default, it writes the logs to `/var/log/secure`.

## Restricting which users can log in to your SSH server

You can enable access to your server by the usernames of the users. To do this, add the `AllowUsers` field in the `/etc/ssh/sshd_config` file.

## Other useful sshd options

- `UseDNS` — Very inefficient if users' connections are slow. Turn off for performance. This option verifies the remote hostname is the same as the remote address.
- `MaxSessions` — Specifies max number of sessions from the same remote IP. If users connect to the server with multiple sessions, it's a good practice to increase this.
- `TCPKeepAlive` — Ensures clients that are unavailable are released.
- `ClientAliveInterval` — Time before the server sends a packet to the client when no activity is detected.
- `ClientAliveCountMax` — Specifies how many of these packets are sent. If set to 30 and `ClientAliveCountMax` is set to 10, connections are kept alive for about 5 minutes.

The two options above can only be used for the server side of SSH connections.

You can try using these two options for clients which attempt the same behavior:

- `ServerAliveInterval`
- `ServerAliveCountMax`

Modify the `~/.ssh/config` for local users.

### Other options used with SSH

- `HostBasedAuthentication` — Allows only users whose keys are already present in `/etc/ssh/ssh_known_hosts`. To enable this, add the key to the user’s `.ssh/authorized_keys` and copy it to the directory mentioned previously.

## Key locations

- Client/Server public/private keys → `/etc/ssh`
- Client's pub/priv key → `/home/user/.ssh`

## Key pair authentication

Generate public/private key:

```bash
ssh-keygen
```

Then copy the key over to your server (make sure password auth is enabled):

```bash
ssh-copy-id -p 1234 user@192.1.1.1
```

If you set a passphrase, cache the passphrase to save time:

```bash
ssh-agent /bin/bash
ssh-add
```

You can set a passphrase for the private key which is, in most cases, more secure. However, it is inconvenient for the user as they will have to enter this key every time they attempt to connect to the server. You can cache the key for a short amount of time using the `ssh-agent` and `ssh-add` commands (see above).


## Local Client Host Configuration 

You can specify specific IP's , Hostnames , and ports for unique hosts in a file located in the users `/home/user/.ssh` directory. This allows you to specify specific ssh Configuration options per host.  

Configure Port , Host and User for ssh connection 

```bash
Host hostname-1
    ServerAliveInterval 0
    Port 6225
    X11Forwarding no
```

