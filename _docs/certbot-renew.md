---

layout: default
title: "Certbot auto-renewal"

---

# Table of Contents 

{: .no_toc }

1. TOC 

{:toc}

---


# Renewing certificate for jellyfin nginx proxy 

Goal is to allow certbot to renew the cert automatically. 

## Issues 

- certbot can't renew since directory for validation is redirecting to https and can't access the required directory to complete the challenge 


## What i've tried so far. 


- Checked namecheap for redirections 
- commented out nginx 301 redirect to https 
- changed domain from https to http
- changed domain from http to https 
- removed mail service domain in namecheap advanced dns settings 



## Namecheap config 

### Old

Source URL: free-shit.pro 

Dest URL: https://free-shit.pro

### New 

Source URL: free-shit.pro 

Dest URL: http://free-shit.pro

> Note changed it back 

## Nginx config 

- commented out the location / section  , systemctl reload nginx 


### Current nginx config ( with 301 commented out )

```bash
server {
        listen 80;
        server_name www.free-shit.pro;

    location /.well-known/acme-challenge/ {
        root /var/www/certbot;
        allow all;
    }

    #location /{

        #return 301 https://$host$request_uri;
        #}

}

server {

        listen 443 ssl http2;

        server_name www.free-shit.pro;

    ssl_certificate /etc/letsencrypt/live/free-shit.pro/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/free-shit.pro/privkey.pem; 

        client_max_body_size 20M;

        ssl_protocols TLSv1.3 TLSv1.2;


    location / {

        proxy_pass http://127.0.0.1:8096;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }



}
```
