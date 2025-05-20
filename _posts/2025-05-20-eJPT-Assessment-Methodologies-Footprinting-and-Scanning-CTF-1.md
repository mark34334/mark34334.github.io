---
title: "eJPT - Assessment Methodologies: Footprinting and Scanning CTF 1"
date: 2025-05-20 00:00:00 - 0500
categories: [ eJPT , Lab , Vulnerability Assessment]
tags: [Footprinting, Easy]

---

### Overview:

Reconnaissance is the initial phase of a penetration testing process where information about a target system is gathered to identify potential vulnerabilities. This phase involves actively or passively collecting data such as server headers, open ports, exposed directories, and system configurations. Techniques like scanning, querying DNS records, examining web application files (e.g., robots.txt), and analyzing response headers help uncover critical information that can aid in later exploitation phases. Effective reconnaissance allows testers to map the attack surface, prioritize targets, and plan their approach with minimal detection by the system's defenses.

This lab is designed to test your knowledge and skills in performing reconnaissance and identifying hidden information on a target web server.

### Lab Environment

In this lab environment, you will be provided with GUI access to a Kali Linux machine. The target machine will be accessible at **http://target.ine.local**.

**Objective:** Perform reconnaissance on the target and capture all the flags hidden within the environment.

**Flags to Capture:**

- **Flag 1**: The server proudly announces its identity in every response. Look closely; you might find something unusual.
- **Flag 2**: The gatekeeper's instructions often reveal what should remain unseen. Don't forget to read between the lines.
- **Flag 3**: Anonymous access sometimes leads to forgotten treasures. Connect and explore the directory; you might stumble upon something valuable.
- **Flag 4**: A well-named database can be quite revealing. Peek at the configurations to discover the hidden treasure.

### Tools

The best tools for this lab are:

- Nmap
- FTP
- MySQL

---

### Note

In this lab, the flag will follow the format: FLAG1_MD5Hash. For example, FLAG1_0f4d0db3668dd58cabb9eb409657eaa8. You need to submit only the MD5 hash string, excluding the underscore (_). For instance: 0f4d0db3668dd58cabb9eb409657eaa8.

---

### Question 1:

The server proudly announces its identity in every response. Look closely; you might find something unusual.

To gather the information about the target machine  we’ll use the trusty Nmap tools, and took a closer look of what it gives, and here is the command to run against machine. 

```bash
nmap -Pn -sV -sC -p- target.ine.local
```

