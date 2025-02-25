
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


---

# SELinux Hands-On Practice Tasks for Linux+ Exam Preparation

Below are 60 practical tasks to help you learn and work with SELinux on a Linux machine. Each task is designed to be performed directly on your system.

1. **Check SELinux Status:** Run the command to display the current SELinux mode and active policy.
2. **Switch to Permissive Mode Temporarily:** Change SELinux mode to permissive using the appropriate command.
3. **Switch Back to Enforcing Mode:** Revert SELinux back to enforcing mode.
4. **Display File Security Context:** Use a command to display the SELinux context of a common file (e.g., `/etc/passwd`).
5. **Change File Context Using chcon:** Create a test file and modify its SELinux context using `chcon`.
6. **Restore File Context to Default:** Reset the test file’s context to its default value using the proper restoration command.
7. **List All SELinux Booleans:** Display all available SELinux booleans on your system.
8. **Temporarily Modify an SELinux Boolean:** Change a boolean (for example, `httpd_enable_homedirs`) without making a permanent change.
9. **Permanently Modify an SELinux Boolean:** Change an SELinux boolean permanently using the appropriate flag.
10. **Add a New File Context Rule:** Use `semanage fcontext` to add a file context rule for a test directory.
11. **Apply the New File Context Rule:** Run the command that applies the new file context rule to the test directory.
12. **Check Active SELinux Policies:** Verify which SELinux policy is active (e.g., targeted or strict).
13. **List Running Processes with SELinux Contexts:** Display all running processes along with their SELinux contexts.
14. **Inspect a Specific Process Context:** Identify a process (e.g., `sshd`) and check its SELinux context (via `/proc/<pid>/attr/current` or similar).
15. **Map a Linux User to an SELinux User:** Create a mapping between a Linux user and an SELinux user using the appropriate command.
16. **List SELinux User Mappings:** Display the current SELinux user mappings.
17. **Generate an SELinux Audit Report:** Use the SELinux audit tool to generate a human-readable audit report.
18. **Filter SELinux Audit Messages:** Filter recent SELinux audit log entries using `ausearch` or a similar command.
19. **Generate a Custom Policy Module:** Simulate a denial and use `audit2allow` to generate a custom SELinux policy module.
20. **Load the Custom Policy Module:** Install the module generated in Task 19 using the SELinux module loader.
21. **Remove an SELinux Module:** Uninstall the custom module using the appropriate command.
22. **Trigger a Full Filesystem Relabel:** Create the necessary file to trigger a full filesystem relabel on the next reboot.
23. **Create a Test File and Change Its Context:** Create a test file (e.g., in `/tmp`) and manually assign a new SELinux context.
24. **Verify the File Context Change:** Confirm that the test file’s context has been changed.
25. **List Installed SELinux Modules:** Display a list of all currently installed SELinux modules.
26. **Adjust a Boolean Affecting a Service:** Modify a boolean (e.g., `httpd_can_network_connect`) that impacts a service, then test the service.
27. **Edit the SELinux Configuration File:** Open and modify `/etc/selinux/config` to change SELinux mode settings.
28. **Reboot to Apply Configuration Changes:** Reboot the system and verify that the changes in SELinux mode are applied.
29. **Add a New Port Context:** Use `semanage port` to add a context for a new TCP port (choose an available port).
30. **Verify the New Port Context:** List port contexts to confirm that your new port has been added.
31. **Confirm Targeted Policy Usage:** Ensure that your system is using the targeted policy by checking the configuration.
32. **Identify an Unconfined Process:** Use process listing commands to find processes running with an unconfined SELinux context.
33. **Simulate a Denial Scenario:** Create a situation (e.g., incorrect file context for a service) that triggers an SELinux denial and inspect the audit log.
34. **Use audit2allow on Denial Logs:** Process the denial logs with `audit2allow` to generate a sample policy rule.
35. **Load the Generated Policy Rule:** Install the custom policy module from Task 34 and verify that the denial is resolved.
36. **Compare System Behavior with SELinux Disabled:** Temporarily disable SELinux and note differences in system behavior (be cautious and revert this change).
37. **Inspect a Web Server Directory:** Check the SELinux file contexts in a web server directory (e.g., `/var/www/html`).
38. **Change a Web Directory Context:** Modify the security context of the web server directory using `chcon` and verify the change.
39. **Create a Custom File Labeling Policy:** Use `semanage fcontext` to define a custom labeling rule for a new directory.
40. **Apply and Verify the Custom Labeling Policy:** Run the restoration command on the directory and confirm the applied labels.
41. **Recursively List File Contexts:** Use a command to list SELinux contexts for all files in a directory (e.g., `/var/www`).
42. **Simulate a Process Context Modification:** If applicable, simulate changing a process’s context (e.g., via startup options) and check the result.
43. **Quickly Check the Current Mode:** Use `getenforce` to display the current SELinux mode.
44. **Toggle and Verify an SELinux Boolean:** Change a boolean’s state and then confirm its value with the appropriate query command.
45. **List Detailed SELinux Policy Modules:** Run a command that lists policy modules with detailed information.
46. **Simulate a Service Denial:** Attempt to start a service that SELinux restricts, then review the resulting denial in the audit log.
47. **Reset a Misconfigured File Context:** Create a file with an incorrect context and then use the restoration command to reset it.
48. **Practice Changing Modes with setenforce:** Toggle SELinux modes using `setenforce` and verify each change.
49. **Inspect Network Port Contexts:** List all network port contexts to understand which ports are associated with which types.
50. **Modify and Revert a File Context:** Change the context of a non-critical file, then revert it back to the default.
51. **Verify a Service’s SELinux Context:** Check that a service (e.g., `nginx` or `sshd`) is running with the correct context using process listing.
52. **Investigate SELinux Log Errors:** Use audit tools to examine SELinux log entries for errors and document your findings.
53. **Create a Custom Policy Rule for an Application:** Simulate an application denial and generate a custom rule using `audit2allow`.
54. **Compare Targeted vs. Strict Policies:** Research and document differences between targeted and strict SELinux policies using command outputs and file inspection.
55. **Explore SELinux Policy Files:** Navigate to `/etc/selinux` and review the available policy files and directories.
56. **Backup Current SELinux Modules:** Create a backup (or log) of the list of installed SELinux modules.
57. **Simulate and Resolve a File Access Block:** Create a scenario where file access is blocked by SELinux and resolve it using appropriate context commands.
58. **Test File Access with SELinux Enforced vs. Permissive:** Compare file access behavior in enforcing mode versus permissive mode.
59. **Configure SELinux for a Container:** If you have Docker or another container engine, launch a container with SELinux enabled and verify its file contexts.
60. **Document Your SELinux Changes:** Create and maintain a log file documenting all SELinux configuration changes made during these tasks.


