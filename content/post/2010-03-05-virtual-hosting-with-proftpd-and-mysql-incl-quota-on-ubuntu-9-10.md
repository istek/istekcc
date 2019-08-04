---
title: Virtual Hosting With Proftpd And MySQL (Incl. Quota) On Ubuntu 9.10
date: 2010-03-05T05:41:28+00:00
layout: post
categories:
  - Linux
tags:
  - proftpd
  - mysql
---
This document describes how to install a Proftpd server that uses virtual users from a MySQL database instead of real system users. This is much more performant and allows to have thousands of ftp users on a single machine. In addition to that I will show the use of quota with this setup.

For the administration of the MySQL database you can use web based tools like phpMyAdmin which will also be installed in this howto. phpMyAdmin is a comfortable graphical interface which means you do not have to mess around with the command line.

This tutorial is based on Ubuntu 9.10. You should already have set up a basic Ubuntu 9.10 server system.

This howto is meant as a practical guide; it does not cover the theoretical backgrounds. They are treated in a lot of other documents in the web.

This document comes without warranty of any kind! I want to say that this is not the only way of setting up such a system. There are many ways of achieving this goal but this is the way I take. I do not issue any guarantee that this will work for you!

<!--more-->
### 1 Preliminary Note

In this tutorial I use the hostname _server1.example.com_ with the IP address _192.168.0.100_. These settings might differ for you, so you have to replace them where appropriate.

Make sure that you are logged in as root:
```
sudo su
```
#### 1.1 Change The Default Shell

`/bin/sh` is a symlink to `/bin/dash`, however we need `/bin/bash`, not `/bin/dash`. Therefore we do this:
```
    dpkg-reconfigure dash

_Install dash as /bin/sh?_ <– No
```
#### <span id="12_Disable_AppArmor">1.2 Disable AppArmor

AppArmor is a security extension (similar to SELinux) that should provide extended security. In my opinion you don’t need it to configure a secure system, and it usually causes more problems than advantages (think of it after you have done a week of trouble-shooting because some service wasn’t working as expected, and then you find out that everything was ok, only AppArmor was causing the problem). Therefore I disable it.

We can disable it like this:
```
/etc/init.d/apparmor stop
update-rc.d -f apparmor remove
aptitude remove apparmor apparmor-utils
```

### 2 Install MySQL And phpMyAdmin

This can all be installed with one single command:
```
aptitude install mysql-server mysql-client phpmyadmin apache2
```

You will be asked to provide a password for the MySQL root user – this password is valid for the user _root@localhost_ as well as _root@server1.example.com_, so we don't have to specify a MySQL root password manually later on:
```
New password for the MySQL “root” user:* <– yourrootsqlpassword
Repeat password for the MySQL “root” user:* <– yourrootsqlpassword
```

In addition to this, you will see the following questions:
```
_Web server to reconfigure automatically:_ <– apache2</span>

_Configure database for phpmyadmin with dbconfig-common?_ <– No </span>
```
### 3 Install Proftpd With MySQL Support

For Ubuntu there is a pre-configured `proftpd-mod-mysql` package available. Install it as a **standalone** daemon like this:
```
aptitude install proftpd-mod-mysql
```

You will be asked the following question:
```
*Run proftpd:* <– standalone
```

Then we create an ftp group (_ftpgroup_) and user (_ftpuser_) that all our virtual users will be mapped to. Replace the group- and userid 2001 with a number that is free on your system:
```
groupadd -g 2001 ftpgroup
useradd -u 2001 -s /bin/false -d /bin/null -c "proftpd user" -g ftpgroup ftpuser
```

### 4 Create The MySQL Database For Proftpd

Now we create a database called ftp and a MySQL user named proftpd which the proftpd daemon will use later on to connect to the ftp database:

mysql -u root -p

```
CREATE DATABASE ftp;
GRANT SELECT, INSERT, UPDATE, DELETE ON ftp.* TO 'proftpd'@'localhost' IDENTIFIED BY 'password';
FLUSH PRIVILEGES;
```

