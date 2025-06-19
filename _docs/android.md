---

layout: default
title: "Android"

---

# Table of Contents 

{: .no_toc }

1. TOC 
{:toc}

---

# MTP Protocol Notes 

- MTP protocol is used with android for transferring files 
- high level file transfer protocol 
- originally used with USB , however implemented TCP / IP , Bluetooth implementations exist.
- implementation can vary across different devices , however it is generally used with mobile phones 
- Clients do not see array of byte blocks that make up data structures that make up a file system


# Adb snippets 

`adb kill-server` -> stops adb server 

`adb start-server` -> starts adb server

`adb devices` -> list all connected adb devices 


