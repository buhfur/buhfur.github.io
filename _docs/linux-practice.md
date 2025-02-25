
# Linux+ Topics & scenarios to pracice 


## iptables 

- [ ] Block a Specific IP: Define rules to block all traffic from a particular IP address and log any attempts.

- [ ] Allow HTTP/HTTPS Traffic Only: Configure rules that allow incoming traffic on ports 80 and 443 while denying all other incoming connections.

- [ ] SSH Access Restriction: Allow SSH connections only from a specific subnet or trusted IP range.

- [ ] Rate Limiting: Set up rules to limit the rate of incoming connections for a particular service (such as SSH or web traffic).

- [ ] Drop ICMP Requests: Create a rule that drops or limits ICMP (ping) requests.

- [ ] Port Forwarding with NAT: Establish a NAT rule to forward traffic from one port to another.

- [ ] Custom Chain Creation: Develop a custom chain to handle and process specific types of traffic, then integrate that chain into your main rules.

- [ ] Stateful Filtering: Implement rules that filter traffic based on connection states (e.g., NEW, ESTABLISHED, RELATED, INVALID).

- [ ] Default Deny Policy: Write a script that flushes existing rules and sets a default deny policy, while adding exceptions for essential services.

- [ ]  Traffic Logging: Create rules that log suspicious or dropped packets to help analyze network activity later.



Topic 1: User & Group Management
---------------------------------
1. Create a new user account with a specified home directory and default shell.
2. Set a password for the new user.
3. Configure the new account so that the user must change the password upon first login.
4. Create a new group and verify its entry in /etc/group.
5. Add the new user to the newly created group.
6. Remove an existing user from a specified group.
7. Display all user accounts on the system by examining /etc/passwd.
8. Display all groups present on the system.
9. Modify the default shell for an existing user account.
10. Lock a user account to prevent login.
11. Unlock a previously locked user account.
12. Remove a user account and optionally its home directory.
13. Remove a group and verify that its memberships are updated.
14. Verify user account details by inspecting /etc/passwd.
15. Verify group information by inspecting /etc/group.
16. Update a user’s details (e.g., comment field or home directory) using a command like usermod.
17. Create a system account for background services.
18. Write a script to batch add multiple users to a group.
19. Grant sudo access to a specific user by editing the sudoers file.
20. Back up the /etc/passwd and /etc/group files for recovery purposes.

Topic 2: Service Management with systemd
------------------------------------------
1. Create a shell script that writes the current date and time to a log file.
2. Build a systemd service unit file to execute the log script.
3. Save the service unit file in /etc/systemd/system/.
4. Reload the systemd daemon to acknowledge the new service.
5. Enable the service so that it starts automatically at boot.
6. Manually start the service and verify its operation.
7. Use systemctl to check the service status and review its logs.
8. Develop a systemd timer unit that triggers the service on a set schedule (e.g., hourly).
9. Enable and start the timer unit.
10. Check the timer’s status and scheduled activations.
11. Modify the service unit to run under a specific user and group.
12. Configure the service to automatically restart on failure.
13. Set a working directory for the service in the unit file.
14. Configure custom environment variables for the service.
15. Establish a dependency between this service and another systemd unit.
16. Create a custom systemd target and assign your service to it.
17. Simulate a script failure to observe the restart behavior.
18. Disable both the service and its timer.
19. Write a separate script that monitors and restarts the service if it stops unexpectedly.
20. Document all changes made to the service and timer configuration.

Topic 3: Scheduling Tasks with Cron
------------------------------------
1. Write a script that backs up a designated directory.
2. Schedule the backup script to run daily at midnight using cron.
3. Edit the current user’s crontab using the proper command.
4. Define environment variables within the crontab file.
5. Modify a cron job to redirect its output to a specific log file.
6. Schedule a task to run every 15 minutes.
7. Create a cron job that runs only on Saturdays and Sundays.
8. Delete an existing cron job from the crontab.
9. List all current cron jobs for the user.
10. Set up a cron job to run at system startup using the @reboot directive.
11. Schedule a cron job for another user (with appropriate privileges).
12. Configure a cron job to run at a specified date and time.
13. Set a cron job to update system packages weekly.
14. Create a cron job that removes old files from a temporary directory.
15. Schedule a job that monitors disk usage and logs the output.
16. Configure a cron job to run with a random delay to avoid simultaneous execution.
17. Set up a cron job that sends email notifications upon task completion.
18. Verify that a cron job is executing correctly by examining log outputs.
19. Modify the timing of an already scheduled cron job.
20. Export and back up your crontab configuration to a file.

