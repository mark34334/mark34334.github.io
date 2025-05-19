---
title: "eJPT - Assessment Methodologies: Information Gathering CTF 1"
date: 2025-05-18 00:00:00 - 0500
categories: [ eJPT , Lab , Vulnerability Assessment]
tags: [Footprinting, Easy]
image:
    path: /assets/img/2.gif
    alt: image
---



This lab focuses on information gathering and reconnaissance techniques to analyze a target website. Participants will explore various aspects of the website to uncover potential vulnerabilities, sensitive files, and misconfigurations. By leveraging investigative skills, they will learn how to identify critical information that could assist in further penetration testing or exploitation.

### Completing Skill Check Labs

Skill Check Labs are interactive, hands-on exercises designed to validate the knowledge and skills you’ve gained in this course through real-world scenarios. Each lab presents practical tasks that require you to apply what you’ve learned. Unlike other INE labs, solutions are not provided, challenging you to demonstrate your understanding and problem-solving abilities. Your performance is graded, allowing you to track progress and measure skill growth over time.

### Lab Environment

A website is accessible at **http://target.ine.local**. Perform reconnaissance and capture the following flags.

- **Flag 1:** This tells search engines what to and what not to avoid.
- **Flag 2:** What website is running on the target, and what is its version?
- **Flag 3:** Directory browsing might reveal where files are stored.
- **Flag 4:** An overlooked backup file in the webroot can be problematic if it reveals sensitive configuration details.
- **Flag 5:** Certain files may reveal something interesting when mirrored.

### Tools

- Firefox
- Curl
- HTTrack

---

Note

In this lab, the flag will follow the format: FLAG1{MD5Hash} OR FL@G1{MD5Hash}. For example, FLAG1{0f4d0db3668dd58cabb9eb409657eaa8}. You need to submit only the MD5

---

### Question 1:

This tells search engines what to and what not to avoid.

Let’s start with the Nmap the first and ever friend in the phase of information gathering and here we have 5 flags to find and let’s chase one by one. The first question says about search engines of what to avoid which simple means the file called **`robots.txt`**  so here are the commands and screenshots for finding the flag1! and we also found the flag2 here in the scan results!!

```bash
cat /etc/hosts

┌──(root㉿INE)-[~]
└─# nmap -sC -sV target.ine.local | tee nmap.txt
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-05-08 14:42 IST
Nmap scan report for target.ine.local (192.165.34.3)
Host is up (0.000026s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
| http-robots.txt: 1 disallowed entry 
|_/wp-admin/
|_http-title: INE
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-generator: WordPress 6.5.3 - FL@G2{168e350f10354489*********}
MAC Address: 02:42:C0:A5:22:03 (Unknown)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 9.27 seconds
```
![alt text](/assets/img/image1232231.png)

---

### Question 2:

What website is running on the target, and what is its version?

The question asked us to find the what website is running and its version and here we can use the Nmap to find the results , here is the more concise command for this scenarios.

```bash
┌──(root㉿INE)-[~]
└─# nmap -A --min-rate=10000 target.ine.local                                                                                                                                              
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-05-08 14:48 IST
Nmap scan report for target.ine.local (192.165.34.3)
Host is up (0.000067s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-generator: WordPress 6.5.3 - FL@G2{168e350f10354489872edc18901c9b3d}
|_http-server-header: Apache/2.4.41 (Ubuntu)
| http-robots.txt: 1 disallowed entry 
|_/wp-admin/
|_http-title: INE
MAC Address: 02:42:C0:A5:22:03 (Unknown)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.94SVN%E=4%D=5/8%OT=80%CT=1%CU=32958%PV=N%DS=1%DC=D%G=Y%M=0242C0
OS:%TM=681C76F2%P=x86_64-pc-linux-gnu)SEQ(SP=100%GCD=1%ISR=10F%TI=Z%CI=Z%TS
OS:=A)SEQ(SP=100%GCD=1%ISR=10F%TI=Z%CI=Z%II=I%TS=A)OPS(O1=M5B4ST11NW7%O2=M5
OS:B4ST11NW7%O3=M5B4NNT11NW7%O4=M5B4ST11NW7%O5=M5B4ST11NW7%O6=M5B4ST11)WIN(
OS:W1=7C70%W2=7C70%W3=7C70%W4=7C70%W5=7C70%W6=7C70)ECN(R=Y%DF=Y%T=40%W=7D78
OS:%O=M5B4NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R
OS:=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%
OS:A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%
OS:DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIP
OS:L=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD=S)

Network Distance: 1 hop

TRACEROUTE
HOP RTT     ADDRESS
1   0.07 ms target.ine.local (192.165.34.3)

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 19.66 seconds
```

---

### Question 3:

Directory browsing might reveal where files are stored.

First we run the dirb scan scan against the target and we already know the service and version the target is running from our Nmap scan.

 

