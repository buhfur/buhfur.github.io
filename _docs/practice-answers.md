


## Selinux answers 



# SELinux Hands-On Practice Task Answers

Below are sample answers, commands, and explanations for each task. Use these as a guide to verify your work.

1. **Check SELinux Status:**  
   - **Command:** `sestatus`  
   - **Explanation:** Displays the current SELinux mode (enforcing, permissive, or disabled) and policy type.

2. **Switch to Permissive Mode Temporarily:**  
   - **Command:** `setenforce 0`  
   - **Explanation:** Temporarily sets SELinux to permissive mode (changes revert on reboot).

3. **Switch Back to Enforcing Mode:**  
   - **Command:** `setenforce 1`  
   - **Explanation:** Re-enables SELinux enforcement immediately.

4. **Display File Security Context:**  
   - **Command:** `ls -Z /etc/passwd`  
   - **Explanation:** Lists the security context for `/etc/passwd`.

5. **Change File Context Using chcon:**  
   - **Command:**  
     ```bash
     touch /tmp/testfile
     chcon -t user_home_t /tmp/testfile
     ```  
   - **Explanation:** Creates a test file and assigns it a new SELinux type.

6. **Restore File Context to Default:**  
   - **Command:** `restorecon /tmp/testfile`  
   - **Explanation:** Reverts the file’s SELinux context to its default as defined by policy.

7. **List All SELinux Booleans:**  
   - **Command:** `getsebool -a`  
   - **Explanation:** Displays all SELinux boolean settings.

8. **Temporarily Modify an SELinux Boolean:**  
   - **Command:** `setsebool httpd_enable_homedirs on`  
   - **Explanation:** Changes the boolean temporarily (does not persist after reboot).

9. **Permanently Modify an SELinux Boolean:**  
   - **Command:** `setsebool -P httpd_enable_homedirs on`  
   - **Explanation:** The `-P` flag ensures the change persists across reboots.

10. **Add a New File Context Rule:**  
    - **Command:**  
      ```bash
      semanage fcontext -a -t my_custom_t "/home/testdir(/.*)?"
      ```  
    - **Explanation:** Adds a file context rule for all files under `/home/testdir`.

11. **Apply the New File Context Rule:**  
    - **Command:** `restorecon -R /home/testdir`  
    - **Explanation:** Applies the new context rule recursively in the directory.

12. **Check Active SELinux Policies:**  
    - **Command:** `sestatus`  
    - **Explanation:** Confirms whether the system is using the targeted or strict policy.

13. **List Running Processes with SELinux Contexts:**  
    - **Command:** `ps -eZ`  
    - **Explanation:** Displays all running processes along with their SELinux contexts.

14. **Inspect a Specific Process Context:**  
    - **Command:**  
      ```bash
      pidof sshd
      cat /proc/<pid>/attr/current
      ```  
    - **Explanation:** Replace `<pid>` with the actual process ID to view its context.

15. **Map a Linux User to an SELinux User:**  
    - **Command:** `semanage login -a -s user_u username`  
    - **Explanation:** Maps the Linux user `username` to the SELinux user `user_u`.

16. **List SELinux User Mappings:**  
    - **Command:** `semanage login -l`  
    - **Explanation:** Lists all current mappings between Linux and SELinux users.

17. **Generate an SELinux Audit Report:**  
    - **Command:** `sealert -a /var/log/audit/audit.log`  
    - **Explanation:** Processes the audit log to provide human-readable suggestions.

18. **Filter SELinux Audit Messages:**  
    - **Command:** `ausearch -m avc -ts recent`  
    - **Explanation:** Filters audit messages for SELinux access vector cache (AVC) denials.

19. **Generate a Custom Policy Module:**  
    - **Command:** `audit2allow -a -M custom_module`  
    - **Explanation:** Analyzes recent denials and generates a module named `custom_module`.

20. **Load the Custom Policy Module:**  
    - **Command:** `semodule -i custom_module.pp`  
    - **Explanation:** Installs the custom policy module into the SELinux policy.

21. **Remove an SELinux Module:**  
    - **Command:** `semodule -r custom_module`  
    - **Explanation:** Uninstalls the previously loaded module.

22. **Trigger a Full Filesystem Relabel:**  
    - **Command:**  
      ```bash
      touch /.autorelabel
      reboot
      ```  
    - **Explanation:** Creates a marker file so that on reboot, the system relabels all files.

23. **Create a Test File and Change Its Context:**  
    - **Command:**  
      ```bash
      echo "SELinux test" > /tmp/testfile2
      chcon -t user_home_t /tmp/testfile2
      ```  
    - **Explanation:** Creates a file and assigns a new SELinux type.

24. **Verify the File Context Change:**  
    - **Command:** `ls -Z /tmp/testfile2`  
    - **Explanation:** Confirms that the new context is applied.

25. **List Installed SELinux Modules:**  
    - **Command:** `semodule -l`  
    - **Explanation:** Lists all currently installed SELinux modules.

26. **Adjust a Boolean Affecting a Service:**  
    - **Command:**  
      ```bash
      setsebool -P httpd_can_network_connect on
      systemctl restart httpd
      ```  
    - **Explanation:** Enables network connections for Apache and restarts the service.

