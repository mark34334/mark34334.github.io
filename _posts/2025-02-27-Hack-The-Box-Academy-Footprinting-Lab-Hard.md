---
title: "Hack The Box Academy: Footprinting Lab (Hard) — Walkthrough & Insights"
date: 2025-02-27 02:00:00 - 0500
categories: [ Hack_the_box , Academy , Footprinting]
tags: [Footprinting, FTP, SSH , Hard]
---

### Scenario

The third server is an MX and management server for the internal network. Subsequently, this server has the function of a backup server for the internal accounts in the domain. Accordingly, a user named `HTB` was also created here, whose credentials we need to access.

Enumerate the server carefully and find the username "HTB" and its password. Then, submit HTB's password as the answer.

### Let's Begin

Let's hack into this machine and before i you ever want any information about hacking related contents check out me on X.!!

### Nmap

I started with a TCP scan and along with script and version scan and using --min-rate=10000 to get faster results and analyzing the results it discovered some ports and i also run the scan against UDP scan and found a port open.

![alt text](</assets/img/Pasted image 20250227143756.png>)

![alt text](</assets/img/Pasted image 20250227143813.png>)

### Enumerating SNMP

Using the onesixtyone tool we found the community strings which is `backup` with that we'll use braa to fetch some more information from the service which resulted in nothing but using snmpwalk to fetch the files worked and found inside information about the target also with username & password `tom NMds732Js2761` using this try to login on the mail services to see anything useful over there.
![alt text](</assets/img/Pasted image 20250227145003.png>)

### Enumerating Mail services

using the telnet i connected to the pop3 service running on the port 110 and logging with the user name and the password which we found on previous enumeration on SNMP and in the services i found the ssh id_rsa key of the user tom and that's our way in using SSH.
![alt text](</assets/img/Pasted image 20250227145912.png>)

### Login as Tom

Using the private key i login as tom! and make sure that id_rsa file is only access by owner!
![alt text](</assets/img/Pasted image 20250227150823.png>)

```
nano id_rsa
chmod 600 id_rsa
ssh -i id_rsa tom@10.129.219.200
```

![alt text](</assets/img/Pasted image 20250227150946.png>)

by see that there is that mysql history there might be the database also in that let's see the .mysql_history and also .bash_history to determine what lies in it.

![alt text](</assets/img/Pasted image 20250227151118.png>)

it's look like that users information are stored in here which means also HTB password also should be there let's also see the .bash_history file
![alt text](</assets/img/Pasted image 20250227151259.png>)

it's seems that tom can access the databases in that case we already found the user name and the password for this let's try to connect to the database.
![alt text](</assets/img/Pasted image 20250227151446.png>)

We're are able to login and let's check the users databases in details for the information which we seek.
![alt text](</assets/img/Pasted image 20250227152454.png>)

Here i've done it!