```bash
┌──(root㉿INE)-[~]
└─# dirb http://target.ine.local/wp-content
-----------------
DIRB v2.22    
By The Dark Raver
-----------------
START_TIME: Thu May  8 14:53:48 2025
URL_BASE: http://target.ine.local/wp-content/
WORDLIST_FILES: /usr/share/dirb/wordlists/common.txt
-----------------
GENERATED WORDS: 4612                                                          
---- Scanning URL: http://target.ine.local/wp-content/ ----
+ http://target.ine.local/wp-content/index.php (CODE:200|SIZE:0)                                                                                                                          
==> DIRECTORY: http://target.ine.local/wp-content/plugins/                                                                                                                                
==> DIRECTORY: http://target.ine.local/wp-content/themes/                                                                                                                                 
==> DIRECTORY: http://target.ine.local/wp-content/uploads/                                                                                                                                                                                                                                                                                                                      
---- Entering directory: http://target.ine.local/wp-content/plugins/ ----
+ http://target.ine.local/wp-content/plugins/index.php (CODE:200|SIZE:0)                                                                                                                                                                                                                                                                                                         
---- Entering directory: http://target.ine.local/wp-content/themes/ ----
+ http://target.ine.local/wp-content/themes/index.php (CODE:200|SIZE:0)                                                                                                                                                                                                                                                                                                         
---- Entering directory: http://target.ine.local/wp-content/uploads/ ----
(!) WARNING: Directory IS LISTABLE. No need to scan it.                        
    (Use mode '-w' if you want to scan it anyway)                                                                           
-----------------
END_TIME: Thu May  8 14:53:52 2025
DOWNLOADED: 13836 - FOUND: 3
```

based on the information we found with dirb we are able to find the flag on this path [`http://target.ine.local/wp-content/uploads/`](http://target.ine.local/wp-content/uploads/)  finally found the flag 3!!!

![alt text](/assets/img/image121212.png)

---

### Question 4:

An overlooked backup file in the webroot can be problematic if it reveals sensitive configuration details.

First using the dirb we’ll start the enumeration process and right now we should be specific with the extension, here we’ll be focusing on `.bak`  which means backup files and here comes the code

```bash
┌──(root㉿INE)-[~]
└─# dirb http://target.ine.local -w /usr/share/dirb/wordlists/big.txt  -X .bak

-----------------
DIRB v2.22    
By The Dark Raver
-----------------

START_TIME: Mon May 19 19:55:15 2025
URL_BASE: http://target.ine.local/
WORDLIST_FILES: /usr/share/dirb/wordlists/common.txt
OPTION: Not Stopping on warning messages
EXTENSIONS_LIST: (.bak) | (.bak) [NUM = 1]

-----------------

GENERATED WORDS: 4612                                                          

---- Scanning URL: http://target.ine.local/ ----
+ http://target.ine.local/wp-config.bak (CODE:200|SIZE:3438)                                                                                                                              
                                                                                                                                                                                          
-----------------
END_TIME: Mon May 19 19:55:17 2025
DOWNLOADED: 4612 - FOUND: 1
```

Then we’ll be using the curl to check what’s with the file, and yep we got the flag!! 

---

### Question 5:

Certain files may reveal something interesting when mirrored.

Here we have come to the next question, for this we’ll use the tool called httrack which is used for phishing that basically clones the website name to it.

```bash
┌──(root㉿INE)-[~]
└─# httrack http://target.ine.local -O target.html
WARNING! You are running this program as root!
It might be a good idea to run as a different user
Mirror launched on Mon, 19 May 2025 20:01:54 by HTTrack Website Copier/3.49-5 [XR&CO'2014]
mirroring http://target.ine.local with the wizard help..
* target.ine.local/wp-admin/load-scripts.php?c=0&load%5Bchunk_0%5D=jquery-core,jquery-migrate,zxcvbn-async,wp-polyfill-inert,regenerator-runtime,wp-polyfill,wp-hooks&ver=6.5.3 (159181 byt40/52: target.ine.local/wp-admin/load-scripts.php?c=0&load%5Bchunk_0%5D=jquery-core,jquery-migrate,zxcvbn-async,wp-polyfill-inert,regenerator-runtime,wp-polyfill,wp-hooks&ver=6.5.3 (0 bytDone.: target.ine.local/wp-login.php (4105 bytes) - OK
Thanks for using HTTrack!
```

Now that we cloned the site, let’s take a look and hope around the files we got from target websites. we got the flag form the file called `xmlrpc0db0.php`  

```bash
┌──(root㉿INE)-[~/target.html/target.ine.local]
└─# cat xmlrpc0db0.php                                                                                                                                                                     
<?xml version="1.0" encoding="UTF-8"?><rsd version="1.0" xmlns="http://archipelago.phrasewise.com/rsd">
        <service>
                <engineName>WordPress</engineName>
                <engineLink>https://wordpress.org/</engineLink>
                <homePageLink>http://target.ine.local</homePageLink>
                <apis>
                        <api name="WordPress" blogID="1" preferred="true" apiLink="http://target.ine.local/xmlrpc.php" />
                        <api name="Movable Type" blogID="1" preferred="false" apiLink="http://target.ine.local/xmlrpc.php" />
                        <api name="MetaWeblog" blogID="1" preferred="false" apiLink="http://target.ine.local/xmlrpc.php" />
                        <api name="Blogger" blogID="1" preferred="false" apiLink="http://target.ine.local/xmlrpc.php" />
                        <api name="FLAG5{4005c7e70********************}" blogID="1" preferred="false" apiLink="http://target.ine.local/xmlrpc.php" />
                                <api name="WP-API" blogID="1" preferred="false" apiLink="http://target.ine.local/index.php/wp-json/" />
                        </apis>
        </service>
</rsd>

```

With that we found the last flag!!!