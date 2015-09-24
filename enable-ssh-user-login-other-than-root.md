# Enable SSH User Login Other Than Root

In order to make SSH more secure I wanted to do 5 things;
* Enable SSH login for a non-root user
* Enable "su" in order to elevate permissions when necessary
* Turn on SSH Public Key Authentication
* Disable Password Authentication
* Disable Root Login

Enable SSH login for a non-root user
1.Create a regular user from the web-based management interface (if you have not already done so).
As above, I will use "frank" in this example. Login to frank using the web-based interface in order to force the system to create frank's home directory.

2. Login as root via SSH.

3. Copy the root profile to frank's home directory and edit the path of "HOME".
cp /root/.profile /volume1/homes/frank
vi /volume1/homes/frank/.profile
Change the line that reads "HOME=/root" to "HOME=/volume1/homes/frank", then quit saving changes.

4. Create a backup of the passwd file and edit the original file changing the user's shell from "C" shell to "Bourne" shell.
cp -p /etc/passwd /etc/passwd.orig
vi /etc/passwd
frank:x:1030:100:Frank Smith:/var/services/homes/frank:/bin/csh
frank:x:1030:100:Frank Smith:/var/services/homes/frank:/bin/sh

Enable "su"
BusyBox includes "su" but the permissions are set incorrectly and cause the following error;
su: must be suid to work properly

The current permissions are rwxr-xr-x (755) instead of rwsr-xr-x (4755).
In order to fix this execute the following;
chmod 4755 /bin/busybox
For a good description of the difference between "su" and "sudo", and the difference between 755 and 4755 see the following links;
http://www.howtogeek.com/111479/htg-exp ... n-sudo-su/
http://beanpicks.wordpress.com/2007/02/ ... ermission/

Turn on SSH Public Key Authentication
For a comprehensive guide on how to create and install your public/private keys see either of the following pages;
http://www.howtoforge.com/ssh_key_based_logins_putty
http://www.mauchle.name/blog/?p=239

Disable Password Authentication
Create a backup of the ssh configuration file and edit the original.
cp -p /etc/ssh/sshd_config /etc/ssh/sshd_config.orig
vi /etc/ssh/sshd_config
Find this text, uncomment it and change "yes" to "no".
#PasswordAuthentication yes

Disable Root Login
As you already have a backup of the ssh configuration file, there is no need to do this again.
vi /etc/ssh/sshd_config
Find this text, uncomment it and change "yes" to "no".
#PermitRootLogin yes

All changes require you to restart SSH before they take effect.
You can restart SSH using the following command, however, I found that disabling Password Authentication required me to restart the entire Synology box.
/usr/syno/etc.defaults/rc.d/S95sshd.sh start &

If you do something wrong and cannot connect to your Synology box via SSH, you can always enable Telnet and undo any changes.