---

# Text Manipulation Tool Tasks

## tr
1. Convert all uppercase letters in a file to lowercase using `tr`.
2. Convert all lowercase letters in a file to uppercase using `tr`.
3. Replace all spaces with underscores using `tr`.
4. Delete all digits from the input using `tr`.
5. Replace newline characters with commas using `tr`.
6. Replace all vowels with `*` using `tr`.
7. Delete all punctuation marks using `tr`.
8. Replace all occurrences of the letter “a” with “z” using `tr`.
9. Delete all non‐alphabetic characters using `tr`.
10. Squeeze multiple spaces into a single space using `tr -s`.
11. Replace tab characters with four spaces using `tr`.
12. Delete all instances of the character “e” using `tr`.
13. Replace all digits with “#” using `tr`.
14. Remove all control characters using `tr -d` (with an appropriate set).
15. Translate the set `[abc]` to `[xyz]` using `tr`.
16. Squeeze repeated alphabetic characters using `tr -s` (applied to a set).
17. Perform ROT13 encoding using `tr`.
18. Remove all vowels from a file using `tr -d`.
19. Replace the vowels “aeiou” with “@@@@@” using `tr`.
20. Replace all punctuation with a space using `tr`.
21. Convert carriage returns (CR) to line feeds (LF) using `tr`.
22. Replace a specific symbol (e.g. “$”) with another (e.g. “%”) using `tr`.
23. Delete all occurrences of the letter “x” using `tr`.
24. Replace lowercase letters from “m” through “z” with their uppercase equivalents using `tr`.
25. Remove characters outside the printable ASCII range using `tr -cd`.
26. Map the characters “ABC” to “123” using `tr`.
27. Squeeze multiple exclamation marks into one using `tr -s`.
28. Replace newline characters with a space using `tr`.
29. Replace underscores with hyphens using `tr`.
30. Remove all non‐numeric characters using `tr -cd`.
31. Replace multiple punctuation marks with a single period using `tr` (possibly in two steps).
32. Change tab characters to a single space using `tr`.
33. Delete all non‐alphanumeric characters using `tr -cd`.
34. Remove extra white space by squeezing spaces using `tr -s`.
35. Convert CRLF to LF by deleting carriage returns using `tr -d`.
36. Substitute a range of characters with a given symbol using `tr` (e.g. map all a–z to “*”).
37. Remove vowels and punctuation simultaneously using `tr -d`.
38. Replace multiple spaces with a single underscore using `tr` (using a pipeline if needed).
39. Remove all non‐ASCII characters using `tr -cd`.
40. Convert each line of a file to lowercase using `tr`.
41. Delete all dash (“-”) characters using `tr -d`.
42. Remove all occurrences of the letter “b” using `tr -d`.
43. Delete both digits and punctuation using `tr -d '0-9[:punct:]'`.
44. Squeeze repeated digits into a single digit using `tr -s`.
45. Map hexadecimal letters (a–f) to uppercase using `tr`.
46. Replace newline characters with semicolons using `tr`.
47. Delete all non‐printable characters using `tr -cd '[:print:]'`.
48. Translate the set “123456” to “abcdef” using `tr`.
49. Delete a range of characters (e.g. from “p” to “t”) using `tr -d`.
50. Map one character set to another to simulate encoding conversion using `tr`.

