---
title: Password Security 
parent: RHEL
grand_parent: Linux
nav_order: 140
layout: default
---

# Table of Contents 
{: .no_toc }

1. TOC 
{:toc}

---

# Password strength for PAM 

* Config File: **/etc/security/pwquality.conf**  

* In order to enforce rules for root change the below 

## **Enforce requirements for root**

* Un-Comment the following line in **/etc/security/pwquality.conf**  

```bash
enforce_for_root
```

## Enforce Password Length  

* Config File: **/etc/security/pwquality.conf**

```bash
# Minimum acceptable size for the new password (plus one if
# credits are not disabled which is the default). (See pam_cracklib manual.)
# Cannot be set to lower value than 6.
minlen = 8
enforce_for_root # enforce for root user as well
```

> NOTE: login.defs is no longer used for setting password length requirments

# Change default password age 

## /etc/login.defs

```bash
PASS_MAX_DAYS: How many days the password is active before it expires.

PASS_MIN_DAYS: How many days a password must be active before it can be changed by a user.

PASS_WARN_AGE: The number of days a warning is issued to the user before an impending password expiry.

```

## Chage

**View password age settings for specific user**
```bash
chage -l <USERNAME> 
```

**change password age for specific user**
```bash
chage -E 2025-12-31 bob  # Configures bobs password to expire on december 31st 2025 
```

**Change Min/Max days & Warn**
```bash
chage -M XX <USERNAME> # Change Max days a password is valid 
chage -m XX <USERNAME> # Change min days password can be used before being changed
chage -W XX <username> # Change days user will be warned before pass change is required.  
```