```bash
┌──(root㉿INE)-[~]
└─# nmap -Pn -sV -sC -p- target.ine.local
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-05-20 15:02 IST
Nmap scan report for target.ine.local (192.11.142.3)
Host is up (0.000021s latency).
Not shown: 65527 closed tcp ports (reset)
PORT      STATE SERVICE  VERSION
21/tcp    open  ftp      vsftpd 3.0.5
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:192.11.142.2
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 3
|      vsFTPd 3.0.5 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| -rw-r--r--    1 0        0              22 Oct 28  2024 creds.txt
|_-rw-r--r--    1 0        0              39 May 20 09:24 flag.txt
22/tcp    open  ssh      OpenSSH 8.9p1 Ubuntu 3ubuntu0.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 a5:93:0f:6b:5a:77:f1:77:e8:2e:c9:31:e7:df:66:06 (ECDSA)
|_  256 b6:0d:e4:92:36:30:79:b7:31:91:3b:a0:1f:c1:ee:85 (ED25519)
25/tcp    open  smtp     Postfix smtpd
|_ssl-date: TLS randomness does not represent time
|_smtp-commands: localhost.members.linode.com, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, ENHANCEDSTATUSCODES, 8BITMIME, DSN, SMTPUTF8, CHUNKING
| ssl-cert: Subject: commonName=localhost
| Subject Alternative Name: DNS:localhost
| Not valid before: 2024-10-28T06:10:50
|_Not valid after:  2034-10-26T06:10:50
80/tcp    open  http     Werkzeug/3.0.6 Python/3.10.12
|_http-server-header: Werkzeug/3.0.6 Python/3.10.12
|_http-title: CTF Challenge
| http-robots.txt: 3 disallowed entries 
|_/photos /secret-info/ /data/
| fingerprint-strings:                                                                                                                                                                                                                     
|   GetRequest: 
|     HTTP/1.1 200 OK
|     Server: Werkzeug/3.0.6 Python/3.10.12
|     Date: Tue, 20 May 2025 09:32:16 GMT
|     Content-Type: text/html; charset=utf-8
|     Content-Length: 2557
|     Server: FLAG1_e33ed006765e48e1****************
|     Connection: close
|     <!DOCTYPE html>
|     <html lang="en">
|     <head>
|     <meta charset="UTF-8">
|     <meta name="viewport" content="width=device-width, initial-scale=1.0">
|     <link rel="shortcut icon" href="#">
|     <title>CTF Challenge</title>
|     <style>
|     body {
|     font-family: 'Arial', sans-serif;
|     margin: 0;
|     padding: 0;
|     background-color: #1c1c1c;
|     color: #fff;
|     background-color: #333;
|     padding: 15px;
|     text-align: center;
|     list-style: none;
|     margin: 0;
|     padding: 0;
|     display:
|   HTTPOptions: 
|     HTTP/1.1 200 OK
|     Server: Werkzeug/3.0.6 Python/3.10.12
|     Date: Tue, 20 May 2025 09:32:16 GMT
|     Content-Type: text/html; charset=utf-8
|     Allow: OPTIONS, HEAD, GET
|     Server: FLAG1_e33ed006765e48e1909ac79b9bfc6915
|     Content-Length: 0
|_    Connection: close
143/tcp   open  imap     Dovecot imapd (Ubuntu)
|_imap-capabilities: have OK more SASL-IR LOGIN-REFERRALS Pre-login IMAP4rev1 capabilities STARTTLS LOGINDISABLEDA0001 ENABLE listed LITERAL+ post-login IDLE ID
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=localhost
| Subject Alternative Name: DNS:localhost
| Not valid before: 2024-10-28T06:10:50
|_Not valid after:  2034-10-26T06:10:50
993/tcp   open  ssl/imap Dovecot imapd (Ubuntu)
|_ssl-date: TLS randomness does not represent time
|_imap-capabilities: have OK AUTH=PLAINA0001 LOGIN-REFERRALS SASL-IR IMAP4rev1 capabilities more Pre-login ENABLE listed LITERAL+ post-login IDLE ID
| ssl-cert: Subject: commonName=localhost
| Subject Alternative Name: DNS:localhost
| Not valid before: 2024-10-28T06:10:50
|_Not valid after:  2034-10-26T06:10:50
3306/tcp  open  mysql    MySQL 8.0.39-0ubuntu0.22.04.1
|_ssl-date: TLS randomness does not represent time
| mysql-info: 
|   Protocol: 10
|   Version: 8.0.39-0ubuntu0.22.04.1
|   Thread ID: 44
|   Capabilities flags: 65535
|   Some Capabilities: SupportsLoadDataLocal, ODBCClient, InteractiveClient, SupportsCompression, Speaks41ProtocolOld, DontAllowDatabaseTableColumn, IgnoreSigpipes, LongPassword, SwitchToSSLAfterHandshake, IgnoreSpaceBeforeParenthesis, Speaks41ProtocolNew, Support41Auth, SupportsTransactions, LongColumnFlag, FoundRows, ConnectWithDatabase, SupportsMultipleStatments, SupportsMultipleResults, SupportsAuthPlugins
|   Status: Autocommit
|   Salt: A//]\x1173\x18\x14IjAYa="O`J\x08
|_  Auth Plugin Name: caching_sha2_password
| ssl-cert: Subject: commonName=MySQL_Server_8.0.39_Auto_Generated_Server_Certificate
| Not valid before: 2024-10-28T06:11:13
|_Not valid after:  2034-10-26T06:11:13
33060/tcp open  mysqlx?
| fingerprint-strings: 
|   DNSStatusRequestTCP, LDAPSearchReq, NotesRPC, SSLSessionReq, TLSSessionReq, X11Probe, afp: 
|     Invalid message"
|     HY000
|   LDAPBindReq: 
|     *Parse error unserializing protobuf message"
|     HY000
|   oracle-tns: 
|     Invalid message-frame."
|_    HY000
2 services unrecognized despite returning data. If you know the service/version, please submit the following fingerprints at https://nmap.org/cgi-bin/submit.cgi?new-service :

