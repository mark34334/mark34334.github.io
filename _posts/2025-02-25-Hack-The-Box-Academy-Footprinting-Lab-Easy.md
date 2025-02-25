---
title: "Hack The Box: Academy footprinting lab easy box"
date: 2025-02-25 02:00:00 - 0500
categories: [ Hack_the_box , Academy , Footprinting]
tags: [Footprinting, FTP, SSH]
---

### Scenario

We were commissioned by the company Inlanefreight Ltd to test three different servers in their internal network. The company uses many different services, and the IT security department felt that a penetration test was necessary to gain insight into their overall security posture.

The first server is an internal DNS server that needs to be investigated. In particular, our client wants to know what information we can get out of these services and how this information could be used against its infrastructure. Our goal is to gather as much information as possible about the server and find ways to use that information against the company. However, our client has made it clear that it is forbidden to attack the services aggressively using exploits, as these services are in production.

Additionally, our teammates have found the following credentials "ceil:qwer1234", and they pointed out that some of the company's employees were talking about SSH keys on a forum.

The administrators have stored a flag.txt file on this server to track our progress and measure success. Fully enumerate the target and submit the contents of this file as proof.

Found: `ceil:qwer1234`


### Question:

 Enumerate the server carefully and find the flag.txt file. Submit the contents of this file as the answer.

### Finding the Flag.txt

The initials scan was done with the help of Nmap and here is the command use for it.

```
namp -sCV -p- --min-rate=10000 10.129.87.37
```


![alt text](</assets/img/Pasted image 20250225105529.png>)

and found that we have multiple services were running on the target but we focused on the ftp services, on the port 21 and another port 2121.

Using using given credentials we are about to login into the ftp services on the port 21
but i couldn't find any others information on that services.

![alt text](</assets/img/Pasted image 20250225105931.png>)

Then i login with other services on the port 2121then WOLA we got the hidden directory which also includes the .ssh.

![alt text](</assets/img/Pasted image 20250225110121.png>)

And using the get command download that contents on the .ssh directory. Which the **`id_rsa`** file is your SSH **private key** used for **public-key authentication**. It pairs with **`id_rsa.pub`** (public key). The public key is added to the server's `authorized_keys` file, allowing password less login by verifying the private key. Keep `id_rsa` secret. but we are about to login using the same id_rsa and it was exposed on the FTP.


Then The id_rsa file needs chmod 600 to set read and write permissions for the owner only. This prevents others from accessing your private key, ensuring it remains secret. SSH refuses to use keys with loose permissions to protect against unauthorized access.


```
get id_rsa
#then after downloaded we are changing the file permissions to owner-only
chmod 600 id_rsa
```

Then given file to the ssh for the password less authentication.

```
ssh -i id_rsa cail@<ip-address>
```

![alt text](</assets/img/Pasted image 20250225111204.png>)

Then Finally finding the flag of this user which can be found in `/home/flag`

![alt text](</assets/img/Pasted image 20250225111409.png>)

With that this comes to an end but a long way to go!