27. **Edit the SELinux Configuration File:**  
    - **Command:** `sudo nano /etc/selinux/config`  
    - **Explanation:** Modify the configuration (e.g., change SELINUX=enforcing to SELINUX=permissive) and save changes.

28. **Reboot to Apply Configuration Changes:**  
    - **Command:** `sudo reboot`  
    - **Explanation:** After reboot, use `sestatus` to verify that the new mode is active.

29. **Add a New Port Context:**  
    - **Command:** `semanage port -a -t http_port_t -p tcp 8080`  
    - **Explanation:** Adds a new TCP port (8080) to be managed under the `http_port_t` type.

30. **Verify the New Port Context:**  
    - **Command:** `semanage port -l | grep 8080`  
    - **Explanation:** Confirms that port 8080 is listed with the correct context.

31. **Confirm Targeted Policy Usage:**  
    - **Command:** `sestatus`  
    - **Explanation:** Look for “Policy from config file: targeted” to confirm usage.

32. **Identify an Unconfined Process:**  
    - **Command:** `ps -eZ | grep unconfined`  
    - **Explanation:** Lists processes running with an unconfined SELinux context.

33. **Simulate a Denial Scenario:**  
    - **Action:** Change the file context of a web file (e.g., assign a non-web type to an Apache file) and try to access it via the web server.  
    - **Verification:** Check `/var/log/audit/audit.log` for AVC denials.

34. **Use audit2allow on Denial Logs:**  
    - **Command:** `audit2allow -a`  
    - **Explanation:** Processes recent denial messages and outputs sample allow rules.

35. **Load the Generated Policy Rule:**  
    - **Command:** `audit2allow -a -M mypolicy && semodule -i mypolicy.pp`  
    - **Explanation:** Generates and installs a custom module to resolve the denial.

36. **Compare System Behavior with SELinux Disabled:**  
    - **Command:** `setenforce 0` (temporarily disable)  
    - **Action:** Test the same operation that previously generated a denial. Then re-enable SELinux with `setenforce 1`.

37. **Inspect a Web Server Directory:**  
    - **Command:** `ls -Z /var/www/html`  
    - **Explanation:** Displays SELinux contexts for all files in the web directory.

38. **Change a Web Directory Context:**  
    - **Command:** `chcon -R -t httpd_sys_content_t /var/www/html`  
    - **Explanation:** Sets the proper SELinux type for web content.

39. **Create a Custom File Labeling Policy:**  
    - **Command:**  
      ```bash
      semanage fcontext -a -t my_custom_t "/home/newdir(/.*)?"
      ```  
    - **Explanation:** Defines a custom file context for all files under `/home/newdir`.

40. **Apply and Verify the Custom Labeling Policy:**  
    - **Command:**  
      ```bash
      restorecon -R /home/newdir
      ls -Z /home/newdir
      ```  
    - **Explanation:** Applies the new policy and verifies the changes.

41. **Recursively List File Contexts:**  
    - **Command:** `ls -ZR /var/www`  
    - **Explanation:** Recursively displays SELinux contexts in the `/var/www` directory.

42. **Simulate a Process Context Modification:**  
    - **Action:** If supported, modify how a process is launched to use a different SELinux domain (this may involve custom scripts or container settings). Verify with `ps -eZ`.

43. **Quickly Check the Current Mode:**  
    - **Command:** `getenforce`  
    - **Explanation:** Outputs the current mode (Enforcing, Permissive, or Disabled).

44. **Toggle and Verify an SELinux Boolean:**  
    - **Command:**  
      ```bash
      setsebool ftp_home_dir on
      getsebool ftp_home_dir
      ```  
    - **Explanation:** Changes and then confirms the boolean’s value.

45. **List Detailed SELinux Policy Modules:**  
    - **Command:** `semodule -l`  
    - **Explanation:** Lists modules; additional inspection may require checking policy file details in `/etc/selinux`.

46. **Simulate a Service Denial:**  
    - **Action:** Attempt to start a service that is misconfigured (or has an incorrect file context).  
    - **Verification:** Review `/var/log/audit/audit.log` for denials.

47. **Reset a Misconfigured File Context:**  
    - **Command:**  
      ```bash
      chcon -t wrong_type /tmp/testfile3
      restorecon /tmp/testfile3
      ```  
    - **Explanation:** Resets the file’s context back to its default.

48. **Practice Changing Modes with setenforce:**  
    - **Command:**  
      ```bash
      setenforce 0
      getenforce
      setenforce 1
      getenforce
      ```  
    - **Explanation:** Demonstrates toggling between modes and verifying each change.

49. **Inspect Network Port Contexts:**  
    - **Command:** `semanage port -l`  
    - **Explanation:** Lists all network port contexts managed by SELinux.

50. **Modify and Revert a File Context:**  
    - **Command:**  
      ```bash
      chcon -t tmp_t /tmp/testfile4
      ls -Z /tmp/testfile4
      restorecon /tmp/testfile4
      ls -Z /tmp/testfile4
      ```  
    - **Explanation:** Changes the file context and then reverts it.

