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

# Password Length Requirements 

* Config File: **/etc/login.defs**

* Find the following values in the mentioned file , tune them to preference

> NOTE: login.defs is no longer used for setting password length requirments

```bash
PASS_MAX_DAYS   99999
PASS_MIN_DAYS   0
PASS_WARN_AGE   7
```

## Check age of password 

* Use the chage command 