## cut
1. Extract the first comma-separated field from each line using `cut -d',' -f1`.
2. Extract the second tab-delimited field using `cut -d$'\t' -f2`.
3. Extract characters 1–5 from each line using `cut -c1-5`.
4. Extract fixed-width columns from a file using `cut -c…` (specify column ranges as needed).
5. Extract the first 10 characters from each line using `cut -c1-10`.
6. Extract the first colon-separated field using `cut -d':' -f1`.
7. Extract the last field of each line (assume a known number of fields) using `cut`.
8. Extract fields separated by spaces using `cut -d' '`.
9. Extract bytes 1–3 from each line using `cut -b1-3`.
10. Extract a specific range of characters using `cut -c` with custom positions.
11. Extract the first word from each line using `cut -d' ' -f1`.
12. Extract all fields except the first using `cut --complement`.
13. Remove the first field from a colon-separated file using `cut --complement -d':' -f1`.
14. Extract multiple fields (e.g. fields 2 and 3) using `cut -d',' -f2,3`.
15. Extract a date field from a log file using `cut` with the appropriate delimiter.
16. Extract and display a specific field for further processing using `cut`.
17. Use `cut` to extract fields when the delimiter is a semicolon using `-d';'`.
18. Extract a substring from each line using `cut -c` with the proper range.
19. Extract the first field from a pipe-delimited file using `cut -d'|' -f1`.
20. Extract the second and third fields using `cut -d',' -f2-3`.
21. Extract fields from a CSV file (assuming no embedded commas) using `cut -d','`.
22. Extract fields from a tab-delimited file using `cut -f` (the default delimiter is tab).
23. Remove a specific field from each line using `cut --complement` with the proper delimiter.
24. Extract the first 2 bytes of each line using `cut -b1-2`.
25. Extract a specific range of bytes using `cut -b` with the desired positions.
26. Extract the username field from `/etc/passwd` using `cut -d':' -f1`.
27. Extract the home directory from `/etc/passwd` using `cut -d':' -f6`.
28. Extract the shell field from `/etc/passwd` using `cut -d':' -f7`.
29. Extract the domain name from an email address field using `cut -d'@' -f2`.
30. Extract the file extension from filenames using `cut -d'.' -f2`.
31. Extract the first word from each line (space-delimited) using `cut -d' ' -f1`.
32. Extract a middle substring from each line using `cut -c5-15`.
33. Extract the last N characters from each line using a pipeline (e.g. with `rev` and `cut`).
34. Extract a specific field when lines use a complex delimiter (e.g. `cut -d'|' -f2`).
35. Combine multiple fields into one output using `cut -d',' -f1,3`.
36. Extract log file columns using `cut -d' '` (with appropriate field numbers).
37. Extract the third field from a space-delimited log file using `cut -d' ' -f3`.
38. Extract a fixed column range from each line using `cut -c10-20`.
39. Extract specific fields from a colon-separated configuration file using `cut -d':'`.
40. Extract the second field from a colon-separated file using `cut -d':' -f2`.
41. Remove the first three characters from each line using `cut -c4-`.
42. Extract fixed-width segments from each line using `cut -c` with proper ranges.
43. Extract a substring from each line using `cut -c` (choose start–end positions).
44. Remove a prefix from each line by cutting off a fixed number of characters using `cut -c`.
45. Extract fields from a semicolon-separated file using `cut -d';'`.
46. Extract a specific byte range (e.g. 5–10) using `cut -b5-10`.
47. Extract fields from a pipe-delimited file using `cut -d'|'`.
48. Output specific columns from a text file using `cut` (choose delimiter and fields).
49. Extract fields even if some lines have missing delimiters using `cut`.
50. Extract a field and combine it with sort (demonstrating pipelining) using `cut` piped to `sort`.