51. **Verify a Service’s SELinux Context:**  
    - **Command:** `ps -eZ | grep sshd`  
    - **Explanation:** Checks that the SSH service is running with the proper context.

52. **Investigate SELinux Log Errors:**  
    - **Command:** `ausearch -m avc -ts recent`  
    - **Explanation:** Inspects recent denial messages; document findings for troubleshooting.

53. **Create a Custom Policy Rule for an Application:**  
    - **Action:** Simulate an application denial, then run:  
      ```bash
      audit2allow -a -M app_policy
      semodule -i app_policy.pp
      ```  
    - **Explanation:** Generates and installs a policy rule to allow the previously blocked action.

54. **Compare Targeted vs. Strict Policies:**  
    - **Action:** Examine `/etc/selinux/config` and use `sestatus` to review the active policy. Document differences by referring to SELinux documentation.
    
55. **Explore SELinux Policy Files:**  
    - **Command:** `ls -l /etc/selinux`  
    - **Explanation:** Reviews the policy directories and files to understand policy structure.

56. **Backup Current SELinux Modules:**  
    - **Command:** `semodule -l > selinux_modules_backup.txt`  
    - **Explanation:** Saves the list of installed modules to a file for backup purposes.

57. **Simulate and Resolve a File Access Block:**  
    - **Action:** Create a file with an improper context, attempt access, note the denial, then fix it using `chcon` or `restorecon`.

58. **Test File Access with SELinux Enforced vs. Permissive:**  
    - **Action:** Compare file access (e.g., for a web server or custom application) while toggling between enforcing (`setenforce 1`) and permissive (`setenforce 0`) modes.

59. **Configure SELinux for a Container:**  
    - **Action:** Launch a Docker container with SELinux enabled (for example, using `--security-opt label=type:container_t`) and verify file contexts inside the container with `ls -Z`.

60. **Document Your SELinux Changes:**  
    - **Action:** Maintain a log file (e.g., `/home/youruser/selinux_changes.log`) where you record each change, command executed, and the outcome. You can use any text editor to create and update this log.

---

These tasks and answers provide a comprehensive hands-on approach to learning SELinux while preparing for your Linux+ exam. Happy practicing!

#############################################
# Linux System Management Cheat Sheet
#############################################

===========================
Topic 1: User & Group Management
===========================
1. Create a new user account with a specified home directory and default shell.
   Command: 
     useradd -m -d /home/username -s /bin/bash username

2. Set a password for the new user.
   Command:
     passwd username

3. Configure the new account so that the user must change the password upon first login.
   Command:
     chage -d 0 username

4. Create a new group and verify its entry in /etc/group.
   Command:
     groupadd groupname
     cat /etc/group | grep groupname

5. Add the new user to the newly created group.
   Command:
     usermod -aG groupname username

6. Remove an existing user from a specified group.
   Command:
     gpasswd -d username groupname

7. Display all user accounts on the system by examining /etc/passwd.
   Command:
     cat /etc/passwd

8. Display all groups present on the system.
   Command:
     cat /etc/group

9. Modify the default shell for an existing user account.
   Command:
     usermod -s /bin/zsh username

10. Lock a user account to prevent login.
    Command:
      passwd -l username

11. Unlock a previously locked user account.
    Command:
      passwd -u username

12. Remove a user account and optionally its home directory.
    Command:
      userdel -r username

13. Remove a group and verify that its memberships are updated.
    Command:
      groupdel groupname
      cat /etc/group | grep groupname

14. Verify user account details by inspecting /etc/passwd.
    Command:
      grep username /etc/passwd

15. Verify group information by inspecting /etc/group.
    Command:
      grep groupname /etc/group

16. Update a user’s details (e.g., comment field or home directory) using a command like usermod.
    Commands:
      usermod -c "Full Name" username
      usermod -d /new/home/username username

17. Create a system account for background services.
    Command:
      useradd -r -s /usr/sbin/nologin serviceuser

18. Write a script to batch add multiple users to a group.
    Example Script (batch_add.sh):
      #!/bin/bash
      for user in $(cat users.txt); do
        usermod -aG groupname "$user"
      done
      # Make executable: chmod +x batch_add.sh

19. Grant sudo access to a specific user by editing the sudoers file.
    Instruction:
      Use visudo and add:
        username ALL=(ALL) ALL

20. Back up the /etc/passwd and /etc/group files for recovery purposes.
    Commands:
      cp /etc/passwd /etc/passwd.bak
      cp /etc/group /etc/group.bak

===========================
Topic 2: Service Management with systemd
===========================
1. Create a shell script that writes the current date and time to a log file.
   Example Script (logtime.sh):
      #!/bin/bash
      date >> /var/log/datetime.log
      # Make executable: chmod +x logtime.sh

2. Build a systemd service unit file to execute the log script.
   Create file: /etc/systemd/system/logtime.service with:
      [Unit]
      Description=Log Current Date and Time

      [Service]
      ExecStart=/path/to/logtime.sh

3. Save the service unit file in /etc/systemd/system/ (see above).

4. Reload the systemd daemon to acknowledge the new service.
   Command:
      systemctl daemon-reload

