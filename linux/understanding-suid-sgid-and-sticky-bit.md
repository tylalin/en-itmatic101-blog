# Understanding SUID, SGID and Sticky Bit

## SUID (Set-User-ID) Bit

Any binary file with SUID bit can be run by anyone as its user owner e.g., `/usr/bin/passwd`. It can be executed by anyone without _sudo_ privilege to change their own password on a Linux system. The SUID bit can be set on any other file or directory and not restricted only to the executable binaries. But setting the SUID bit on a directory in a Linux system is not a common or recommended practice, and it typically doesn't serve a useful purpose in the same way it does for executable files. The SUID bit is mainly used for executable files to allow them to run with the privileges of the file owner rather than the user who executed the file. When applied to a directory, the SUID bit doesn't have the same meaning or functionality.

In a Linux system, the SUID bit is typically applied to executable files, not directories. The SUID bit on an executable file allows a user who runs the file to temporarily execute it with the permissions of the file's owner rather than their own. This can be useful in certain situations to grant users limited access to specific privileged actions without giving them full root access.

However, applying the SUID bit to a directory is not a common or recommended practice because directories don't execute code in the same way that executable files do. Directories primarily contain files and sub-directories, and they are used for organising and storing data.

If the SUID bit is set on a directory, it may not have any meaningful effect, as the operating system doesn't have a standard behaviour associated with it. Additionally, it could potentially create security risks or unexpected behaviour because it might affect how file operations are carried out within that directory.

In summary, setting the SUID bit on a directory is not a standard practice and is generally discouraged in Linux systems. If you have specific security or access control requirements for directories, it's typically better to use appropriate file permissions and ownership settings rather than relying on SUID. To scan all the files with SUID bit by using `find` command:

```bash
vagrant@ubuntu1:~$ find / -perm -u=s -type f 2>/dev/null

or 

vagrant@ubuntu1:~$ find / -perm -4000 -type f 2>/dev/null
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/openssh/ssh-keysign
/usr/lib/snapd/snap-confine
/usr/libexec/polkit-agent-helper-1
/usr/bin/passwd  <<< Here is the path for passwd binary file
/usr/bin/chfn
/usr/bin/mount
/usr/bin/su
/usr/bin/pkexec
/usr/bin/newgrp
/usr/bin/sudo
/usr/bin/gpasswd
/usr/bin/fusermount3
/usr/bin/chsh
/usr/bin/umount
/opt/VBoxGuestAdditions-7.0.6/bin/VBoxDRMClient
/snap/core20/2015/usr/bin/chfn
/snap/core20/2015/usr/bin/chsh
/snap/core20/2015/usr/bin/gpasswd
/snap/core20/2015/usr/bin/mount
/snap/core20/2015/usr/bin/newgrp
/snap/core20/2015/usr/bin/passwd
/snap/core20/2015/usr/bin/su
/snap/core20/2015/usr/bin/sudo
/snap/core20/2015/usr/bin/umount
/snap/core20/2015/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/snap/core20/2015/usr/lib/openssh/ssh-keysign
/snap/core20/1822/usr/bin/chfn
/snap/core20/1822/usr/bin/chsh
/snap/core20/1822/usr/bin/gpasswd
/snap/core20/1822/usr/bin/mount
/snap/core20/1822/usr/bin/newgrp
/snap/core20/1822/usr/bin/passwd
/snap/core20/1822/usr/bin/su
/snap/core20/1822/usr/bin/sudo
/snap/core20/1822/usr/bin/umount
/snap/core20/1822/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/snap/core20/1822/usr/lib/openssh/ssh-keysign
/snap/snapd/18357/usr/lib/snapd/snap-confine
```

As shown in the output, `/usr/bin/passwd` is one of the binary files with SUID bit. Its permission looks like this.

```bash
vagrant@ubuntu1:~$ ll /usr/bin/passwd
-rwsr-xr-x 1 root root 59976 Nov 24  2022 /usr/bin/passwd*
```