## nl
1. Number all lines in a file using `nl file.txt`.
2. Number non-empty lines only using `nl -b a file.txt`.
3. Number lines starting from 10 using `nl -v 10 file.txt`.
4. Number lines with an increment of 5 using `nl -i 5 file.txt`.
5. Number lines with a custom format using `nl -n rz file.txt`.
6. Number lines and pad with zeros using `nl -n rz file.txt`.
7. Skip blank lines while numbering using `nl -b t file.txt`.
8. Number lines with a custom delimiter using `nl -s ': ' file.txt`.
9. Use a custom numbering format (e.g. left justified) with `nl` options.
10. Number lines of a log file using `nl logfile.txt`.
11. Add line numbers to a script file using `nl script.sh`.
12. Number lines and (simulate) print a header using `nl file.txt`.
13. Format numbering with a custom width using `nl -w 3 file.txt`.
14. Number lines with a colon separator using `nl -s ': ' file.txt`.
15. Number lines using an alternate numbering style with `nl -n ln file.txt`.
16. Number lines using a custom starting value using `nl -v 1 file.txt`.
17. Remove any preexisting numbers by re‐numbering using `nl file.txt`.
18. Start numbering at 100 using `nl -v 100 file.txt`.
19. Right justify line numbers using `nl -n r file.txt`.
20. Left justify line numbers using `nl -n l file.txt`.
21. Number lines with fixed width using `nl -w 4 file.txt`.
22. Redirect the numbered output to a new file using `nl file.txt > numbered.txt`.
23. Number lines read from standard input using `cat file.txt | nl`.
24. Number lines and write the result to another file using `nl file.txt > output.txt`.
25. Ignore leading whitespace and number lines using `nl -b p file.txt`.
26. Insert a delimiter between line numbers and text using `nl -s ' | ' file.txt`.
27. (Simulate) Number lines with a custom prefix using `nl` with formatting.
28. Number lines in a file that contains blank lines using `nl -b t file.txt`.
29. (Simulate) Add a suffix to line numbers using `nl` (combine with other tools).
30. Combine `grep` and `nl` to number only matching lines.
31. Use `nl` to number lines for diff comparison.
32. (Simulate) Number paragraphs in a text file using `nl` (may need preprocessing).
33. (Simulate) Number only lines that contain a specific pattern (pre-filter then nl).
34. Number lines with a dash as a separator using `nl -s ' - ' file.txt`.
35. (Simulate) Add a custom prefix to line numbers using formatting.
36. Number lines with a custom column width using `nl -w 5 file.txt`.
37. Remove extra spaces (preprocess) and then number lines using `nl`.
38. (Simulate) Number lines with a hash prefix using `nl` (combine with sed).
39. Start numbering at 100 using `nl -v 100 file.txt`.
40. Number lines in a multi‐column file using `nl`.
41. Number lines from piped input using `cat file.txt | nl`.
42. (Simulate) Number lines in reverse order by combining `tac` and `nl`.
43. Number lines with indentation using appropriate `nl` formatting.
44. (Simulate) Append trailing line numbers (combine with paste).
45. (Simulate) Use a custom fill character (with sed) after numbering.
46. Skip comment lines (pre-filter then nl) and number the rest.
47. Number lines in a file with leading spaces using `nl -b t file.txt`.
48. Merge the original file with numbered output using `paste`.
49. Combine `nl` with `sort` to sort the numbered lines.
50. Use `nl` to number lines and then compare with another file’s numbered output.

