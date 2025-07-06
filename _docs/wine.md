---

layout: default
title: "Wine Snippets / Info"

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

# Setup 32-bit prefix

There are multiple ways to do this , you can create a prefix on the CLI or add it to your bashrc to make default 


CLI: 
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

# Issues

Anytime you encounter errors , it's usually a good idea to delete the ~/.wine cache
```bash
rm -rf ~/.wine 
```
