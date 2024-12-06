

**Step 1 : be able to answer all the Do I know this already ? questions** 


**Step 2 : complete all the labs in the book**

**Step 3 : complete the pre-assessment exam**

**Step 4 : complete the RHCSA Practice Exams**

[x] -  Mount installation disk automatically on /repo with fstab 

[ ] - remove all references to external repoistories 

[x] - Assuming you don't know the root password , boot the server into a mode where you can reset the root password to "mypassword"

[x] - Set default values for new users,  change default password validity to 90 days , set the first UID used for new users to 2000

[x] - create users , edwin , santos

[x] - make edwin , santos of the group livingopensource as a secondary group membership 

[x] - create users serene , alex

[x] - make serene and alex apart of the group "operations" as a secondary group 

[x] - change santos UID to 1234 

[x] - configure santos to be unable to start an interactive shell 

[x] - create shared group directories /groups/livingopensource and /groups/operations

[x] - configure the group livingopensource to have full access 

[x] - configure operations to have full access 

[x] - configure the /group directory where new files are group owned by the group owner of the parent directory. AKA files created in /groups/operations are owned by the group owner of /groups/operations

[x] - configure "others" to have no access to the group directories 

[x] - create a 2Gib volume group with the name myvg , using 8-MiB physical extents. 

[ ] - In the myvg volume group , create a 500-MiB logical volume with the name "mydata"

[ ] - mount the mydata volume group persistently on /mydata

[ ] - Find all files that are owned by user edwin and copy them to the /rootedwinfiles

[ ] - Schedule a task that runs the command touch /etc/motd every day from Monday through Friday at 2 am 

[ ] - Add a new 10-GiB virtual disk to your VM 

[ ] - on the newly added disk , create a stratis volume and mount it persistently 

[ ] - create the user bob and set the users shell so that they can only change the password and nothing else.

[ ] - install the vsftpd service and it is started automatically at boot 

[ ] - create a container that runs an HTTP server. 

[ ] - Ensure that the container mounts the host directory /httproot on the directory /var/www/html 

[ ] - configure the container to start at boot as a system user service 

[ ] - create a dir with the name /users 

[ ] - ensure the /users directory contains the subdirs "linda" and "anna"

[ ] - export the linda and anna directories using an NFS server

[ ] - create users linda and anna and set their home directories to /home/users/linda and /home/users/anna

[ ] - use autofs to mount their home directories 