It means that anyone on this Linux box can run `passwd` command as user owner _root_ without sudo privilege escalation. In other word, you don't need to be _root_ to execute this binary although _root_ is the user and group owner of it. That's why you can run `passwd` command as _non-root_ user or without _sudo_ prefix.

```bash
vagrant@ubuntu1:~$ passwd
Changing password for vagrant.
Current password: 
```

Let's have a look at a binary file which doesn't have SUID bit on it. For example, you can't run `parted` command as _non-root_ since its default permission is shown as below.

```bash
vagrant@ubuntu1:~$ which parted
vagrant@ubuntu1:~$ ll /usr/sbin/parted
-rwxr-xr-x 1 root root 88472 Mar 24  2022 /usr/sbin/parted*
vagrant@ubuntu1:~$ parted
WARNING: You are not superuser.  Watch out for permissions.
/dev/mapper/control: open failed: Permission denied
Failure to communicate with kernel device-mapper driver.
Incompatible libdevmapper 1.02.175 (2021-01-08) and kernel driver (unknown version).
Error: No device found
Retry/Cancel?     
```

It is not quite usual to add SUID bit to a binary file like `parted` for everyone to run it as _non-root_ user. But it can demonstrate the drastic affect by doing a strange thing like adding SUID bit to a system partition tool `parted`. To add SUID bit to the _parted_ binary, you can use `chmod` command as below.

```bash
# ugoa - u for User, g for Group, o for Others and a for All 
sudo chmod u+s /usr/sbin/parted

or

# Numeric mode in octal format - 4000(s) for SUID, 0700 (rwx) for User, 0050 (r-x) for Group and 0005 (r-x) for Others
sudo chmod 4755 /usr/sbin/parted
```

Now you can run `parted` as _non-root_ user even though it complains about it as shown below.

```bash
vagrant@ubuntu1:~$ parted
WARNING: You are not superuser.  Watch out for permissions.
GNU Parted 3.4
Using /dev/sda
Welcome to GNU Parted! Type 'help' to view a list of commands.
(parted)  
```

## SGID (Set-Group-ID) Bit

Any sub-files or sub-directories created inside a directory with SGID bit are owned by its group owner through inheritance although they are created by different user or group owner. It is an ideal situation to share a directory among team to collaborate for a group project. Only caveat is whoever has the access to write to the directory also can delete stuffs inside it although they don't own them or create them originally. Sometimes it is desired to let all users in a group to create and delete sub-files and sub-directories in a shared space for collaboration.

There are some binary files with SGID bit for some use cases like `crontab`. The _crontab_ group has no members but it has a very unique usage. The binary file `/usr/bin/crontab` is owned by the _crontab_ group with SGID bit set, and `/var/spool/cron/crontabs` directory's group owner is _crontab_ with sticky bit _T_. When you check its directory, there is no file or directory exists as you can see below.

```bash
vagrant@ubuntu1:~$ which crontab
/usr/bin/crontab
vagrant@ubuntu1:~$ ll /usr/bin/crontab
-rwxr-sr-x 1 root crontab 39568 Mar 23  2022 /usr/bin/crontab*
vagrant@ubuntu1:~$ ll /var/spool/cron/
total 12
drwxr-xr-x 3 root root    4096 Feb 17  2023 ./
drwxr-xr-x 4 root root    4096 Feb 17  2023 ../
drwx-wx--T 2 root crontab 4096 Mar 23  2022 crontabs/
vagrant@ubuntu1:~$ sudo  -i 
root@ubuntu1:~# ll /var/spool/cron/crontabs/
total 8
drwx-wx--T 2 root crontab 4096 Mar 23  2022 ./
drwxr-xr-x 3 root root    4096 Feb 17  2023 ../
```

Now let's experiment creating a simple cron job as the user _vagrant_ and verify if that makes any change in the `/var/spool/cron/crontabs` directory.