Replace the string _password_ with whatever password you want to use for the MySQL user _proftpd_. Still on the MySQL shell, we create the database tables we need:
```
USE ftp;
CREATE TABLE ftpgroup (
 groupname varchar(16) NOT NULL default '',
 gid smallint(6) NOT NULL default '5500',
 members varchar(16) NOT NULL default ”,
 KEY groupname (groupname)
 ) TYPE=MyISAM COMMENT='ProFTP group table';

CREATE TABLE ftpquotalimits (
 name varchar(30) default NULL,
 quota_type enum('user','group','class','all') NOT NULL default 'user',
 per_session enum('false','true') NOT NULL default 'false',
 limit_type enum('soft','hard') NOT NULL default 'soft',
 bytes_in_avail bigint(20) unsigned NOT NULL default '0',
 bytes_out_avail bigint(20) unsigned NOT NULL default '0',
 bytes_xfer_avail bigint(20) unsigned NOT NULL default '0',
 files_in_avail int(10) unsigned NOT NULL default '0',
 files_out_avail int(10) unsigned NOT NULL default '0',
 files_xfer_avail int(10) unsigned NOT NULL default '0'
 ) TYPE=MyISAM;

CREATE TABLE ftpquotatallies (
 name varchar(30) NOT NULL default '',
 quota_type enum('user','group','class','all') NOT NULL default 'user',
 bytes_in_used bigint(20) unsigned NOT NULL default '0',
 bytes_out_used bigint(20) unsigned NOT NULL default '0',
 bytes_xfer_used bigint(20) unsigned NOT NULL default '0',
 files_in_used int(10) unsigned NOT NULL default '0',
 files_out_used int(10) unsigned NOT NULL default '0',
 files_xfer_used int(10) unsigned NOT NULL default '0'
 ) TYPE=MyISAM;

CREATE TABLE ftpuser (
 id int(10) unsigned NOT NULL auto_increment,
 userid varchar(32) NOT NULL default '',
 passwd varchar(32) NOT NULL default '',
 uid smallint(6) NOT NULL default '5500',
 gid smallint(6) NOT NULL default '5500',
 homedir varchar(255) NOT NULL default '',
 shell varchar(16) NOT NULL default '/sbin/nologin',
 count int(11) NOT NULL default '0',
 accessed datetime NOT NULL default '0000-00-00 00:00:00',
 modified datetime NOT NULL default '0000-00-00 00:00:00',
 PRIMARY KEY (id),
 UNIQUE KEY userid (userid)
 ) TYPE=MyISAM COMMENT='ProFTP user table';

quit;
```

As you may have noticed, with the _quit;_ command we have left the MySQL shell and are back on the Linux shell.

BTW, (I’m assuming that the hostname of your ftp server system is _server1.example.com_) you can access phpMyAdmin under _<http://server1.example.com/phpmyadmin/>_ (you can use the IP address instead of _server1.example.com_) in a browser and log in as proftpd. Then you can have a look at the database. Later on you can use phpMyAdmin to manage your Proftpd server.

### 5 Configure Proftpd

Open _/etc/proftpd/modules.conf_…
```
vi /etc/proftpd/modules.conf
```

… and enable the following three modules:
```
# Install proftpd-mod-mysql or proftpd-mod-pgsql to use this
LoadModule mod_sql.c
[…]
# Install proftpd-mod-mysql to use this
LoadModule mod_sql_mysql.c
[…]
# Install proftpd-mod-pgsql or proftpd-mod-mysql to use this
LoadModule mod_quotatab_sql.c
```

