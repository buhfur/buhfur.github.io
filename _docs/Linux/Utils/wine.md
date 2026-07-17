---
title: Wine Snippets / Info
parent: Utils
grand_parent: Linux
nav_order: 190
layout: default
---
# Table of Contents 
{: .no_toc }

1. TOC 
{:toc}

---


# Pre setup for 32 bit mode 

Debian/Ubuntu: 
```bash
sudo dpkg --add-architecture i386
sudo apt update
sudo apt install wine wine32 wine64 libwine libwine:i386 fonts-wine
```

WineHQ Staging( Debian ):
```bash
sudo apt install wget software-properties-common
sudo wget -nc https://dl.winehq.org/wine-builds/winehq.key -O /usr/share/keyrings/winehq-archive.key
sudo wget -nc https://dl.winehq.org/wine-builds/debian/dists/bookworm/winehq-bookworm.sources -O /etc/apt/sources.list.d/winehq.sources
sudo apt update
sudo apt install --install-recommends winehq-stable  # or winehq-staging
```

RHEL:
```bash
sudo dnf install epel-release
sudo dnf groupinstall "Development Tools"
sudo dnf install wine wine-alsa wine-core wine-capi wine-fonts wine-libs wine-tools wine-mono wine-gecko
sudo dnf install glibc.i686 libgcc.i686 libX11.i686 freetype.i686
```

Arch:

In /etc/pacman.conf uncomment the following lines:

```bash
[multilib]
Include = /etc/pacman.d/mirrorlist
```

Install:
```bash
sudo pacman -Syu
sudo pacman -S wine wine-mono wine-gecko lib32-mesa lib32-libpulse lib32-alsa-lib lib32-freetype2 lib32-glibc
```

See next section for creating a 32-bit prefix.

# Prefix info 

* A wine prefix is a environment variable which contains a path to a directory which stores the isolated windows environment. 

* AKA all the files like `dosdevices` and the imitation windows C drive directory structure.   

* This allows you to configure indpendent environments for different windows applications  

* I've created a directory in `/home/buhfur/wine-prefixes` that acts as the main folder for storing different wine environments 

## Steps to create new prefix with windows environments 

For this example we're going to be using the /home/buhfur/wine-prefixes/minesweeper folder.

### **1. Set this environment variable or add as an alias. run this command in your shell**

```bash
# Run this in a shell to temporarily set the wineprefix the specified directory 
# Any further commands or apps run with wine will use this prefix 
export WINEPREFIX="/home/buhfur/wine-prefixes/minesweeper" 

# Then run the command below , this initiates the prefix and creates the directory structure 
wineboot -u 
```

### **2. You can add an export to your environment ( i.e bash , zsh etc ) to point to wine environment to make it easy to switch between prefixes**

```bash
# in my ~/.bashrc , points to prefix I have for a specific app 
export MYPREFIX="$HOME/wine-prefixes/myprefix"

# Then after you set that you can change your prefix by adding an alias or manually 
alias myprefix="WINEPREFIX=$MYPREFIX"

```



# Setup 32-bit prefix

There are multiple ways to do this , you can create a prefix on the CLI or add it to your bashrc to make default 


## **CLI:** 
```bash
WINEARCH=win32 WINEPREFIX=~/wine32prefix winecfg
```

To use this prefix: 

```bash
WINEPREFIX=~/wine32prefix wine your_app.exe
```

To make prefix default, add this in bashrc:
```bash
export WINEPREFIX=~/wine32prefix
export WINEARCH=win32
```

Check prefix for 32-bit:
```bash
file "$WINEPREFIX/drive_c/windows/explorer.exe"
```

# Setup 64+32 bit prefix 

```bash
WINEPREFIX=~/wineprefix winecfg
```

# Issues

Anytime you encounter errors , it's usually a good idea to delete the ~/.wine cache
```bash
rm -rf ~/.wine 
```



# Run wine with debug output limited to errors 
```bash
WINEDEBUG=-all wine your_app.exe
```
