---
title: KDE
parent: Misc
grand_parent: Linux
nav_order: 150
layout: default
---
# Table of Contents 
{: .no_toc }

1. TOC 
{:toc}

--- 

# Change default app for opening html files 

## LibreWolf

```bash
xdg-mime default librewolf.desktop text/html
```

## Firefox

```bash
xdg-mime default firefox.desktop text/html
```

## Chromium

```bash
xdg-mime default chromium.desktop text/html
```

## Google Chrome

```bash
xdg-mime default google-chrome.desktop text/html
```

## Rebuild KDE's Application Association Cache

```bash
kbuildsycoca5 --noincremental
```

---