Then open _/etc/proftpd/proftpd.conf_ and comment out the following lines:
```
vi /etc/proftpd/proftpd.conf
```
```
    #
    #QuotaEngine off
    #
    Further down in the file, add the following lines:
     #
     # Alternative authentication frameworks
     #
     #Include /etc/proftpd/ldap.conf
     #Include /etc/proftpd/sql.conf
    DefaultRoot ~

    SQLBackend mysql
     # The passwords in MySQL are encrypted using CRYPT
     SQLAuthTypes Plaintext Crypt
     SQLAuthenticate users groups

    # used to connect to the database
     # databasename@host database_user user_password
     SQLConnectInfo ftp@localhost proftpd password

    # Here we tell ProFTPd the names of the database columns in the “usertable”
     # we want it to interact with. Match the names with those in the db
     SQLUserInfo ftpuser userid passwd uid gid homedir shell

    # Here we tell ProFTPd the names of the database columns in the “grouptable”
     # we want it to interact with. Again the names match with those in the db
     SQLGroupInfo ftpgroup groupname gid members

    # set min UID and GID – otherwise these are 999 each
     SQLMinID 500

    # create a user’s home directory on demand if it doesn’t exist
     CreateHome on

    # Update count every time user logs in
     SQLLog PASS updatecount
     SQLNamedQuery updatecount UPDATE “count=count+1, accessed=now() WHERE userid=’%u'” ftpuser

    # Update modified everytime user uploads or deletes a file
     SQLLog STOR,DELE modified
     SQLNamedQuery modified UPDATE “modified=now() WHERE userid=’%u'” ftpuser

    # User quotas
     # ===========
     QuotaEngine on
     QuotaDirectoryTally on
     QuotaDisplayUnits Mb
     QuotaShowQuotas on

    SQLNamedQuery get-quota-limit SELECT “name, quota_type, per_session, limit_type, bytes_in_avail, bytes_out_avail, bytes_xfer_avail, files_in_avail, files_out_avail, files_xfer_avail FROM ftpquotalimits WHERE name = ‘%{0}’ AND quota_type = ‘%{1}'”

    SQLNamedQuery get-quota-tally SELECT “name, quota_type, bytes_in_used, bytes_out_used, bytes_xfer_used, files_in_used, files_out_used, files_xfer_used FROM ftpquotatallies WHERE name = ‘%{0}’ AND quota_type = ‘%{1}'”

    SQLNamedQuery update-quota-tally UPDATE “bytes_in_used = bytes_in_used + %{0}, bytes_out_used = bytes_out_used + %{1}, bytes_xfer_used = bytes_xfer_used + %{2}, files_in_used = files_in_used + %{3}, files_out_used = files_out_used + %{4}, files_xfer_used = files_xfer_used + %{5} WHERE name = ‘%{6}’ AND quota_type = ‘%{7}'” ftpquotatallies

    SQLNamedQuery insert-quota-tally INSERT “%{0}, %{1}, %{2}, %{3}, %{4}, %{5}, %{6}, %{7}” ftpquotatallies

    QuotaLimitTable sql:/get-quota-limit
    QuotaTallyTable sql:/get-quota-tally/update-quota-tally/insert-quota-tally

    RootLogin off
    RequireValidShell off
```

Make sure that you replace the string _password_ with the real password for the MySQL user _proftpd_ in the line _SQLConnectInfo_!

Then restart Proftpd:
```
/etc/init.d/proftpd restart
```

### 6 Populate The Database And Test

To populate the database you can use the MySQL shell:
```
mysql -u root -p
```
```
USE ftp;
```

First we create an entry in the table ftpgroup. It contains the groupname, the groupid and the username of the ftp group/user we created at the end of step two (replace the groupid appropriately if you use another one than 2001):
```
INSERT INTO `ftpgroup` (`groupname`, `gid`, `members`) VALUES ('ftpgroup', 2001, 'ftpuser');
```

Now we are done with the table ftpgroup. We do not have to create further entries here. Whenever you create a new virtual ftp user, you do this in the tables ftpquotalimits and ftpuser. So let us create our first user exampleuser with a quota of 15MB and the password secret (we are still in the MySQL shell):
```
INSERT INTO `ftpquotalimits` (`name`, `quota_type`, `per_session`, `limit_type`, `bytes_in_avail`, `bytes_out_avail`, `bytes_xfer_avail`, `files_in_avail`, `files_out_avail`, `files_xfer_avail`) VALUES ('exampleuser', 'user', 'true', 'hard', 15728640, 0, 0, 0, 0, 0);

INSERT INTO `ftpuser` (`id`, `userid`, `passwd`, `uid`, `gid`, `homedir`, `shell`, `count`, `accessed`, `modified`) VALUES (1, 'exampleuser', 'secret', 2001, 2001, '/home/www.example.com', '/sbin/nologin', 0, '', '');

quit;
```

(Do not forget to replace the groud- and userid 2001 appropriately in the last INSERT statement if you are using other values than in this tutorial!)

Now open your FTP client program on your work station (something like FileZilla, WS_FTP, SmartFTP or gFTP) and try to connect. As hostname you use server1.example.com (or the IP address of the system), the username is exampleuser, and the password is secret.

If you are able to connect – congratulations! If not, something went wrong.

Now, if you run

`ls -l /home/`

you should see that the directory _/home/www.example.com_ (exampleuser’s home directory) has been automatically created, and it is owned by ftpuser and ftpgroup (the user/group we created at the end of step two):
```
*root@server1:~# ls -l /home/
 total 12
 drwxr-xr-x 2 administrator administrator 4096 2010-01-04 15:38 administrator
 drwxr-xr-x 2 ftp nogroup 4096 2010-01-04 17:10 ftp
 drwx------ 2 ftpuser ftpgroup 4096 2010-01-04 17:26 www.example.com
 root@server1:~#*
```

### 7 Database Administration