## od
1. Display a file in octal format using `od -o file.txt`.
2. Display file contents in hexadecimal using `od -x file.txt`.
3. Display file contents in decimal using `od -d file.txt`.
4. Display file contents as ASCII characters using `od -c file.txt`.
5. Display the first 16 bytes of a file using `od -N 16 file.txt`.
6. Display file contents in “binary” (simulate using hex output) with `od -t x1 file.txt`.
7. Set the number of bytes per line using `od -w16 file.txt`.
8. Display file offsets using `od -An file.txt`.
9. Display file contents in canonical format using `od -c file.txt`.
10. Display both hex and ASCII using `od -Ax -tx1z file.txt`.
11. (Simulate) Display in little-endian order using appropriate options.
12. Use a custom format specifier with `od -t o2 file.txt`.
13. Display file contents with address offsets using `od -A x file.txt`.
14. Start displaying a file from a given offset using `od -j 10 file.txt`.
15. (Simulate) Display in octal and decimal simultaneously using two commands.
16. Set the number of columns using `od -w8 file.txt`.
17. Group bytes in hex with two-byte groups using `od -t x2 file.txt`.
18. Skip a specified number of bytes using `od -j 20 file.txt`.
19. Limit output to 32 bytes using `od -N 32 file.txt`.
20. Display in octal with canonical representation using `od -c file.txt`.
21. Use a custom format string with the `-t` option in `od`.
22. Show ASCII alongside hex using `od -tx1z file.txt`.
23. Dump a binary file in hex using `od -A x -t x1 file.bin`.
24. Dump a binary file in octal using `od -A o -t o1 file.bin`.
25. Output file contents in blocks (64 bytes per block) using `od -N 64 file.txt`.
26. Display only the header bytes (first 8 bytes) using `od -N 8 file.txt`.
27. Group output in two-byte blocks using `od -t x2 file.txt`.
28. Show decimal values for each byte using `od -t d1 file.txt`.
29. (Simulate) Display hex values with a delimiter (preprocess if needed).
30. Display octal output with addresses using `od -A x -t o1 file.txt`.
31. (Combine) Hex and octal output by running two od commands.
32. Use a custom format specifier (e.g. `od -t x4 file.txt`).
33. Display hex output with decimal offsets using `od -A d -t x1 file.txt`.
34. Display octal output with 8 bytes per line using `od -w8 -t o1 file.txt`.
35. Display hex output with 16 bytes per line using `od -w16 -t x1 file.txt`.
36. Combine hex and ASCII output using `od -tx1z file.txt`.
37. Start output from a specific offset using `od -j 5 file.txt`.
38. Format output in a structured style using `od` (with appropriate options).
39. Ensure the output ends with a newline (default behavior).
40. Skip an initial header using `od -j 10 file.txt`.
41. Display hex without addresses using `od -An -t x1 file.txt`.
42. Group output by 2 bytes using `od -t x2 file.txt`.
43. (Combine) Hex and octal options as needed.
44. Redirect output to another file using `od file.txt > dump.txt`.
45. Set a custom block size using `od -N 32 file.txt`.
46. Display in different numeral bases by changing the `-t` option.
47. Set a custom column width using `od -w8 file.txt`.
48. Output in a compact format using `od -v file.txt`.
49. Use custom formatting flags with `od` as needed.
50. Display file in hex with offset in hex using `od -Ax -tx1 file.txt`.

## sed
1. Replace the first occurrence of “foo” with “bar” using:
   `sed 's/foo/bar/' file.txt`
2. Replace all occurrences of “cat” with “dog” using:
   `sed 's/cat/dog/g' file.txt`
3. Delete lines containing “error” using:
   `sed '/error/d' file.txt`
4. Insert a line “Header” before lines matching “START” using:
   `sed '/START/i Header' file.txt`
5. Append a line “Footer” after lines matching “END” using:
   `sed '/END/a Footer' file.txt`
6. Perform an in-place substitution of “old” with “new” using:
   `sed -i 's/old/new/g' file.txt`
7. Print only lines containing “success” using:
   `sed -n '/success/p' file.txt`
8. Perform multiple substitutions in one command using:
   `sed -e 's/foo/bar/g' -e 's/baz/qux/g' file.txt`
9. Replace “apple” with “orange” only on line 3 using:
   `sed '3s/apple/orange/' file.txt`
10. Delete all blank lines using:
    `sed '/^$/d' file.txt`
11. Replace all digits with “#” using:
    `sed 's/[0-9]/#/g' file.txt`
12. Insert “Start of File” at the beginning using:
    `sed '1i Start of File' file.txt`
13. Append “End of File” at the end using:
    `sed '$a End of File' file.txt`
14. Change the delimiter (using `|` instead of `/`) with:
    `sed 's|foo|bar|g' file.txt`
15. Replace “test” with “exam” ignoring case using GNU sed:
    `sed 's/test/exam/Ig' file.txt`
16. Print matching lines with their line numbers using a two‑step command:
    `sed -n '=' file.txt | sed 'N;s/\n/ /'`
17. Remove leading and trailing whitespace using:
    `sed 's/^[ \t]*//;s/[ \t]*$//' file.txt`
18. Duplicate each line using:
    `sed 'p' file.txt`
19. Delete the first line using:
    `sed '1d' file.txt`