5. Enable the service so that it starts automatically at boot.
   Command:
      systemctl enable logtime.service

6. Manually start the service and verify its operation.
   Commands:
      systemctl start logtime.service
      systemctl status logtime.service

7. Use systemctl to check the service status and review its logs.
   Commands:
      systemctl status logtime.service
      journalctl -u logtime.service

8. Develop a systemd timer unit that triggers the service on a set schedule (e.g., hourly).
   Create file: /etc/systemd/system/logtime.timer with:
      [Unit]
      Description=Run logtime.service hourly

      [Timer]
      OnCalendar=hourly
      Persistent=true

      [Install]
      WantedBy=timers.target

9. Enable and start the timer unit.
   Command:
      systemctl enable --now logtime.timer

10. Check the timer’s status and scheduled activations.
    Command:
      systemctl list-timers --all
      systemctl status logtime.timer

11. Modify the service unit to run under a specific user and group.
    In the [Service] section, add:
      User=username
      Group=groupname

12. Configure the service to automatically restart on failure.
    In the service unit file under [Service] add:
      Restart=on-failure

13. Set a working directory for the service in the unit file.
    In the service unit file under [Service] add:
      WorkingDirectory=/path/to/dir

14. Configure custom environment variables for the service.
    In the service unit file under [Service] add:
      Environment="VAR=value"

15. Establish a dependency between this service and another systemd unit.
    In the [Unit] section add:
      After=network.target   # (or the specific unit)

16. Create a custom systemd target and assign your service to it.
    Create file: /etc/systemd/system/mytarget.target with:
      [Unit]
      Description=Custom target for logtime service
    Then, in logtime.service add under [Install]:
      WantedBy=mytarget.target

17. Simulate a script failure to observe the restart behavior.
    Instruction:
      Modify the script to exit with a non-zero status to test Restart=on-failure.

18. Disable both the service and its timer.
    Command:
      systemctl disable --now logtime.service logtime.timer

19. Write a separate script that monitors and restarts the service if it stops unexpectedly.
    Example Script (monitor_service.sh):
      #!/bin/bash
      while true; do
        systemctl is-active --quiet logtime.service || systemctl restart logtime.service
        sleep 60
      done

20. Document all changes made to the service and timer configuration.
    Recommendation:
      Maintain a changelog (e.g., in /var/log/changelog.log or a version-controlled documentation file).

===========================
Topic 3: Scheduling Tasks with Cron
===========================
1. Write a script that backs up a designated directory.
   Example Script (backup.sh):
      #!/bin/bash
      tar -czf /backup/mydir_$(date +%F).tar.gz /path/to/directory
      # Make executable: chmod +x backup.sh

2. Schedule the backup script to run daily at midnight using cron.
   Crontab entry:
      0 0 * * * /path/to/backup.sh

3. Edit the current user’s crontab using the proper command.
   Command:
      crontab -e

4. Define environment variables within the crontab file.
   Example at the top of crontab:
      PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin

5. Modify a cron job to redirect its output to a specific log file.
   Example:
      0 0 * * * /path/to/backup.sh >> /var/log/backup.log 2>&1

6. Schedule a task to run every 15 minutes.
   Crontab entry:
      */15 * * * * /path/to/script.sh

7. Create a cron job that runs only on Saturdays and Sundays.
   Crontab entry:
      0 0 * * 6,0 /path/to/weekend_script.sh

8. Delete an existing cron job from the crontab.
   Instruction:
      Run “crontab -e”, remove the specific line, and save.

9. List all current cron jobs for the user.
   Command:
      crontab -l

10. Set up a cron job to run at system startup using the @reboot directive.
    Crontab entry:
      @reboot /path/to/startup_script.sh

11. Schedule a cron job for another user (with appropriate privileges).
    Command:
      sudo crontab -u username -e

12. Configure a cron job to run at a specified date and time.
    Instruction:
      Use standard cron time fields to match the specific date/time.

13. Set a cron job to update system packages weekly.
    Example (for Debian/Ubuntu):
      0 3 * * 0 sudo apt update && sudo apt upgrade -y

14. Create a cron job that removes old files from a temporary directory.
    Example:
      0 4 * * * find /tmp -type f -mtime +7 -delete

15. Schedule a job that monitors disk usage and logs the output.
    Example:
      0 * * * * df -h >> /var/log/disk_usage.log

16. Configure a cron job to run with a random delay to avoid simultaneous execution.
    Instruction:
      Insert “sleep $((RANDOM % 300))” at the start of your script.

17. Set up a cron job that sends email notifications upon task completion.
    Recommendation:
      Set the MAILTO variable at the top of the crontab and ensure your script outputs results.

18. Verify that a cron job is executing correctly by examining log outputs.
    Check:
      /var/log/syslog (or /var/log/cron) for cron entries.

19. Modify the timing of an already scheduled cron job.
    Instruction:
      Edit the crontab using “crontab -e” and adjust the time fields.

20. Export and back up your crontab configuration to a file.
    Command:
      crontab -l > my_crontab_backup.txt