For most people it is easier if they have a graphical front-end to MySQL; therefore you can also use phpMyAdmin (in this example under _<http://server1.example.com/phpmyadmin/>_) to administrate the _ftp_ database.

Whenever you create a new user, you only have to create entries in the tables _ftpquotalimits_ and _ftpuser_ so I will explain the columns of these tables here:

**ftpuser Table:**

The important columns are these (the others are handled by MySQL or Proftpd automatically, so do not fill these manually!):

* passwd: The unencrypted (i.e., clear-text) password of the user.
* homedir: The home directory of the virtual Proftpd user (e.g. /home/www.example.com). If it does not exist, it will be created when the new user logs in the first time via FTP. The virtual user will be jailed into this home directory, i.e., he cannot access other directories outside his home directory.
* shell: It is ok if you fill in /sbin/nologin here by default.

**ftpquotalimits Table:**

The important columns are these (the others are handled by MySQL or Proftpd automatically, so do not fill these manually!):

* name: The name of the virtual Proftpd user (e.g. exampleuser).
* quota_type: user or group. Normally, we use user here.
* per_session: true or false. true means the quota limits are valid only for a session. For example, if the user has a quota of 15 MB, and he has uploaded 15 MB during the current session, then he cannot upload anything more. But if he logs out and in again, he again has 15 MB available. false means, that the user has 15 MB, no matter if he logs out and in again.
* limit_type: hard or soft. A hard quota limit is a never-to-exceed limit, while a soft quota can be temporarily exceeded. Normally you use hard here.
* bytes_in_avail: Upload limit in bytes (e.g. 15728640 for 15 MB). 0 means unlimited.
* bytes_out_avail: Download limit in bytes. 0 means unlimited.
* bytes_xfer_avail: Transfer limit in bytes. The sum of uploads and downloads a user is allowed to do. 0 means unlimited.
* files_in_avail: Upload limit in files. 0 means unlimited.
* files_out_avail: Download limit in files. 0 means unlimited.
* files_xfer_avail: Tranfer limit in files. 0 means unlimited.

The _ftpquotatallies_ table is used by Proftpd internally to manage quotas so you do not have to make entries there!

### 8 Anonymous FTP

If you want to create an anonymous ftp account (an ftp account that everybody can login to without a password), you can do it like this:

First we create a user and group with the name _anonymous_ftp_. The user has the home directory _/home/anonymous_ftp_:
```
groupadd -g 2002 anonymous_ftp
useradd -u 2002 -s /bin/false -d /home/anonymous_ftp -m -c “Anonymous FTP User” -g anonymous_ftp anonymous_ftp
```

(Replace _2002_ with a group-/userid that is free on your system.)

A few files beginning with a . have been created by the last command (_useradd_) in the _/home/anonymous_ftp_ directory. We don’t need them, so we delete them:
```
cd /home/anonymous_ftp
rm -f .bash_logout
rm -f .profile
rm -f .bashrc
```

Then we create the directory _/home/anonymous_ftp/incoming_ which will allow anonymous users to upload files:
```
mkdir /home/anonymous_ftp/incoming
chown anonymous_ftp:nogroup /home/anonymous_ftp/incoming
```

And finally, open _/etc/proftpd/proftpd.conf_ and append the following directives to it:
```
vi /etc/proftpd/proftpd.conf
```
```
User anonymous_ftp
Group nogroup
 # We want clients to be able to login with “anonymous” as well as “ftp”
UserAlias anonymous anonymous_ftp
 # Cosmetic changes, all files belongs to ftp user
DirFakeUser on anonymous_ftp
DirFakeGroup on anonymous_ftp

RequireValidShell off

# Limit the maximum number of anonymous logins
 MaxClients 10

# We want ‘welcome.msg’ displayed at login, and ‘.message’ displayed
# in each newly chdired directory.
 DisplayLogin welcome.msg
 DisplayChdir .message

# Limit WRITE everywhere in the anonymous chroot
DenyAll

# Uncomment this if you’re brave.
# Umask 022 is a good standard umask to prevent new files and dirs
# (second parm) from being group and world writable.
Umask 022 022

DenyAll

AllowAll
```

Finally restart Proftpd:
```
/etc/init.d/proftpd restart
```

Now anonymous users can login, and they can download files from _/home/anonymous_ftp_, but uploads are limited to _/home/anonymous_ftp/incoming_ (and once a file is uploaded into _/home/anonymous_ftp/incoming_, it cannot be read nor downloaded from there; the server admin has to move it into _/home/anonymous_ftp_ first to make it available to others).

(Please note: You can only have one anonymous ftp account per IP address!)