20. Delete the last line using:
    `sed '$d' file.txt`
21. Replace “foobar” with “foobaz” using a captured group with:
    `sed 's/\(foo\)bar/\1baz/' file.txt`
22. Perform a global substitution of “old” with “new” using:
    `sed 's/old/new/g' file.txt`
23. Insert a blank line after each line using:
    `sed 'G' file.txt`
24. Comment out lines containing “DEBUG” using:
    `sed '/DEBUG/s/^/# /' file.txt`
25. Uncomment lines starting with “#” using:
    `sed 's/^# //' file.txt`
26. Swap the words “first” and “second” using:
    `sed 's/\(first\)\(.*\)\(second\)/\3\2\1/' file.txt`
27. Remove duplicate spaces using:
    `sed 's/  */ /g' file.txt`
28. Add line numbers to each line using:
    `sed = file.txt | sed 'N;s/\n/\t/'`
29. Replace tab characters with four spaces using:
    `sed 's/\t/    /g' file.txt`
30. Change the case of “hello” to uppercase using GNU sed:
    `sed 's/hello/\U&/g' file.txt`
31. Extract a substring (matching “pattern”) using:
    `sed -n 's/.*\(pattern\).*/\1/p' file.txt`
32. Replace punctuation with “~” using:
    `sed 's/[[:punct:]]/~/g' file.txt`
33. Insert “Line Start” at line 5 using:
    `sed '5i Line Start' file.txt`
34. Remove all digits using:
    `sed 's/[0-9]//g' file.txt`
35. Replace multiple spaces with a single space using:
    `sed 's/ \{2,\}/ /g' file.txt`
36. Delete lines starting with a digit using:
    `sed '/^[0-9]/d' file.txt`
37. Convert a CSV file by replacing commas with semicolons using:
    `sed 's/,/;/g' file.csv`
38. Remove leading whitespace using:
    `sed 's/^[ \t]*//' file.txt`
39. Delete lines that do not contain “INFO” using:
    `sed '/INFO/!d' file.txt`
40. Replace only the second occurrence of “foo” per line (using a more complex script).
41. Append a suffix “ -- end” to each line using:
    `sed 's/$/ -- end/' file.txt`
42. Remove the suffix “.txt” from filenames using:
    `sed 's/\.txt$//' file.txt`
43. Add a prefix “START: ” to each line using:
    `sed 's/^/START: /' file.txt`
44. Remove the prefix “DEBUG: ” from each line using:
    `sed 's/^DEBUG: //' file.txt`
45. Enclose the word “error” in brackets using:
    `sed 's/error/[error]/g' file.txt`
46. Replace “begin” with “start” only at the beginning of a line using:
    `sed 's/^begin/start/' file.txt`
47. Replace “end” with “finish” only at the end of a line using:
    `sed 's/end$/finish/' file.txt`
48. Merge two consecutive lines into one using:
    `sed 'N;s/\n/ /' file.txt`
49. Split a long line at each comma (inserting a newline) using:
    `sed 's/,/,\n/g' file.txt`
50. Remove all non-alphanumeric characters using:
    `sed 's/[^a-zA-Z0-9]//g' file.txt`

## awk
1. Print the first field of each line using:
   `awk '{print $1}' file.txt`
2. Print the second field using:
   `awk '{print $2}' file.txt`
3. Print lines where the third field is greater than 100 using:
   `awk '$3 > 100' file.txt`
4. Sum the values in the second column using:
   `awk '{sum+=$2} END {print sum}' file.txt`
5. Calculate the average of the first column using:
   `awk '{sum+=$1; count++} END {print sum/count}' file.txt`
6. Print the last field of each line using:
   `awk '{print $NF}' file.txt`
7. Print only lines matching “success” using:
   `awk '/success/' file.txt`
8. Specify a comma as the field separator using:
   `awk -F, '{print $1}' file.txt`
9. Print a header then the file contents using:
   `awk 'BEGIN {print "Header"} {print}' file.txt`
10. Print line numbers along with each line using:
    `awk '{print NR, $0}' file.txt`
11. Count the number of lines using:
    `awk 'END {print NR}' file.txt`
12. Print lines where the first field matches a regex using:
    `awk '$1 ~ /pattern/' file.txt`
13. Replace the second field with a computed value using:
    `awk '{$2 = $2 * 2; print}' file.txt`
14. Reformat a CSV file using:
    `awk -F, '{print $1 " - " $2}' file.csv`
15. Print the first and third fields using:
    `awk '{print $1, $3}' file.txt`