===========================
Topic 4: File Permissions and Ownership
===========================
1. Create a new directory and a file within it.
   Commands:
      mkdir /path/to/newdir
      touch /path/to/newdir/newfile.txt

2. Change a file’s permissions to 644 using both symbolic and numeric methods.
   Commands:
      chmod 644 file.txt
      chmod u=rw,go=r file.txt

3. Adjust a directory’s permissions to 755.
   Command:
      chmod 755 /path/to/directory

4. Use chown to change the file owner to another user.
   Command:
      chown newuser file.txt

5. Modify a file’s group using chgrp or chown.
   Commands:
      chgrp newgroup file.txt
      # or
      chown :newgroup file.txt

6. Recursively set permissions for all files in a directory.
   Command:
      chmod -R 644 /path/to/directory
      # (Adjust directories to 755 if needed)

7. Change permissions on a file using symbolic notation (e.g., u+x).
   Command:
      chmod u+x file.sh

8. Use numeric (octal) notation to modify file permissions.
   Command:
      chmod 755 file.sh

9. Create or modify an executable file to set the setuid bit.
   Command:
      chmod u+s file.sh

10. Create a shared directory and apply the sticky bit.
    Command:
      mkdir /shared
      chmod 1777 /shared

11. Apply an Access Control List to grant a specific user additional permissions on a file.
    Command:
      setfacl -m u:username:rw file.txt

12. Verify the ACL settings on a file using getfacl.
    Command:
      getfacl file.txt

13. Remove an ACL entry from a file using setfacl.
    Command:
      setfacl -x u:username file.txt