Topic 4: File Permissions and Ownership
-----------------------------------------
1. Create a new directory and a file within it.
2. Change a file’s permissions to 644 using both symbolic and numeric methods.
3. Adjust a directory’s permissions to 755.
4. Use chown to change the file owner to another user.
5. Modify a file’s group using chgrp or chown.
6. Recursively set permissions for all files in a directory.
7. Change permissions on a file using symbolic notation (e.g., u+x).
8. Use numeric (octal) notation to modify file permissions.
9. Create or modify an executable file to set the setuid bit.
10. Create a shared directory and apply the sticky bit.
11. Apply an Access Control List to grant a specific user additional permissions on a file.
12. Verify the ACL settings on a file using getfacl.
13. Remove an ACL entry from a file using setfacl.
14. Write a script to change group ownership on multiple files.
15. Set and test the default umask for new files and directories.
16. Use ls -l to document permissions before and after changes.
17. Create a script that resets file permissions to a predefined standard.
18. Create a file with no permissions and then restore default permissions.
19. Adjust group permissions to allow execution on a file.
20. Write a script to back up current permissions and restore them when needed.

Topic 5: Process Management
---------------------------
1. Use ps to display all running processes.
2. Launch top or htop to monitor system processes in real time.
3. Use ps or pgrep to find a process by name and note its PID.
4. Gracefully terminate a process using the SIGTERM signal.
5. Force terminate a process with SIGKILL if it does not respond to SIGTERM.
6. Write a script to monitor a specific process’s CPU usage over time.
7. Create a script that tracks a process’s memory consumption.
8. Utilize pgrep to find processes matching a specific pattern.
9. Use pkill to terminate all processes matching a given name.
10. Start a process in the background and verify it is running.
11. Bring a background process to the foreground using job control commands.
12. Suspend a process (using Ctrl+Z) and then resume it with fg or bg.
13. Use pstree to visualize parent-child relationships among processes.
14. Run a long‑running process with nohup to ensure it continues after logout.
15. Change the niceness value of a running process using renice.
16. Use htop to interactively manage and monitor processes.
17. Create a script that logs CPU and memory usage of a process over time.
18. Set up a mechanism (using cron or systemd) to automatically restart a specific process if it stops.
19. Configure a systemd service to manage a long‑running process.
20. Document overall process resource usage and create a summary report.

Topic 6: System Logging Configuration
---------------------------------------
1. Configure rsyslog to write specific log messages to a custom log file.
2. Develop a new rsyslog configuration file for filtering certain logs.
3. Restart the rsyslog service to apply configuration changes.
4. Set up a filter in rsyslog to log messages of a specific severity to a separate file.
5. Configure rsyslog to forward selected log messages to a remote log server.
6. Create a logrotate configuration for your custom log file.
7. Use the logger command to generate a test log message and verify its destination.
8. Send a test message via logger and check its logging.
9. Use journalctl to view logs from the last hour for a particular service.
10. Export filtered journal logs to a file for offline analysis.
11. Modify the rsyslog configuration to change the default log file location for a facility.
12. Activate debug logging in rsyslog and verify increased verbosity.
13. Back up the original rsyslog configuration file before making changes.
14. Examine the system log files for common error messages and document your findings.
15. Configure logrotate to manage log retention and file size limits.
16. Adjust file permissions on log files to enhance security.
17. Create a script to monitor log file sizes and send alerts when they grow beyond a threshold.
18. Write a script to archive and compress logs older than a specified date.
19. Set up a centralized logging solution on your VM.
20. Record every change made to the logging configuration and maintain a backup copy of config files.

