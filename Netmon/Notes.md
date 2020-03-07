
# NetMon <!-- omit in toc -->

# Table of Contents <!-- omit in toc -->
- [Scan Results](#scan-results)
  - [Nmap](#nmap)
    - [Ports](#ports)
- [Expolit](#expolit)
    - [FTP](#ftp)
- [Post Exploit](#post-exploit)
- [Flags](#flags)
    - [User Flag](#user-flag)
    - [Root Flag](#root-flag)

# Scan Results

## Nmap
```
Nmap 7.80 scan initiated Thu Mar  5 17:29:40 2020 as: nmap -sC -sV -oA ./Documents/HackTheBox/Netmon/Scans/nmap 10.10.10.152

Nmap scan report for 10.10.10.152
Host is up (0.091s latency).
Not shown: 995 closed ports
PORT    STATE SERVICE      VERSION
21/tcp  open  ftp          Microsoft ftpd
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| 02-02-19  11:18PM                 1024 .rnd
| 02-25-19  09:15PM       <DIR>          inetpub
| 07-16-16  08:18AM       <DIR>          PerfLogs
| 02-25-19  09:56PM       <DIR>          Program Files
| 02-02-19  11:28PM       <DIR>          Program Files (x86)
| 02-03-19  07:08AM       <DIR>          Users
|_02-25-19  10:49PM       <DIR>          Windows
| ftp-syst: 
|_  SYST: Windows_NT
80/tcp  open  http         Indy httpd 18.1.37.13946 (Paessler PRTG bandwidth monitor)
|_http-server-header: PRTG/18.1.37.13946
| http-title: Welcome | PRTG Network Monitor (NETMON)
|_Requested resource was /index.htm
|_http-trane-info: Problem with XML parsing of /evox/about
135/tcp open  msrpc        Microsoft Windows RPC
139/tcp open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp open  microsoft-ds Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 5s, deviation: 0s, median: 5s
|_smb-os-discovery: ERROR: Script execution failed (use -d to debug)
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2020-03-05T17:30:02
|_  start_date: 2020-03-05T17:24:07

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Thu Mar  5 17:30:01 2020 -- 1 IP address (1 host up) scanned in 20.48 seconds
```

### Ports
* 21 - FTP:  Anonymous login
* 80 - HTTP: Custom web server probably made by PRTG

  Following are the standard SMB ports:

* 135 - msrpc: Microsoft Remote Procedure Call
* 139 - [Netbios](https://en.wikipedia.org/wiki/NetBIOS#Services)
* 445 - microsoft-ds - SMB: "Server Message Block."  
  SMB is a network protocol used by Windows-based computers that allows systems within the same network to share files. It allows computers connected to the same network or domain to access files from other local computers as easily as if they were on the computer's local hard drive.  
  With an administrative username we can use PSEXEC and get a remote shell.

# Expolit
### FTP
the ftp serer allows anonymous login to the box: `ftp 10.10.10.152`
the username is: `Anonymous` and the password can be left blank

![FTP Login](./Pictures/ftp_login.png)

Commands:
* `dir` - List files
* `dir -a` - Show hidden files.
* `ls`  - List files - jist like dir
* `cd`  - Change directory. spaces in the file path can be escaped by `\` (backslash) or use double quotes.
* `get` - Download a file.
* `pwd` - Print current directory.
* `lcd` - Canges the working directory on the local computer. By default, the working directory is the directory in which ftp was started.
* `?`   - Shows all the possible commands.

The User.txt file can be found on `Users\Public` folder.


# Post Exploit

# Flags

### User Flag
The User.txt file can be found on `Users\Public` folder. **dd58ce67b49e15105e88096c8d9255a5**


### Root Flag

