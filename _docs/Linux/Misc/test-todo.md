---
title: RHCSA Test practice tests from Book
parent: Misc
grand_parent: Linux
nav_order: 50
layout: default
---
# Test practice TODO  

1. [x] Set default values for new users. Set the default password validity to 90 days, and set the first
UID that is used for new users to 2000.

2. [x] Create users edwin and santos and make them members of the group livingopensource as a
secondary group membership. Also, create users serene and alex and make them members of
the group operations as a secondary group. Ensure that user santos has UID 1234 and cannot
start an interactive shell.

3. [x] Create shared group directories /groups/livingopensource and /groups/operations, and
make sure the groups meet the following requirements:
    1.[x]  Members of the group livingopensource have full access to their directory.
    2.[x]  Members of the group operations have full access to their directory.
    3.[x]  New files that are created in the group directory are group owned by the group owner of
the parent directory.
    4.[x]  Others have no access to the group directories

4. [x] Create a 2-GiB volume group with the name myvg, using 8-MiB physical extents. In this volume
group, create a 500-MiB logical volume with the name mydata, and mount it persistently on
the directory /mydata

5. [x]  Find all files that are owned by user edwin and copy them to the directory /rootedwinfiles.

6.  [x] Schedule a task that runs the command touch /etc/motd every day from Monday through
Friday at 2 a.m.


8.  [x] Create user bob and set this user’s shell so that this user can only change the password and
cannot do anything else.

9.  [x] Install the vsftpd service and ensure that it is started automatically at reboot.

10. [x]  Create a container that runs an HTTP server. Ensure that it mounts the host directory /httproot
on the directory /var/www/html.

11.  [x] Configure this container such that it is automatically started on system boot as a system user
service.

12.  [x] Create a directory with the name /users and ensure it contains the subdirectories linda and
anna. Export this directory by using an NFS server.

13.  [x] Create users linda and anna and set their home directories to /home/users/linda and
/home/users/anna. Make sure that while these users access their home directory, autofs is used
to mount the NFS shares /users/linda and /users/anna from the same server. 


---


2.[ ]  Create user student with password password, and user root with password password.

3.[ ]  Configure your system to automatically mount the ISO of the installation disk on the directory
/repo. Configure your system to remove this loop-mounted ISO as the only repository that is
used for installation. Do not register your system with subscription-manager, and remove all
references to external repositories that may already exist.

4.[ ]  Create a 1-GB partition on /dev/sdb. Format it with the vfat file system. Mount it persistently on
the directory /mydata, using the label mylabel.

5.[ ]  Set default values for new users. Ensure that an empty file with the name NEWFILE is copied
to the home directory of each new user that is created.

6.[ ]  Create users laura and linda and make them members of the group livingopensource as a
secondary group membership. Also, create users lisa and lori and make them members of the
group operations as a secondary group.

7.[ ]  Create shared group directories /groups/livingopensource and /groups/operations and
make sure these groups meet the following requirements:
    1.[ ]  Members of the group livingopensource have full access to their directory.
    2.[ ]  Members of the group operations have full access to their directory.
    3.[ ]  Users should be allowed to delete only their own files.
    4.[ ]  Others should have no access to any of the directories.

8.[ ]  Create a 2-GiB swap partition and mount it persistently.

9.[ ]  Resize the LVM logical volume that contains the root file system and add 1 GiB. Perform all
tasks necessary to do so.

10.[ ]  Find all files that are owned by user linda and copy them to the file /tmp/lindafiles/.

11.[ ]  Create user vicky with the custom UID 2008.

12.[ ]  Install a web server and ensure that it is started automatically.

13.[x]  Configure a container that runs the docker.io/library/mysql:latest image and ensure it meets
the following conditions
    1.[x]  It runs as a rootless container in the user linda account.
    2.[x]  It is configured to use the mysql root password password.
    3.[x]  It bind mounts the host directory /home/student/mysql to the container directory /var/lib/mysql.
    4.[x]  It automatically starts through a systemd job, where it is not needed for user linda to log in.
    

---

2.[x]  Create user student with password password, and user root with password password.

3.[x]  Configure your system to automatically mount the ISO of the installation disk on the directory
/repo. Configure your system to remove this loop-mounted ISO as the only repository that is
used for installation. Do not register your system with subscription-manager, and remove all
references to external repositories that may already exist.

4.[x]  Reboot your server. Assume that you don’t know the root password, and use the appropriate
mode to enter a root shell that doesn’t require a password. Set the root password to
mypassword.

