---
title: Desktop 
parent: Misc
grand_parent: Linux
nav_order: 120
layout: default
---
# Table of Contents 
{: .no_toc }

1. TOC 
{:toc}

--- 


# KDE 

## Create desktop icon 

* Save into : ~/.local/share/applications/myapp.desktop

* Will allow you to start the application from the start menu 

* To make desktop icon , cp desktop file over to ~/Desktop 

```bash
[Desktop Entry]
Type=Application
Name=My App
Exec=/home/youruser/Applications/MyApp.AppImage
Icon=/home/youruser/Applications/myapp.png
Terminal=false
Categories=Utility;
```
