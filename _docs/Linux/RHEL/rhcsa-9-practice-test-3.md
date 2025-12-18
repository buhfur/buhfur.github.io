---
title: RHCSA Practice Tests 3
parent: RHEL
grand_parent: Linux
nav_order: 200
layout: default
---

# Table of Contents 
{: .no_toc }

1. TOC 
{:toc}


# RHCSA 9 Practice Exams (EX200)

> **Scope:** These practice exams are designed to closely mimic the **RHCSA 9 (EX200)** hands‑on exam format.
>
> **Containers/Podman tasks are intentionally excluded.**
>
> Each exam covers **all remaining official objectives** and is suitable for **2.5–3 hour timed practice** in a RHEL 9 lab.

---

## Exam Instructions (Simulate the Real Exam)

- You have **2.5 hours** to complete each exam.
- Internet access is **not allowed**.
- You may use `man`, `/usr/share/doc`, and local system documentation.
- All tasks must persist after reboot unless stated otherwise.
- Hostname: `rhcsa9.exam`
- Root password: `redhat`

---

# Practice Exam 1 – Core Administration

## Objective 1: Essential Tools

1. Create a file `/root/files.txt` containing a recursive listing of `/etc`, sorted alphabetically, with all permission denied errors suppressed.
2. Create a compressed archive `/root/etc-backup.tar.gz` containing all files under `/etc` that were modified in the last 7 days.
3. Find all regular files larger than 50 MB under `/var` and write the output to `/root/largefiles.txt`.

---

## Objective 2: File Systems & Storage

4. Create a 1 GiB logical volume named `lvdata` in volume group `vgdata`.
5. Format the logical volume with the XFS filesystem.
6. Mount the filesystem persistently at `/data`.
7. Configure the filesystem to mount automatically at boot.

---

## Objective 3: Local User and Group Management

8. Create a group named `developers` with GID 4000.
9. Create a user `linda` with UID 2000 and primary group `developers`.
10. Set the password for `linda` to expire after 90 days.
11. Lock the user account `linda`.

---

## Objective 4: Permissions & ACLs

12. Create the directory `/shared/dev`.
13. Configure the directory so that files created inside inherit the group ownership.
14. Set an ACL so user `anna` has read and write access to `/shared/dev`.

---

## Objective 5: Networking

15. Configure a static IPv4 address using NetworkManager:
    - IP: `192.168.50.20/24`
    - Gateway: `192.168.50.1`
    - DNS: `192.168.50.1`
16. Verify connectivity persists after reboot.

---

## Objective 6: Firewall Configuration

17. Configure the firewall to allow incoming HTTP traffic permanently.
18. Verify the firewall configuration.

---

## Objective 7: Process and Service Management

19. Configure the `chronyd` service to start at boot.
20. Ensure the service is currently running.

---

## Objective 8: Software Management

21. Configure a YUM repository using the URL:
    `http://content.example.com/rhel9/BaseOS`
22. Ensure the repository is enabled and functional.

---

## Objective 9: Task Scheduling

23. Schedule a cron job for user `linda` that runs `/usr/bin/logger "RHCSA exam"` every day at 14:30.

---

## Objective 10: Boot & System Management

24. Configure the system to boot into multi-user (non-graphical) mode by default.

---

# Practice Exam 2 – Troubleshooting Focus

## Objective 1: Essential Tools

1. Redirect all lines containing the word `error` (case insensitive) from `/var/log/messages` into `/root/errors.txt`.
2. Display the top 5 processes by memory usage and save the output to `/root/memtop.txt`.

---

## Objective 2: Storage & Swap

3. Create a 512 MiB swap file at `/swapfile`.
4. Activate the swap file persistently.
5. Verify swap usage.

---

## Objective 3: Users & Authentication

6. Create a user `anna` with a non-interactive shell.
7. Configure the system so failed login attempts are logged.

---

## Objective 4: SELinux

8. Ensure SELinux is in enforcing mode.
9. Configure SELinux to allow HTTP service to read files from `/webcontent`.
10. Restore correct SELinux contexts on `/webcontent`.

---

## Objective 5: Networking

11. Disable IPv6 on the primary network interface persistently.
12. Verify the configuration after reboot.

---

## Objective 6: Firewall & Services

13. Install and configure the Apache HTTP server.
14. Configure it to listen on port 80.
15. Allow access through the firewall.

---

## Objective 7: Logging & Troubleshooting

16. Configure `journald` to persist logs across reboots.
17. Verify logs are retained after reboot.

---

## Objective 8: Time & Scheduling

18. Configure the system to synchronize time using `time.example.com`.

---

## Objective 9: Boot Recovery

19. Reset the root password using the bootloader.
20. Verify successful login.

---

# Practice Exam 3 – Mixed Difficulty (Exam‑Style)

## Objective Coverage: All (Excluding Containers)

1. Create a tar archive of `/home` excluding all `.cache` directories.
2. Configure user `bob` with passwordless sudo for `/usr/bin/systemctl status`.
3. Create an autofs map to mount `/exports/home/&` at `/home/users/&`.
4. Ensure the mount occurs on demand.
5. Configure a persistent static route to network `10.10.10.0/24` via `192.168.50.254`.
6. Configure `rsyslog` to log all cron messages to `/var/log/cron-custom.log`.
7. Set SELinux boolean to allow FTP home directories.
8. Configure a systemd service that runs `/usr/bin/logger "Service started"` at boot.
9. Verify the service status.
10. Reboot and validate all configurations persist.

---

## Scoring Recommendation

- 0–69%: Needs improvement
- 70–79%: Borderline pass
- 80–100%: Exam‑ready

---

## Notes for Wiki Integration

- This file is compatible with **Just‑the‑Docs**, **GitHub Pages**, and **MediaWiki (Markdown extension)**.
- You may split each exam into separate pages if desired.
- Add answer keys in a separate, restricted page to avoid accidental review.

---

*End of RHCSA 9 Practice Exams*

