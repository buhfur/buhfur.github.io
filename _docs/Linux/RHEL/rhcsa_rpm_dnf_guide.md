---
title: RPM & DNF 
parent: RHEL
grand_parent: Linux
nav_order: 110
layout: default
---

# Table of Contents 
{: .no_toc }

1. TOC 
{:toc}

---

# RPM & DNF Guide (RHCSA Exam Focus)

This guide covers **RPM** and **DNF** package management commands
relevant to the **RHCSA exam**.\
Use this as a reference for study and practice.

------------------------------------------------------------------------

## 1. RPM (Red Hat Package Manager)

### Check if a package is installed

``` bash
rpm -q package_name
```

### List all installed packages

``` bash
rpm -qa
```

### Get package info

``` bash
rpm -qi package_name
```

### List files installed by a package

``` bash
rpm -ql package_name
```

### Find which package owns a file

``` bash
rpm -qf /path/to/file
```

### Verify a package (check for missing/corrupted files)

``` bash
rpm -V package_name
```

### Install a package (local .rpm file)

``` bash
rpm -ivh package_name.rpm
```

### Upgrade a package

``` bash
rpm -Uvh package_name.rpm
```

### Remove a package

``` bash
rpm -e package_name
```

------------------------------------------------------------------------

## 2. DNF (Dandified YUM)

DNF is the default package manager in RHEL 8/9.

### Search for a package

``` bash
dnf search package_name
```

### Get package info

``` bash
dnf info package_name
```

### Install a package

``` bash
dnf install package_name -y
```

### Remove a package

``` bash
dnf remove package_name -y
```

### Update all packages

``` bash
dnf update -y
```

### Check for available updates

``` bash
dnf check-update
```

### Upgrade a package

``` bash
dnf upgrade package_name -y
```

### Reinstall a package

``` bash
dnf reinstall package_name
```

### List installed packages

``` bash
dnf list installed
```

### List available packages

``` bash
dnf list available
```

### List package groups

``` bash
dnf group list
```

### Install a group of packages

``` bash
dnf groupinstall "Server with GUI" -y
```

### Remove a group of packages

``` bash
dnf groupremove "Server with GUI" -y
```

------------------------------------------------------------------------

## 3. Repository Management

### Show enabled repositories

``` bash
dnf repolist enabled
```

### Show all repositories (enabled + disabled)

``` bash
dnf repolist all
```

### Temporary enable/disable repo

``` bash
dnf --enablerepo=repo_name install package_name
dnf --disablerepo=repo_name install package_name
```

### Permanently enable/disable a repo

Edit the repo file under:

    /etc/yum.repos.d/*.repo

Example section in `.repo` file:

``` ini
[baseos]
name=RHEL BaseOS
baseurl=http://content.example.com/rhel9/baseos
enabled=1
gpgcheck=1
```

------------------------------------------------------------------------

## 4. Exam Tips & Tricks

-   Know how to install, update, and remove packages with **dnf**.
-   Be familiar with **rpm -qf** for finding which package owns a file.
-   Understand how to **list repos** and edit `.repo` files under
    `/etc/yum.repos.d/`.
-   Be able to **verify package integrity** using `rpm -V`.
-   Practice **group installs** for environments like *Server with GUI*.

------------------------------------------------------------------------

## Practice Scenarios

1.  Verify which package owns `/bin/ls`.
2.  Install the `httpd` package and verify its version.
3.  Enable a disabled repo and install a package from it.
4.  Perform a group installation for `"Development Tools"`.
5.  Remove `vsftpd` and confirm it is gone.

------------------------------------------------------------------------

This guide aligns with RHCSA exam objectives for **RPM/DNF package
management**.
