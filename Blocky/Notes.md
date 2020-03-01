
# Box Name <!-- omit in toc -->

# Table of Contents <!-- omit in toc -->
- [Scan Results](#scan-results)
  - [Nmap](#nmap)
    - [Ports](#ports)
  - [Gobuster](#gobuster)
    - [Directories](#directories)
- [Expolit](#expolit)
- [Post Exploit](#post-exploit)
- [Flags](#flags)
    - [User Flag](#user-flag)
    - [Root Flag](#root-flag)

# Scan Results

## Nmap

### Ports
* Port 80 is open - we get a website

## Gobuster
install gobuster if you dont have it

```
apt-get install gobuster
```

and run it

```
root@kali:~# gobuster dir -u 10.10.10.37 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://10.10.10.37
[+] Threads:        10
[+] Wordlist:       /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Timeout:        10s
===============================================================
2020/03/01 15:37:36 Starting gobuster
===============================================================
/wiki (Status: 301)
/wp-content (Status: 301)
/plugins (Status: 301)
/wp-includes (Status: 301)
/javascript (Status: 301)
/wp-admin (Status: 301)
/phpmyadmin (Status: 301)
/server-status (Status: 403)
===============================================================
2020/03/01 16:09:22 Finished
===============================================================
```

### Directories
* /wiki 
* /wp-content 
* /plugins 
* /wp-includes
* /javascript 
* /phpmyadmin

Download files from /plugins folder and extracted BlockyCore.jar using

```
jar xf ./BlockyCore.jar
```
inside the "com" we found a BlockyCore.class file. this is a java class file but we can still try to view as a text file

If we do so and look carefuly we see "root" and after that something that looks like a password.

we get the password for the MySQL database

```
PhpMyAdmin:  
Username: root  
Password: 8YsqfCTnvxAUeduzjNSXe22
```

click on Databses at the top left  
![phoMyAdmin Header](./Pictures/phpmyadmin.png)

we can see a wordpress database  
![Databases](./Pictures/databses.png)  

which includes wp_users table
inside we can see the username and password for the wordpress website

![WordPress User](./Pictures/wordpress_user.png)  

```
WordPress:  
username: Notch  
Password: $P$BiVoTj899ItS1EZnMhqeqVbrZI4Oq0/  
Email:    notch@blockcraftfake.com
```

The password field is hashed. In order to change it we edit the entry.
change the user_pass column such that the _Function_ field is _MD5_ and the _Value_ is "password".  

![WordPress User](./Pictures/pass_change.png)   

Scroll down and press _GO_   
The new password is now "password" and we now we can login into wordpress as Notch

# Expolit

# Post Exploit

# Flags

### User Flag

### Root Flag