16. Print fields in reverse order using:
    `awk '{for(i=NF;i>0;i--) printf $i " "; print ""}' file.txt`
17. Print fields separated by a colon using:
    `awk 'BEGIN {OFS=":"} {print $1, $2}' file.txt`
18. Filter lines based on multiple conditions using:
    `awk '$1=="foo" && $2>50' file.txt`
19. Print only non-empty lines using:
    `awk 'NF' file.txt`
20. Print the sum and average of a numeric column using:
    `awk '{sum+=$2; count++} END {print sum, sum/count}' file.txt`
21. Print numbers with two‑decimal formatting using:
    `awk '{printf "%.2f\n", $1}' file.txt`
22. Count occurrences of “error” using:
    `awk '/error/ {count++} END {print count}' file.txt`
23. Print lines conditionally using:
    `awk '{if($1=="foo") print $0}' file.txt`
24. Process a tab-delimited file using:
    `awk -F'\t' '{print $1}' file.txt`
25. Print only unique lines using:
    `awk '!seen[$0]++' file.txt`
26. Perform arithmetic on fields using:
    `awk '{print $1 * $2}' file.txt`
27. Concatenate the first two fields with a hyphen using:
    `awk '{print $1 "-" $2}' file.txt`
28. Print fields with fixed width using:
    `awk '{printf "%-10s %-10s\n", $1, $2}' file.txt`
29. Extract a column from a log file using:
    `awk '{print $3}' file.txt`
30. (Simulate) Format dates from a file using an appropriate awk script.
31. Count words in a file using:
    `awk '{ total += NF } END { print total }' file.txt`
32. Print only the header row of a CSV file using:
    `awk 'NR==1 {print}' file.csv`
33. Skip the header row and process the rest using:
    `awk 'NR>1 {print}' file.csv`
34. Replace commas with spaces using:
    `awk '{gsub(/,/, " "); print}' file.txt`
35. Print lines where the number of fields equals 5 using:
    `awk 'NF==5' file.txt`
36. Print the first two columns separated by a colon using:
    `awk '{print $1 ":" $2}' file.txt`
37. Multiply the first and second fields and print the result using:
    `awk '{print $1 * $2}' file.txt`
38. Print a field as a percentage using:
    `awk '{printf "%.2f%%\n", $1}' file.txt`
39. Convert a field to uppercase using:
    `awk '{print toupper($1)}' file.txt`
40. Convert a field to lowercase using:
    `awk '{print tolower($1)}' file.txt`
41. Compute a running total for a column using:
    `awk '{sum+=$1; print sum}' file.txt`
42. Print rows where the first field is empty using:
    `awk '$1==""' file.txt`
43. Print the maximum value in the first column using:
    `awk 'BEGIN {max=0} {if($1>max) max=$1} END {print max}' file.txt`
44. Print the minimum value in the first column using:
    `awk 'BEGIN {min=999999} {if($1<min) min=$1} END {print min}' file.txt`
45. Print lines where the second field equals a specific value using:
    `awk '$2=="value"' file.txt`
46. Print a formatted report using:
    `awk '{printf "Field1: %s, Field2: %s\n", $1, $2}' file.txt`
47. Output data in JSON format (simulated) using:
    `awk 'BEGIN {print "["} {print "{\"field\":\"" $1 "\"},"} END {print "]"}' file.txt`
48. Process a file and save the output using:
    `awk '{print $1}' file.txt > output.txt`
49. Print the second field from a colon-separated file using:
    `awk -F: '{print $2}' file.txt`
50. Process a file and perform multiple actions using:
    `awk '{print $1; print $2}' file.txt`

## sort
1. Sort a file alphabetically using:
   `sort file.txt`
2. Sort a file in reverse order using:
   `sort -r file.txt`
3. Sort a file numerically using:
   `sort -n file.txt`
4. Sort a file by the second field using:
   `sort -k2 file.txt`
5. Sort a CSV file by the third column using:
   `sort -t',' -k3 file.csv`
6. Sort lines ignoring case using:
   `sort -f file.txt`
7. Sort lines by length (using awk to prepend length) using:
   `awk '{print length, $0}' file.txt | sort -n | cut -d" " -f2-`
8. Sort a file and remove duplicate lines using:
   `sort -u file.txt`
9. Sort a file with a custom delimiter using:
   `sort -t':' -k2 file.txt`
10. Sort a file based on a numeric field using:
    `sort -t',' -k2n file.txt`
11. Sort lines then reverse the order using:
    `sort file.txt | sort -r`