5.[x]  Set default values for new users. Make sure that any new user password has a length of at least
six characters and must be used for at least three days before it can be reset.

6.[x]  Create users linda and anna and make them members of the group sales as a secondary
group membership. Also, create users serene and alex and make them members of the group
acc unt as a secondary group.

7.[x]  Configure an SSH server that meets the following requirements:
    1.[x] User root is allowed to connect through SSH.
    2.[x] The server offers services on port 2022.

8.[x] Create shared group directories /groups/sales and /groups/account, and make sure these
groups meet the following requirements:
    1.[x] Members of the group sales have full access to their directory.
    2.[x] Members of the group account have full access to their directory.
    3.[x] Users have permissions to delete only their own files, but Alex is the general manager, so user alex has access to delete all users’ files.

9.[x] Create a 4-GiB volume group, using a physical extent size of 2 MiB. In this volume group,
create a 1-GiB logical volume with the name myfiles, format it with the Ext3 file system, and
mount it persistently on /myfiles.

10.[x] Create a group sysadmins. Make users linda and anna members of this group and ensure that
all members of this group can run all administrative commands using sudo.

11.[x] Optimize your server with the appropriate profile that optimizes throughput.

12.[x] Add a new disk to your virtual machine with a size of 10 GiB. On this disk, create a LVM logical
volume with a size of 5 GiB, configure it as swap, and mount it persistently.

13.[x] Create a directory /users/ and in this directory create the directories user1 through user5
using one command.

14.[x] Configure a web server to use the nondefault document root /webfiles. In this directory, create
a file index.html that has the contents hello world and then test that it works.

15.[x] Configure your system to automatically start a mariadb container. This container should expose
its services at port 3306 and use the directory /var/mariadb-container on the host for persistent
storage of files it writes to the /var directory.

16.[ ] Configure your system such that the container created in step 15 is automatically started as a
Systemd user container.

---


4.[ ] Create a 500-MiB partition on your second hard disk, and format it with the Ext4 file system.
Mount it persistently on the directory /mydata, using the label mydata.

5.[ ] Set default values for new users. A user should get a warning three days before expiration of
the current password. Also, new passwords should have a maximum lifetime of 120 days.

6.[ ]  Create users lori and laura and make them members of the secondary group sales. Ensure
that user lori uses UID 2000 and user laura uses UID 2001.

7.[ ]  Create shared group directories /groups/sales and /groups/data, and make sure the groups
meet the following requirements:

    1.[ ]  Members of the group sales have full access to their directory.
    2.[ ]  Members of the group data have full access to their directory.
    3.[ ]  Others has no access to any of the directories.
    4.[ ]  Alex is general manager, so user alex has read access to all files in both directories and has permissions to delete all files that are created in both directories.

8.[x]  Create a 1-GiB swap partition and mount it persistently.

9.[x]  Find all files that have the SUID permission set, and write the result to the file /root/suidfiles.

10.[x]  Create a 1-GiB LVM volume group. In this volume group, create a 512-MiB swap volume and
mount it persistently.

12.[x]  Install an HTTP web server and configure it to listen on port 8080.

13.[x]  Create a configuration that allows user laura to run all administrative commands using sudo.

14.[x]  Create a directory with the name /users and ensure it contains the subdirectories linda and
anna. Export this directory by using an NFS server.

15.[x]  Create users linda and anna and set their home directories to /home/users/linda and
/home/users/anna. Make sure that while these users access their home directory, autofs is used
to mount the NFS shares /users/linda and /users/anna from the same server


# Weaknesses 

- cronjob timers 
- SElinux context label switching 
- Selinux modifying boolean settings 
- Diagnosing SElinux violations 
- regex 


# Consistent practice 

- swap files / partitions 

- NFS exported home dirctories 

