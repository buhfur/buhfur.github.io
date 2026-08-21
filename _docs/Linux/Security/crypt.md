---
title: Password & Cryptography stuff 
parent: Utils
grand_parent: Security 
nav_order: 20
layout: default
---
# Table of Contents 
{: .no_toc }

1. TOC 
{:toc}

---


# Create random base-64 encoded random password 

```bash
openssl rand -base64 32 > password.txt
```