12. Sort in human-numeric order using:
    `sort -h file.txt`
13. Sort a tab-delimited file by the third column using:
    `sort -t$'\t' -k3 file.txt`
14. Sort version numbers using:
    `sort -V file.txt`
15. Sort by multiple keys using:
    `sort -k1,1 -k2,2 file.txt`
16. (Simulate) Sort lines based on the last field by first extracting it.
17. Sort lines while preserving the order of duplicates using:
    `sort -s file.txt`
18. Sort a file with blank lines at the end using:
    `sort file.txt`
19. Sort a file and output only unique lines using:
    `sort -u file.txt`
20. Sort a file ignoring leading blanks using:
    `sort -b file.txt`
21. Sort in parallel (if supported) using:
    `sort --parallel=4 file.txt`
22. Write sorted output to a new file using:
    `sort file.txt -o sorted.txt`
23. Check if a file is already sorted using:
    `sort -c file.txt`
24. Use a temporary directory with sort using:
    `sort -T /tmp file.txt`
25. Sort a file with mixed numeric and alphabetic keys (use appropriate options).
26. Sort a file containing dates using:
    `sort -k1,1M file.txt`
27. Sort by month name order using:
    `sort -k1,1M file.txt`
28. Sort ignoring case and numerically using:
    `sort -f -n file.txt`
29. (Simulate) Sort by the last character (requires preprocessing).
30. Sort by the first three characters using:
    `sort -k1.1,1.3 file.txt`
31. Sort with a custom locale using:
    `LC_ALL=C sort file.txt`
32. Perform a stable sort using:
    `sort -s file.txt`
33. Sort a file with zero-terminated lines using:
    `sort -z file.txt`
34. Reverse sort on a numeric column using:
    `sort -k2,2nr file.txt`
35. Sort a file and then add line numbers using:
    `sort file.txt | nl`
36. (Simulate) Merge sort using:
    `sort --merge file.txt`
37. (Simulate) Sort while ignoring non-printable characters.
38. Sort by word count per line (using awk to count words) then sort.
39. (Note: Random sort isn’t done with sort; use shuf instead.)
40. Sort by multiple comma-separated fields using:
    `sort -t',' -k1,1 -k2,2 file.txt`
41. Sort a file containing mixed numeric and string data using:
    `sort -n file.txt`
42. Sort ignoring case differences using:
    `sort -f file.txt`
43. (Simulate) Sort ignoring punctuation (preprocess if needed).
44. Reverse sort and output to another file using:
    `sort -r file.txt -o rev_sorted.txt`
45. (Simulate) Sort and then extract the first field using a pipeline.
46. (Simulate) Sort using a custom comparator (use available options).
47. Enable debug mode in sort using:
    `sort --debug file.txt`
48. Sort with human-readable numbers using:
    `sort -h file.txt`
49. (Simulate) Sort with a custom dictionary order using available keys.
50. Sort based on the first 5 characters using:
    `sort -k1.1,1.5 file.txt`

## split
1. Split a file into equal-sized parts (100 lines each) using:
   `split -l 100 file.txt`
2. Split a file into parts with 100 lines each using:
   `split -l 100 file.txt`
3. Split a file into parts with a specified prefix using:
   `split -l 50 -a 2 file.txt part_`
4. Split a file by bytes (1 KB per part) using:
   `split -b 1K file.txt`
5. Split a large log file into smaller files using:
   `split -l 1000 logfile.txt`
6. Split a file and add numeric suffixes using:
   `split -d file.txt`
7. Split a file with a custom suffix length (3 characters) using:
   `split -a 3 file.txt`
8. Split a file at a specific line number (200 lines per file) using:
   `split -l 200 file.txt`
9. Split a file into 1 KB chunks using:
   `split -b 1K file.txt`
10. (Simulate) Split a file into parts while preserving the header (requires extra scripting).
11. Split a file into a specified number of files using:
    `split -n 5 file.txt`
12. Split a file using a numeric suffix using:
    `split -d file.txt`
13. Split a file into parts of 500 bytes each using:
    `split -b 500 file.txt`
14. Split a file with a custom prefix using:
    `split -d -a 2 file.txt mypart_`
15. Split a file and store the parts in a specific directory (change directory first).
16. Split a file with verbose output using:
    `split -v file.txt`
17. Split a file and then list the created parts using:
    `split file.txt && ls x*`
18. Split a file into 5 equal parts using:
    `split -n 5 file.txt`
19. Split a file with a numeric suffix length of 3 using:
    `split -d -a 3 file.tx

