---
title: New Practice Questions 
parent: RHEL
grand_parent: Linux
nav_order: 120
layout: default
---


# RHCSA 9 Practice Questions

The following are non-trivial practice questions/tasks to help you prepare for the RHCSA 9 exam.  
No answers are provided; these are intended for self-practice.

---

## 1. Swap to a System Non-Destructively
1. Add 1 GiB of swap space using a file without removing existing swap.  
2. Verify current swap usage, then enable an additional swap device persistently.  
3. Create a swap file on `/var/swapfile1` with 512 MiB and make it permanent.  
4. Increase swap space by adding another swap file under `/swap_extra`.  
5. Add a new 2 GiB disk to the system and configure it as swap without repartitioning.  
6. Temporarily disable a swap device and re-enable it without rebooting.  
7. Prioritize a swap partition higher than a swap file.  
8. Confirm swap entries persist across reboots.  
9. Add multiple swap files and balance priority levels between them.  
10. Display system performance metrics showing swap usage.  

---

## 2. Configure Autofs
1. Configure autofs to mount `/home/students` from `nfsserver:/srv/nfs/students`.  
2. Create an indirect map that mounts `/mnt/projectX` from an NFS share.  
3. Configure autofs to time out mounts after 60 seconds of inactivity.  
4. Use a direct map to mount `/- /etc/auto.direct`.  
5. Automatically mount `/data/reports` from NFS with read-only access.  
6. Troubleshoot why autofs is not automatically mounting `/mnt/shared`.  
7. Configure automount maps stored in LDAP instead of flat files.  
8. Use wildcards in autofs maps to mount per-user directories.  
9. Make an autofs entry that mounts based on a variable such as `$USER`.  
10. Verify autofs mounts appear only when accessed.  

---

## 3. Schedule Tasks Using at and cron
1. Schedule a one-time script run tomorrow at 10:00 using `at`.  
2. Write a cron job that runs `/usr/local/bin/cleanup.sh` every Sunday at 01:30.  
3. Schedule a job with `at` to shut down the system at 23:00 today.  
4. Configure a system-wide cron job that rotates logs daily at midnight.  
5. Prevent user `bob` from using the `at` command.  
6. Create a cron job for user `alice` that runs every 15 minutes.  
7. Schedule a one-time job with `at` to append output to a file.  
8. Configure a cron job using `/etc/cron.d` instead of `crontab`.  
9. Use `cron` to schedule a task only on weekdays.  
10. Troubleshoot why a scheduled cron job isn’t running as expected.  

---

## 4. Configure Time Service Clients
1. Configure `chronyd` to synchronize with `time.example.com`.  
2. Ensure time synchronization persists after reboot.  
3. Verify current synchronization status using `chronyc`.  
4. Add two public NTP servers to `chrony.conf`.  
5. Configure a local system as an NTP client for `ntp.local`.  
6. Test connectivity to the configured NTP server.  
7. Force an immediate synchronization with the upstream server.  
8. Disable `ntpd` and enable `chronyd` for time sync.  
9. Configure fallback NTP servers for reliability.  
10. Verify that the system clock is within 1 second of server time.  

---

## 5. Manage Default File Permissions
1. Set the default umask for all users to `0022`.  
2. Configure a custom umask for user `bob` only.  
3. Confirm new files created by a user respect the system umask.  
4. Configure `/etc/login.defs` so default umask is inherited by new users.  
5. Demonstrate how to temporarily override umask in a shell session.  
6. Set directory default ACLs to ensure group members always have `rwX` access.  
7. Configure a umask so new files are not world-readable.  
8. Troubleshoot why umask changes aren’t applied after login.  
9. Apply different umask values system-wide vs user-specific.  
10. Show the difference in file permissions when created with umask 0002 vs 0077.  

---

## 6. Manage SELinux Port Labels
1. List all SELinux port contexts currently defined.  
2. Add an SELinux rule allowing HTTP on TCP port 8080.  
3. Change the SELinux label for port 3307 to `mysqld_port_t`.  
4. Remove a custom SELinux port mapping.  
5. Configure SSH to listen on port 2222 with SELinux enforcement.  
6. Verify SELinux allows vs blocks services bound to new ports.  
7. Add a UDP port context for DNS service.  
8. Test connectivity to confirm SELinux changes took effect.  
9. Restore the default SELinux port context for a service.  
10. Troubleshoot why a service fails to bind due to SELinux.  

---

## 7. Use Boolean Settings to Modify System SELinux Settings
1. List all SELinux booleans related to HTTPD.  
2. Enable a boolean to allow HTTPD to make network connections.  
3. Make an SELinux boolean change persistent across reboots.  
4. Disable a boolean to restrict FTP home directory access.  
5. Temporarily set a boolean for testing only.  
6. Configure a boolean to allow NFS home directories.  
7. Set a boolean to allow SSH to forward X11 sessions.  
8. Verify boolean changes are active.  
9. Combine multiple boolean changes in a script.  
10. Troubleshoot why a boolean setting didn’t persist after reboot.  

---

## 8. List and Identify SELinux File and Process Context
1. View SELinux context of all files in `/var/www/html`.  
2. Display SELinux context of the `sshd` process.  
3. Compare SELinux context between `/home/user1` and `/home/user2`.  
4. Use `ls -Z` to verify custom SELinux labels on a directory.  
5. List SELinux process contexts with `ps -eZ`.  
6. Confirm whether a file’s context is default or customized.  
7. Identify mismatched contexts causing a service failure.  
8. Check SELinux context of a log file in `/var/log`.  
9. Compare SELinux contexts before and after a restore operation.  
10. Write output of SELinux file contexts to a report file.  

---

## 9. Configure Superuser Access
1. Allow user `devops` to run all commands as root with `sudo`.  
2. Restrict user `analyst` to only run `/usr/bin/less` with `sudo`.  
3. Configure a sudo rule that requires passwordless execution.  
4. Deny user `guest` from executing any sudo command.  
5. Set up a group `wheel` with full sudo access.  
6. Require `bob` to re-enter his password for each sudo command.  
7. Configure sudo logs to `/var/log/sudo.log`.  
8. Validate sudoers file syntax after changes.  
9. Create an alias in sudoers for multiple commands.  
10. Test user privilege escalation to ensure correct configuration.  

---

## 10. Restore Default File Contexts
1. Change the SELinux context of `/var/www/html/index.html`, then restore it.  
2. Use `restorecon` on a single file.  
3. Restore SELinux contexts recursively for `/home/student`.  
4. Apply `restorecon` after modifying a custom policy.  
5. Check default contexts before restoring.  
6. Restore SELinux contexts using `semanage fcontext`.  
7. Apply restore to files under `/srv/web` after relabeling.  
8. Run restore without affecting unconfined_t contexts.  
9. Verify restored contexts match defaults.  
10. Troubleshoot why a context isn’t restored correctly.  

---

## 11. Configure IPv6 Addresses
1. Assign a static IPv6 address to interface `eth0`.  
2. Configure an additional IPv6 address on the same interface.  
3. Set up an IPv6 default gateway.  
4. Verify IPv6 connectivity to an external host.  
5. Make IPv6 configuration persistent across reboots.  
6. Use `nmcli` to configure IPv6 for DHCP.  
7. Configure IPv6 DNS servers via NetworkManager.  
8. Test dual-stack (IPv4/IPv6) connectivity.  
9. Display current IPv6 routes and explain entries.  
10. Troubleshoot why IPv6 pings to `ipv6.google.com` fail.  