```bash
vagrant@ubuntu1:~$ crontab -e

# Edit this file to introduce tasks to be run by cron.
# 
# Each task to run has to be defined through a single line
# indicating with different fields when the task will be run
# and what command to run for the task
# 
# To define the time you can provide concrete values for
# minute (m), hour (h), day of month (dom), month (mon),
# and day of week (dow) or use '*' in these fields (for 'any').
# 
# Notice that tasks will be started based on the cron's system
# daemon's notion of time and timezones.
# 
# Output of the crontab jobs (including errors) is sent through
# email to the user the crontab file belongs to (unless redirected).
# 
# For example, you can run a backup of all your user accounts
# at 5 a.m every week with:
# 0 5 * * 1 tar -zcf /var/backups/home.tgz /home/
# 
# For more information see the manual pages of crontab(5) and cron(8)
# 
# m h  dom mon dow   command
* * * * * /bin/date >> /tmp/vagrant_date

crontab: installing new crontab
vagrant@ubuntu1:~$ sudo -i
root@ubuntu1:~# cd /var/spool/cron/crontabs
root@ubuntu1:/var/spool/cron/crontabs# ll
total 12
drwx-wx--T 2 root    crontab 4096 Sep  9 05:31 ./
drwxr-xr-x 3 root    root    4096 Feb 17  2023 ../
-rw------- 1 vagrant crontab 1130 Sep  9 05:31 vagrant
root@ubuntu1:/var/spool/cron/crontabs# cat vagrant 
# DO NOT EDIT THIS FILE - edit the master and reinstall.
# (/tmp/crontab.pslqAe/crontab installed on Sat Sep  9 05:34:52 2023)
# (Cron version -- $Id: crontab.c,v 2.13 1994/01/17 03:20:37 vixie Exp $)
# Edit this file to introduce tasks to be run by cron.
# 
# Each task to run has to be defined through a single line
# indicating with different fields when the task will be run
# and what command to run for the task
# 
# To define the time you can provide concrete values for
# minute (m), hour (h), day of month (dom), month (mon),
# and day of week (dow) or use '*' in these fields (for 'any').
# 
# Notice that tasks will be started based on the cron's system
# daemon's notion of time and timezones.
# 
# Output of the crontab jobs (including errors) is sent through
# email to the user the crontab file belongs to (unless redirected).
# 
# For example, you can run a backup of all your user accounts
# at 5 a.m every week with:
# 0 5 * * 1 tar -zcf /var/backups/home.tgz /home/
# 
# For more information see the manual pages of crontab(5) and cron(8)
# 
# m h  dom mon dow   command
* * * * * /bin/date >> /tmp/vagrant_date
```

Upon checking its directory, you can see there is a new file called _vagrant_ which is owned by the user owner _vagrant_ and the group owner _crontab_ with the permission of _-rw-------_. To scan all the files and directories with SGID by using `find` command:

```bash
vagrant@ubuntu1:~$ find / -perm -2000 2>/dev/null
/usr/lib/x86_64-linux-gnu/utempter/utempter
/usr/sbin/unix_chkpwd
/usr/sbin/pam_extrausers_chkpwd
/usr/bin/chage
/usr/bin/ssh-agent
/usr/bin/wall
/usr/bin/write.ul
/usr/bin/crontab
/usr/bin/expiry
/usr/local/share/fonts
/snap/core20/2015/usr/bin/chage
/snap/core20/2015/usr/bin/expiry
/snap/core20/2015/usr/bin/ssh-agent
/snap/core20/2015/usr/bin/wall
/snap/core20/2015/usr/sbin/pam_extrausers_chkpwd
/snap/core20/2015/usr/sbin/unix_chkpwd
/snap/core20/2015/var/mail
/snap/core20/1822/usr/bin/chage
/snap/core20/1822/usr/bin/expiry
/snap/core20/1822/usr/bin/ssh-agent
/snap/core20/1822/usr/bin/wall
/snap/core20/1822/usr/sbin/pam_extrausers_chkpwd
/snap/core20/1822/usr/sbin/unix_chkpwd
/snap/core20/1822/var/mail
/run/log/journal
/var/log/journal
/var/log/journal/b7e96a3ae2604010b85b38bceabddc41
/var/log/journal/4c21ad51207042618852295ed0c84eba
/var/local
/var/mail
```