Topic 7: Disk & Partition Management
--------------------------------------
1. Use fdisk -l (or a similar command) to list all available disks and partitions.
2. Use fdisk or parted to create a new partition on a virtual disk.
3. Format the new partition with a filesystem (e.g., ext4).
4. Manually mount the new partition to a designated directory.
5. Add an entry in /etc/fstab to mount the partition automatically at boot.
6. Initialize a new partition table on a virtual disk.
7. Resize an existing partition using parted or another tool.
8. Create a new physical volume, add it to a volume group, and create a logical volume.
9. Format the logical volume with an appropriate filesystem.
10. Mount the logical volume and update /etc/fstab accordingly.
11. Create and activate a swap file or partition.
12. Use df to display disk space usage for all mounted filesystems.
13. Use du to determine the disk usage of specific directories.
14. Set up a RAID configuration using mdadm on multiple partitions.
15. Test the RAID configuration by simulating a disk failure scenario.
16. Use tools like sfdisk to back up the current partition table.
17. Practice restoring a partition table from a backup file.
18. Modify the label of a partition using a tool like e2label.
19. Perform a filesystem integrity check using fsck.
20. Develop a script that monitors disk usage and sends alerts when thresholds are exceeded.

Topic 8: Network Configuration
-------------------------------
1. Use ip addr (or ifconfig) to list network interfaces and their statuses.
2. Configure a network interface with a static IP address.
3. Update /etc/resolv.conf to change DNS server settings.
4. Restart networking services to apply configuration changes.
5. Use ping to verify connectivity to an external host.
6. Display the current routing table using ip route or route.
7. Add a secondary IP address to an existing network interface.
8. Reconfigure a network interface to use DHCP for dynamic IP assignment.
9. Implement a basic firewall rule with iptables (or nftables) to block a specific port.
10. Use netstat or ss to display active network connections.
11. Use traceroute (or tracepath) to diagnose network path issues.
12. Adjust NetworkManager settings for a network interface if applicable.
13. Establish a VPN connection using command‑line tools and configuration files.
14. Verify DNS functionality using nslookup or dig.
15. Add a custom routing rule for specific traffic.
16. Use iftop to observe real‑time network bandwidth usage.
17. Write a script that checks and logs network connectivity at intervals.
18. Set up a mechanism to automatically switch network configurations upon failure.
19. Record every change made to network settings and back up configuration files.
20. Develop a script that resets network settings automatically if connectivity is lost.

Topic 9: Package Management
---------------------------
1. Refresh the package index using your distro’s package manager (e.g., apt-get update or yum update).
2. Perform a full system upgrade.
3. Install a new package from the repositories.
4. Uninstall an installed package and verify its removal.
5. Use a package search command (e.g., apt-cache search or yum search) to find a package.
6. List installed packages to confirm a package’s presence.
7. Download a package without immediately installing it.
8. Install a package from a local .deb (or equivalent) file.
9. Resolve and fix broken dependencies using appropriate package manager commands.
10. Clear the package cache to free up space.
11. Generate a complete list of available packages.
12. Add a new repository or PPA (if using Ubuntu/Debian).
13. Remove or disable an added repository from the configuration.
14. Lock a package version to prevent automatic upgrades.
15. Downgrade a package to an earlier version and verify compatibility.
16. Apply security updates only and document the process.
17. Set up unattended upgrades for automated security patches.
18. Display detailed information about a package using the appropriate commands.
19. Check for available package updates.
20. Document and back up package management logs and history for future reference.

Topic 10: System Resource Monitoring
--------------------------------------
1. Use top to observe real‑time CPU and memory usage.
2. Install and run htop to monitor system resources interactively.
3. Display current memory usage using the free command.
4. Use vmstat to obtain system performance metrics.
5. Install and run iostat to monitor disk input/output activity.
6. Write a script to log system resource usage (CPU, memory) at regular intervals.
7. Install and configure sysstat to use sar for detailed performance tracking.
8. Use mpstat to create a CPU usage report and save it to a file.
9. Use iftop to monitor real‑time network bandwidth usage.
10. Use the uptime command to display load averages.
11. Use dstat to display multiple system resource statistics simultaneously.
12. Verify swap usage using free or swapon -s.
13. Configure monitoring tools to alert when CPU or memory usage exceeds a defined threshold.
14. Create a cron job that runs your resource monitoring script at set intervals.
15. Redirect the output of your monitoring script to a log file for historical analysis.
16. Use available tools (e.g., GNUPlot) to create a graphical report of resource usage.
17. Capture and compare resource usage data between two time intervals.
18. Write a script to monitor and log resource usage for a specific process.
19. Create a summary report of system resource data over a period.
20. Outline and implement a comprehensive plan for ongoing system performance monitoring.