- LVM management ( creating PV's, VG's , and LV's )

- Resizing LV's 

- container persistent storage and logging 

- resetting root password 

- modifying default grub config 

- generating systemd unit file for container  

- configuring network interface through config file in /etc/sysconfig  



# Practice tasks
---

## Cronjob Timer Practice 

### Beginner  

1. Run a script every day at 2:00 AM.
2. Execute a backup script every Sunday at 4:30 AM.
3. Schedule a job to run every 15 minutes.
4. Trigger a script every weekday at 8:00 AM.
5. Run a task every hour, on the hour.

### Intermediate 

6. Schedule a task to run on the 1st and 15th of every month at midnight.
7. Run a script every 10 minutes, but only during working hours (9 AM to 5 PM).
8. Execute a job every Monday, Wednesday, and Friday at 6:45 PM.
9. Run a job every 2 hours, but only on weekends.
10. Schedule a script to run every day at 5 minutes past every hour.

### Advanced 

11. Run a script at 11:15 PM on the last day of every month.
12. Trigger a script every 3 minutes, but only between 1:00 PM and 1:30 PM on Mondays.
13. Execute a task every 5 minutes between 6 AM and 9 AM, but only on the 1st of each month.
14. Schedule a script to run at 3:33 AM and 3:33 PM on odd-numbered days.
15. Run a script every minute, but only during the first 10 minutes of every hour on weekdays.



# SELinux Practice Tasks (RHCSA 9)
---

## 1. Enable SELinux enforcing mode at boot
- Ensure SELinux is in enforcing mode both currently and after reboot.

## 2. Identify and fix a file context mismatch
- A web server is failing to serve content from `/srv/www/html`. Diagnose and fix any SELinux context issues.

## 3. Allow a custom Apache virtual host to bind to port 8081
- Configure SELinux to allow Apache to use TCP port 8081 for a new virtual host.

## 4. Create a custom SELinux policy module
- A script located at `/opt/scripts/backup.sh` is being blocked by SELinux when accessing `/mnt/backup`. Identify the AVC denial and create a custom policy module to allow the access.

## 5. Set SELinux boolean to allow FTP write access
- Enable SELinux to allow users to upload files to the vsftpd server.

## 6. Restore default file contexts recursively
- Restore the correct SELinux contexts for all files under `/var/www/html` after manual modification.

## 7. Configure SELinux to allow NFS home directories
- The system uses NFS-mounted home directories. Configure SELinux to permit this configuration.

## 8. Relabel a newly created directory for Samba
- A directory `/share/public` has been created and shared using Samba. Configure the correct SELinux context so Samba can access it properly.

## 9. Troubleshoot a SELinux-related mail service issue
- The `postfix` service fails to deliver mail. Use SELinux logs to identify the issue and correct it.

## 10. Limit Apache access to specific directories with SELinux
- Configure SELinux to deny Apache access to `/secret_data` while still allowing it to serve content from `/var/www/html`.



# SELinux Boolean Practice Questions
---

## 1. Enable the SELinux boolean that allows Apache to make network connections.
- Identify and enable the appropriate SELinux boolean so that Apache can connect to remote services (e.g., database servers).

## 2. Temporarily disable SELinux boolean for FTP passive mode support.
- Disable the boolean that allows FTP to use passive mode and ensure the change is not persistent.

## 3. Allow Samba to share home directories.
- Set the appropriate SELinux boolean to permit Samba to access and share user home directories.

## 4. Enable persistent SELinux boolean for web applications to access user content.
- Ensure that web applications running under Apache can access user home directories persistently.

## 5. Prevent MySQL from accessing home directories.
- Disable the SELinux boolean that allows MySQL to read or write in user home directories.

## 6. Allow NFS to be exported by Samba.
- Set the SELinux boolean that allows Samba to export NFS-mounted directories.

## 7. Enable SELinux boolean to allow HTTPD to send mail.
- Allow Apache to send emails using local mail transfer agents like Postfix.

## 8. List all SELinux booleans related to `httpd` and describe their current states.
- Use appropriate tools to generate a list and determine the function of each boolean.

## 9. Change a boolean so that FTP can write to files in the public directory.
- Modify SELinux boolean settings to allow vsftpd users to upload files to a shared directory.

## 10. Persistently enable SELinux boolean to allow SSH to manage users.
- Adjust and persist the boolean setting so SSH can manage user sessions with elevated control.



# Configure Time Service Clients
---

## Task 1
Configure a RHEL system to synchronize time using the `chronyd` service. Ensure it starts at boot.

## Task 2
Modify the system time server list to include `time.nist.gov` and remove any default servers.

## Task 3
Configure the time service client to use a specific NTP server on your local network, such as `192.168.1.100`.

## Task 4
Ensure the system clock is synchronized immediately with the configured NTP server using `chronyc`.

## Task 5
Verify that the system time is being synchronized correctly using the appropriate command.

## Task 6
Disable and mask `ntpd` if it is installed, and ensure only `chronyd` is used for time synchronization.

## Task 7
Modify the `chrony.conf` file to log measurements to a custom log file in `/var/log`.

## Task 8
Set the time zone to `America/New_York` and verify that the change is applied system-wide.

## Task 9
Configure the system to act as a time server for a private network using `chronyd`.

## Task 10
Force a manual synchronization of the system clock and verify the result.