```

With that we found the first flag plain in this fields. now let’s gather the information about the target and move further for finding more flags and exploiting system!!!

---

### Questions 2:

The gatekeeper's instructions often reveal what should remain unseen. Don't forget to read between the lines.

Gatekeeper’s haa it could possible tells us about the firewall or the robots text file on the system, since gatekeeper we’ll move on with robots first, 

![alt text](/assets/img/brave_ccBH7z713U.png)
![alt text](/assets/img/brave_tWqvko1uBN.png)
Bingo we found a few directory's let’s take a closer look, and we found a file called flag.txt which is what we are looking for.  
 
![alt text](/assets/img/brave_HtoCB5Sdvn.png)
with that we found the next flag!!

---

### Question 3:

Anonymous access sometimes leads to forgotten treasures. Connect and explore the directory; you might stumble upon something valuable.

For this we are already able to identify two files on the ftp with the help of Nmap scan results and the name of the files are `creds.txt` and `flag.txt`  here is the Nmap results we already able to get gather for the first flag or the information gathering with Nmap. this shows that anonymous access is allowed.

  

```bash
┌──(root㉿INE)-[~]
└─# nmap -Pn -sV -sC -p- target.ine.local
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-05-20 15:02 IST
Nmap scan report for target.ine.local (192.11.142.3)
Host is up (0.000021s latency).
Not shown: 65527 closed tcp ports (reset)
PORT      STATE SERVICE  VERSION
21/tcp    open  ftp      vsftpd 3.0.5
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:192.11.142.2
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 3
|      vsFTPd 3.0.5 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| -rw-r--r--    1 0        0              22 Oct 28  2024 creds.txt
|_-rw-r--r--    1 0        0              39 May 20 09:24 flag.txt
```

what is being there let’s download those files to our system with a command `get`

```bash
┌──(root㉿INE)-[~]
└─# ftp target.ine.local                                                                                                                                                                   
Connected to target.ine.local.
220 (vsFTPd 3.0.5)
Name (target.ine.local:root): anonymous
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> dir
229 Entering Extended Passive Mode (|||47206|)
150 Here comes the directory listing.
-rw-r--r--    1 0        0              22 Oct 28  2024 creds.txt
-rw-r--r--    1 0        0              39 May 20 09:24 flag.txt
226 Directory send OK.
ftp> get creds.txt
local: creds.txt remote: creds.txt
229 Entering Extended Passive Mode (|||22346|)
150 Opening BINARY mode data connection for creds.txt (22 bytes).
100% |**********************************************************************************************************************************************|    22      143.22 KiB/s    00:00 ETA
226 Transfer complete.
22 bytes received in 00:00 (48.60 KiB/s)
ftp> get flag.txt
local: flag.txt remote: flag.txt
229 Entering Extended Passive Mode (|||21695|)
150 Opening BINARY mode data connection for flag.txt (39 bytes).
100% |**********************************************************************************************************************************************|    39      718.60 KiB/s    00:00 ETA
226 Transfer complete.
39 bytes received in 00:00 (135.05 KiB/s)
ftp> 
```

let’s see what was in those files and we got a creds for a database and flag3!!!

```bash
┌──(root㉿INE)-[~]
└─# cat creds.txt flag.txt 
db_admin:password****
FLAG3_4005df6e7c89451e9d1f062******
```

---

### Question 4:

A well-named database can be quite revealing. Peek at the configurations to discover the hidden treasure.

With Nmap results saying that we having MySQL service running, we’ll login with it.

```bash
3306/tcp  open  mysql    MySQL 8.0.39-0ubuntu0.22.04.1
|_ssl-date: TLS randomness does not represent time
| mysql-info: 
|   Protocol: 10
|   Version: 8.0.39-0ubuntu0.22.04.1
|   Thread ID: 44
|   Capabilities flags: 65535
|   Some Capabilities: SupportsLoadDataLocal, ODBCClient, InteractiveClient, SupportsCompression, Speaks41ProtocolOld, DontAllowDatabaseTableColumn, IgnoreSigpipes, LongPassword, SwitchToSSLAfterHandshake, IgnoreSpaceBeforeParenthesis, Speaks41ProtocolNew, Support41Auth, SupportsTransactions, LongColumnFlag, FoundRows, ConnectWithDatabase, SupportsMultipleStatments, SupportsMultipleResults, SupportsAuthPlugins
|   Status: Autocommit
|   Salt: A//]\x1173\x18\x14IjAYa="O`J\x08
|_  Auth Plugin Name: caching_sha2_password
| ssl-cert: Subject: commonName=MySQL_Server_8.0.39_Auto_Generated_Server_Certificate
| Not valid before: 2024-10-28T06:11:13
|_Not valid after:  2034-10-26T06:11:13
33060/tcp open  mysqlx?
| fingerprint-strings: 
|   DNSStatusRequestTCP, LDAPSearchReq, NotesRPC, SSLSessionReq, TLSSessionReq, X11Probe, afp: 
|     Invalid message"
|     HY000
|   LDAPBindReq: 
|     *Parse error unserializing protobuf message"
|     HY000
|   oracle-tns: 
|     Invalid message-frame."
|_    HY000
```

here is the MySQL command to connect to services 

```bash
mysql -u db_admin -p -h target.ine.local
```

![alt text](/assets/img/brave_mwjhzJnsyR.png)
With that room comes to an end!!!