14. Write a script to change group ownership on multiple files.
    Example Script (change_group.sh):
      #!/bin/bash
      for file in /path/to/directory/*; do
        chown :newgroup "$file"
      done

15. Set and test the default umask for new files and directories.
    Command:
      umask 022
      # Then create files/directories and check with ls -l

16. Use ls -l to document permissions before and after changes.
    Commands:
      ls -l file.txt > permissions_before.txt
      chmod 644 file.txt
      ls -l file.txt > permissions_after.txt

17. Create a script that resets file permissions to a predefined standard.
    Example Script (reset_permissions.sh):
      #!/bin/bash
      find /path/to/directory -type f -exec chmod 644 {} \;
      find /path/to/directory -type d -exec chmod 755 {} \;

18. Create a file with no permissions and then restore default permissions.
    Commands:
      touch file.txt
      chmod 000 file.txt
      chmod 644 file.txt

19. Adjust group permissions to allow execution on a file.
    Command:
      chmod g+x file.sh

20. Write a script to back up current permissions and restore them when needed.
    Example Script (backup_restore_acl.sh):
      #!/bin/bash
      # Backup ACLs and permissions
      getfacl -R /path/to/directory > permissions.backup
      # To restore later:
      # setfacl --restore=permissions.backup

===========================
Topic 5: Process Management
===========================
1. Use ps to display all running processes.
   Command:
      ps aux

2. Launch top or htop to monitor system processes in real time.
   Commands:
      top
      # or
      htop

3. Use ps or pgrep to find a process by name and note its PID.
   Commands:
      ps aux | grep processname
      pgrep processname

4. Gracefully terminate a process using the SIGTERM signal.
   Command:
      kill -15 <PID>

5. Force terminate a process with SIGKILL if it does not respond to SIGTERM.
   Command:
      kill -9 <PID>

6. Write a script to monitor a specific process’s CPU usage over time.
   Example Script (cpu_monitor.sh):
      #!/bin/bash
      while true; do
        ps -p <PID> -o %cpu,%mem
        sleep 60
      done

7. Create a script that tracks a process’s memory consumption.
   Example Script (mem_monitor.sh):
      #!/bin/bash
      while true; do
        ps -p <PID> -o %mem
        sleep 60
      done

8. Utilize pgrep to find processes matching a specific pattern.
   Command:
      pgrep -f pattern

9. Use pkill to terminate all processes matching a given name.
   Command:
      pkill processname

10. Start a process in the background and verify it is running.
    Commands:
      ./script.sh &
      jobs   # or ps aux

11. Bring a background process to the foreground using job control commands.
    Command:
      fg %<jobnumber>

12. Suspend a process (using Ctrl+Z) and then resume it with fg or bg.
    Instruction:
      Press Ctrl+Z to suspend, then use:
        fg   # to resume in foreground
        bg   # to resume in background

13. Use pstree to visualize parent-child relationships among processes.
    Command:
      pstree

14. Run a long‑running process with nohup to ensure it continues after logout.
    Command:
      nohup ./long_running.sh &

15. Change the niceness value of a running process using renice.
    Command:
      renice -n 10 -p <PID>

16. Use htop to interactively manage and monitor processes.
    Command:
      htop

17. Create a script that logs CPU and memory usage of a process over time.
    Example Script (process_log.sh):
      #!/bin/bash
      while true; do
        ps -p <PID> -o %cpu,%mem >> process_usage.log
        sleep 60
      done

18. Set up a mechanism to automatically restart a specific process if it stops.
    Recommendation:
      Use a systemd service with “Restart=on-failure” or a cron script to check status and restart.

19. Configure a systemd service to manage a long‑running process.
    Instruction:
      Write a systemd unit file for the process and enable it (see Topic 2 for examples).

20. Document overall process resource usage and create a summary report.
    Recommendation:
      Use tools like ps, top, and vmstat to collect data; redirect output to a file and summarize with awk/sed.

===========================
Topic 6: System Logging Configuration
===========================
1. Configure rsyslog to write specific log messages to a custom log file.
   Example (in /etc/rsyslog.d/custom.conf):
      if $msg contains 'specific text' then /var/log/custom.log
      & stop

2. Develop a new rsyslog configuration file for filtering certain logs.
   Instruction:
      Create a file (e.g., /etc/rsyslog.d/filter.conf) with your filtering rules.

3. Restart the rsyslog service to apply configuration changes.
   Command:
      systemctl restart rsyslog

4. Set up a filter in rsyslog to log messages of a specific severity to a separate file.
   Example:
      if $syslogseverity-text == 'error' then /var/log/error.log
      & stop

5. Configure rsyslog to forward selected log messages to a remote log server.
   Example:
      *.* @@remote.server.com:514

6. Create a logrotate configuration for your custom log file.
   Example (in /etc/logrotate.d/custom):
      /var/log/custom.log {
          daily
          rotate 7
          compress
          missingok
          notifempty
      }

7. Use the logger command to generate a test log message and verify its destination.
   Command:
      logger "Test log message"

8. Send a test message via logger and check its logging.
   (Same as item 7.)

9. Use journalctl to view logs from the last hour for a particular service.
   Command:
      journalctl -u servicename --since "1 hour ago"

10. Export filtered journal logs to a file for offline analysis.
    Command:
      journalctl -u servicename --since "1 hour ago" > servicelog.txt

11. Modify the rsyslog configuration to change the default log file location for a facility.
    Instruction:
      Edit the appropriate facility settings in the rsyslog configuration file.

12. Activate debug logging in rsyslog and verify increased verbosity.
    Instruction:
      Adjust rsyslog.conf for debug level logging and restart rsyslog.

13. Back up the original rsyslog configuration file before making changes.
    Command:
      cp /etc/rsyslog.conf /etc/rsyslog.conf.bak

14. Examine the system log files for common error messages and document your findings.
    Recommendation:
      Use grep and tail on /var/log/syslog or /var/log/messages.

15. Configure logrotate to manage log retention and file size limits.
    Instruction:
      Create/update configuration files under /etc/logrotate.d/

16. Adjust file permissions on log files to enhance security.
    Command:
      chmod 640 /var/log/custom.log

17. Create a script to monitor log file sizes and send alerts when they grow beyond a threshold.
    Example Script (log_monitor.sh):
      #!/bin/bash
      filesize=$(stat -c%s /var/log/custom.log)
      threshold=10485760  # 10 MB
      if [ $filesize -gt $threshold ]; then
        echo "Log file too large!" | mail -s "Alert" admin@example.com
      fi

18. Write a script to archive and compress logs older than a specified date.
    Example Script (archive_logs.sh):
      #!/bin/bash
      find /var/log -type f -mtime +7 -exec tar -czf archived_logs.tar.gz {} \;

19. Set up a centralized logging solution on your VM.
    Recommendation:
      Consider installing and configuring tools such as rsyslog, syslog-ng, or an ELK stack.

20. Record every change made to the logging configuration and maintain a backup copy of config files.
    Recommendation:
      Use version control (e.g., git) to track changes in /etc/rsyslog.d/ and /etc/rsyslog.conf.

===========================
Topic 7: Disk & Partition Management
===========================
1. Use fdisk -l (or a similar command) to list all available disks and partitions.
   Command:
      fdisk -l

2. Use fdisk or parted to create a new partition on a virtual disk.
   Command:
      fdisk /dev/sdX
      # Follow on-screen prompts to create a new partition

3. Format the new partition with a filesystem (e.g., ext4).
   Command:
      mkfs.ext4 /dev/sdX1

4. Manually mount the new partition to a designated directory.
   Command:
      mount /dev/sdX1 /mnt/newpartition

5. Add an entry in /etc/fstab to mount the partition automatically at boot.
   Example Entry (in /etc/fstab):
      /dev/sdX1  /mnt/newpartition  ext4  defaults  0 2

6. Initialize a new partition table on a virtual disk.
   Command (using parted):
      parted /dev/sdX mklabel gpt
      # (Or use “msdos” for MBR)

7. Resize an existing partition using parted or another tool.
   Command:
      parted /dev/sdX resizepart <PART_NUMBER> <END>
      # Follow prompts to set new size

8. Create a new physical volume, add it to a volume group, and create a logical volume.
   Commands:
      pvcreate /dev/sdX1
      vgcreate myvg /dev/sdX1
      lvcreate -L 10G -n mylv myvg

9. Format the logical volume with an appropriate filesystem.
   Command:
      mkfs.ext4 /dev/myvg/mylv

10. Mount the logical volume and update /etc/fstab accordingly.
    Commands:
      mount /dev/myvg/mylv /mnt/lv
      # Add appropriate entry to /etc/fstab

11. Create and activate a swap file or partition.
    For a swap file:
      fallocate -l 1G /swapfile
      chmod 600 /swapfile
      mkswap /swapfile
      swapon /swapfile

12. Use df to display disk space usage for all mounted filesystems.
    Command:
      df -h

13. Use du to determine the disk usage of specific directories.
    Command:
      du -sh /path/to/directory

14. Set up a RAID configuration using mdadm on multiple partitions.
    Command:
      mdadm --create --verbose /dev/md0 --level=1 --raid-devices=2 /dev/sdX1 /dev/sdY1

15. Test the RAID configuration by simulating a disk failure scenario.
    Instruction:
      Use “mdadm --fail /dev/md0 /dev/sdX1” then “mdadm --remove /dev/md0 /dev/sdX1” to simulate failure.

16. Use tools like sfdisk to back up the current partition table.
    Command:
      sfdisk -d /dev/sdX > partition_table.backup

17. Practice restoring a partition table from a backup file.
    Command:
      sfdisk /dev/sdX < partition_table.backup

18. Modify the label of a partition using a tool like e2label.
    Command:
      e2label /dev/sdX1 newlabel

19. Perform a filesystem integrity check using fsck.
    Command:
      fsck /dev/sdX1

20. Develop a script that monitors disk usage and sends alerts when thresholds are exceeded.
    Example Script (disk_alert.sh):
      #!/bin/bash
      usage=$(df / | awk 'NR==2 {print $5}' | sed 's/%//')
      threshold=80
      if [ $usage -gt $threshold ]; then
        echo "Disk usage is above threshold: ${usage}%" | mail -s "Disk Alert" admin@example.com
      fi

===========================
Topic 8: Network Configuration
===========================
1. Use ip addr (or ifconfig) to list network interfaces and their statuses.
   Commands:
      ip addr
      # or
      ifconfig

2. Configure a network interface with a static IP address.
   Example (using nmcli):
      nmcli con mod "System eth0" ipv4.addresses 192.168.1.100/24

3. Update /etc/resolv.conf to change DNS server settings.
   Instruction:
      Edit /etc/resolv.conf and add, for example:
      nameserver 8.8.8.8

4. Restart networking services to apply configuration changes.
   Commands:
      systemctl restart networking
      # or
      /etc/init.d/networking restart

5. Use ping to verify connectivity to an external host.
   Command:
      ping google.com

6. Display the current routing table using ip route or route.
   Commands:
      ip route
      # or
      route -n

7. Add a secondary IP address to an existing network interface.
   Command:
      ip addr add 192.168.1.101/24 dev eth0

8. Reconfigure a network interface to use DHCP for dynamic IP assignment.
   Example (using nmcli):
      nmcli con mod "System eth0" ipv4.method auto
      systemctl restart NetworkManager

9. Implement a basic firewall rule with iptables (or nftables) to block a specific port.
   Command:
      iptables -A INPUT -p tcp --dport 8080 -j DROP

10. Use netstat or ss to display active network connections.
    Commands:
      ss -tuln
      # or
      netstat -tuln

11. Use traceroute (or tracepath) to diagnose network path issues.
    Commands:
      traceroute google.com
      # or
      tracepath google.com

12. Adjust NetworkManager settings for a network interface if applicable.
    Example (using nmcli):
      nmcli con mod "System eth0" ipv4.dns "8.8.8.8 8.8.4.4"

13. Establish a VPN connection using command‑line tools and configuration files.
    Example:
      openvpn --config /path/to/config.ovpn

14. Verify DNS functionality using nslookup or dig.
    Commands:
      nslookup google.com
      # or
      dig google.com

15. Add a custom routing rule for specific traffic.
    Example:
      ip rule add from 192.168.1.100 table 100
      ip route add default via 192.168.1.1 dev eth0 table 100

16. Use iftop to observe real‑time network bandwidth usage.
    Command:
      iftop

17. Write a script that checks and logs network connectivity at intervals.
    Example Script (network_ping.sh):
      #!/bin/bash
      while true; do
        ping -c 4 google.com >> /var/log/network_ping.log
        sleep 300
      done

18. Set up a mechanism to automatically switch network configurations upon failure.
    Recommendation:
      Use tools (ifupdown, NetworkManager scripts) or custom scripts to detect failure and reconfigure.

19. Record every change made to network settings and back up configuration files.
    Recommendation:
      Maintain version-controlled backups of configuration files (e.g., /etc/network/interfaces, /etc/NetworkManager).

20. Develop a script that resets network settings automatically if connectivity is lost.
    Example Script (reset_network.sh):
      #!/bin/bash
      if ! ping -c 1 8.8.8.8 &> /dev/null; then
        systemctl restart networking
      fi

===========================
Topic 9: Package Management
===========================
1. Refresh the package index using your distro’s package manager.
   Commands:
      sudo apt-get update
      # or
      sudo yum update

2. Perform a full system upgrade.
   Commands:
      sudo apt-get upgrade
      # or
      sudo yum upgrade

3. Install a new package from the repositories.
   Commands:
      sudo apt-get install package_name
      # or
      sudo yum install package_name

4. Uninstall an installed package and verify its removal.
   Commands:
      sudo apt-get remove package_name
      # or
      sudo yum remove package_name

5. Use a package search command to find a package.
   Commands:
      apt-cache search package_name
      # or
      yum search package_name

6. List installed packages to confirm a package’s presence.
   Commands:
      dpkg -l
      # or
      rpm -qa

7. Download a package without immediately installing it.
   Command:
      apt-get download package_name

8. Install a package from a local .deb (or equivalent) file.
   Command:
      sudo dpkg -i package_file.deb
      # (For RPM-based systems, use: sudo rpm -i package_file.rpm)

9. Resolve and fix broken dependencies using appropriate package manager commands.
   Command:
      sudo apt-get install -f

10. Clear the package cache to free up space.
    Commands:
      sudo apt-get clean
      # or
      sudo yum clean all

11. Generate a complete list of available packages.
    Commands:
      apt-cache dumpavail
      # or
      yum list available

12. Add a new repository or PPA (if using Ubuntu/Debian).
    Command:
      sudo add-apt-repository ppa:repository_name

13. Remove or disable an added repository from the configuration.
    Instruction:
      Edit /etc/apt/sources.list or files under /etc/apt/sources.list.d/ and comment out or remove the repository.

14. Lock a package version to prevent automatic upgrades.
    Command:
      sudo apt-mark hold package_name

15. Downgrade a package to an earlier version and verify compatibility.
    Example:
      sudo apt-get install package_name=version_number

16. Apply security updates only and document the process.
    Recommendation:
      Use tools like unattended-upgrades or manually run: sudo unattended-upgrade

17. Set up unattended upgrades for automated security patches.
    Command:
      sudo dpkg-reconfigure -plow unattended-upgrades

18. Display detailed information about a package.
    Commands:
      apt-cache show package_name
      # or
      yum info package_name

19. Check for available package updates.
    Command:
      sudo apt-get update && apt-get upgrade -s

20. Document and back up package management logs and history.
    Recommendation:
      Copy /var/log/apt/history.log (or equivalent for yum) to a secure backup location.

===========================
Topic 10: System Resource Monitoring
===========================
1. Use top to observe real‑time CPU and memory usage.
   Command:
      top

2. Install and run htop to monitor system resources interactively.
   Commands:
      sudo apt-get install htop
      htop

3. Display current memory usage using the free command.
   Command:
      free -h

4. Use vmstat to obtain system performance metrics.
   Command:
      vmstat 5

5. Install and run iostat to monitor disk input/output activity.
   Commands:
      sudo apt-get install sysstat
      iostat -x 5

6. Write a script to log system resource usage (CPU, memory) at regular intervals.
   Example Script (sys_resource_log.sh):
      #!/bin/bash
      while true; do
        top -b -n1 | head -n 20 >> /var/log/sys_resources.log
        sleep 300
      done

7. Install and configure sysstat to use sar for detailed performance tracking.
   Commands:
      sudo apt-get install sysstat
      # Enable data collection in /etc/default/sysstat and restart:
      systemctl restart sysstat

8. Use mpstat to create a CPU usage report and save it to a file.
   Command:
      mpstat 1 5 > cpu_report.txt

9. Use iftop to monitor real‑time network bandwidth usage.
   Command:
      iftop

10. Use the uptime command to display load averages.
    Command:
      uptime

11. Use dstat to display multiple system resource statistics simultaneously.
    Command:
      dstat

12. Verify swap usage using free or swapon -s.
    Commands:
      free -h
      swapon -s

13. Configure monitoring tools to alert when CPU or memory usage exceeds a defined threshold.
    Recommendation:
      Use monitoring solutions like Nagios, Zabbix, or custom scripts.

14. Create a cron job that runs your resource monitoring script at set intervals.
    Example:
      */5 * * * * /path/to/sys_resource_log.sh

15. Redirect the output of your monitoring script to a log file for historical analysis.
    (This is handled in the scripts above with output redirection.)

16. Use available tools (e.g., GNUPlot) to create a graphical report of resource usage.
    Recommendation:
      Install gnuplot and use it to graph data extracted from your log files.

17. Capture and compare resource usage data between two time intervals.
    Recommendation:
      Run resource monitoring commands at different intervals and compare outputs (e.g., using diff or custom parsing scripts).

18. Write a script to monitor and log resource usage for a specific process.
    Example Script (process_resource.sh):
      #!/bin/bash
      while true; do
        ps -p <PID> -o %cpu,%mem >> process_resource.log
        sleep 60
      done

19. Create a summary report of system resource data over a period.
    Recommendation:
      Aggregate data from various logs (using awk/sed) and output a formatted summary report.

20. Outline and implement a comprehensive plan for ongoing system performance monitoring.
    Recommendation:
      Develop a plan that includes:
        - Regular collection of metrics (using tools like sar, top, vmstat)
        - Automated alerts when thresholds are exceeded
        - Periodic analysis and reporting of performance data
        - Maintaining historical logs for trend analysis
        - Documenting all monitoring procedures and thresholds

#############################################
# End of Cheat Sheet
#############################################

