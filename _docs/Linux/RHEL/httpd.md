---
title: Httpd
parent: RHEL
grand_parent: Linux
nav_order: 160
layout: default
---

# Table of Contents 
{: .no_toc }

1. TOC 
{:toc}


# Change webroot of Apache server 
---

## Step 1. Choose method below 


### Option 1. Change webroot in httpd configuration 

* Open /etc/httpd/httpd.conf 

* Change "**DocumentRoot**" to file location of new webroot

* Change "Directory" to location of new webroot

* reload httpd 


**Example Configuration**

```bash

DocumentRoot "/webfiles"

<Directory "/webfiles">
    AllowOverride None
    # Allow open access:
    Require all granted
</Directory>

```

### Option 2. Create symlink to new webroot ( no config changes required )

```bash
# Make sure new webroot has httpd_sys_content_t port label applied
rm -rf /var/www/html
sudo ln -s /webroot /var/www/html
```


## Step 2. Change file label using SELinux 


> Note: New webroot must have port label changed to the following to work 

```bash
sudo semanage fcontext -a -t httpd_sys_content_t "/new/webroot(/.*)?"
sudo restorecon -Rv /data/share
```

## Step 3. Test 

* Once you have made your changes , test if the changes worked by using curl

```bash
curl http://localhost:80
```

# Allow different port for http using Selinux 
--- 

## Step 1. Change port label for http using semanage

```bash
semanage port -a -t http_port_t -p tcp 81 # Use any alternate port number here  
```

## Step 2 ( optional). add port in firewall 

If you have not done so already , make sure the new port you would like to use has been allowed through your firewall 

```bash
firewall-cmd --add-port=<new-port-here> --permanent && firewall-cmd --reload
```