To add SGID bit to a directory, you can again use `chmod` command as shown below.

```bash
# ugoa - u for User, g for Group, o for Others and a for All 
sudo chmod g+s /developers

or 

# Numeric mode in octal format - 2000(s) for SUID, 0000 (---) for User, 0070 (rwx) for Group and 0000 (---) for Others
sudo chmod 2070 /developers
```

Here is how it can be prepared to test how SGID bit works in terminal.

```bash
vagrant@ubuntu1:~$ sudo -i
root@ubuntu1:~# mkdir /developers
# Assume that you have already created a group called 'dev'
root@ubuntu1:~# chown :dev /developers
root@ubuntu1:~# chmod 2070 /developers
root@ubuntu1:~# ll -d /developers/
d---rws--- 2 root dev 4096 Sep  9 06:02 /developers/
# Assume that you have already created a user called 'testusr'
root@ubuntu1:~# usermod -aG dev testusr
root@ubuntu1:~# exit
logout
vagrant@ubuntu1:~$ sudo -i -u testusr
testusr@ubuntu1:~$ cd /developers
testusr@ubuntu1:/developers$ touch testfile
testusr@ubuntu1:/developers$ mkdir testdir
testusr@ubuntu1:/developers$ ll
total 12
d---rws---  3 root    dev  4096 Sep  9 06:22 ./
drwxr-xr-x 21 root    root 4096 Sep  9 05:55 ../
drwxrwsr-x  2 testusr dev  4096 Sep  9 06:22 testdir/
-rw-rw-r--  1 testusr dev     0 Sep  9 06:21 testfile
```

As you can see in the output, those newly created 'testfile' file and 'testdir' directory inherit the group owner 'dev' due to SGID bit set on `/developers` parent directory. It is quite clever to use the SGID bit for sharing a directory among team. But there is a downside with this permission since any other users of 'dev' group can remove or delete those file and directory created by 'testusr' account if that's not intended in the shared space. That's where the Sticky bit comes to save your bacon.

## Sticky Bit

The sticky bit is a permission in Linux and Unix-like operating systems that can be set on directories to control who can delete or modify files within that directory. It has a specific and important role in shared directories, particularly in locations like `/tmp`, where multiple users may need to create and manipulate files.

When the sticky bit is set on a directory, it allows users to create files and directories within that directory, but they can only delete or modify their own files and directories. Users cannot delete or modify files or directories owned by other users, even if they have write permissions on the parent directory. Only the owner of a file or directory or the superuser (root) can delete or modify files within a directory with the sticky bit set.

The primary use cases for the sticky bit are:

1. **Temporary Directories**: The most common use of the sticky bit is on directories like `/tmp`. By setting the sticky bit on `/tmp`, it ensures that any user can create temporary files and directories there, but they can only remove their own files, helping to prevent accidental or malicious deletion of other users' files.
2. **Shared Directories**: In shared directories where multiple users have write access, the sticky bit can prevent one user from interfering with the files of others. For example, in a shared project directory, you may want to set the sticky bit to ensure that each user can manage their own files and subdirectories without impacting others.

To set the sticky bit on a directory, you can use the `chmod` command with the numeric mode or symbolic mode. For example, to set the sticky bit on a directory:

```bash
chmod +t directoryname
```

Or using numeric mode:

```bash
chmod 1777 directoryname
```

In the numeric mode, the number "1" in the thousands place represents the sticky bit. The "777" specifies the owner, group, and other permissions.

In summary, the sticky bit in Linux permissions is used on directories to restrict the deletion or modification of files and directories within that directory to their respective owners or the superuser. It helps maintain security and integrity in shared directories, especially temporary and public directories where multiple users have access.

{% hint style="info" %}
When you only have SUID or SGID set, it displays _rwS_ but _rws_ when you have both SUID or SGID and executable(x) set for user or group. Likewise with Sticky bit, it displays _rwT_ when you only set Sticky bit but _rwt_ when you have both Sticky bit and executable(x) set for others.
{% endhint %}