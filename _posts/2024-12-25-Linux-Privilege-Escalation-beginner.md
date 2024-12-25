---
title: "Beginner's Linux Privilege Escalation: From User to Root"
date: 2024-12-25 22:00:00 - 0500
categories: [Exploiting, SQL-Injection]
tags: [Docker, SQL-Injection]
---


---


## Introduction 
---
Hey guys in the post we're gonna learn about a small Privilege Escalation which i learn it form the hack the box academy module!!

So this was a Linux environment which has two user namely 
* User1
* User2
* Root

Our Question is to login as user1 using the credential given then privilege escalation into **User2** then again gain the access to **Root**

---

## Into User1 
---

Navigating round the user1 and we found that the **User2** `/bin/bash` file can be accessed without the password which is found by running this command `sudo -l`

which lists the file which are run by **User1** using that we can be able to access the *tty* of Use **User2** then by run the file as **User2** using the command

```bash
sudo -l
sudo -u user2 /bin/bash
```
![alt text](</assets/img/Pasted image 20241225194336.png>)

And here we have gained the terminal of **User2**

## From user2 - to - Root
---
After spending some time around in the **User2** I Found that `ssh` file can be read by us, So i have copied `id_rsa` file from the machine into the local machine.

Now we can login by using the `ssh` private key `id_rsa` and be sure that file is in the proper permission to change the permission use the command `chmod 600 <file_name?>` 

Here is the command to which gain us into the root!
```bash
ssh root@<ip_addresss?> -p <Port_number?> -i <id_rsa_file?> 
```

![alt text](</assets/img/Pasted image 20241225220547.png>)


### Explanation: 
- Replace `<ip_address>` with the actual IP address of the remote server. 
- Replace `<Port_number>` with the port number (e.g., `22` or another custom port). 
- Replace `<path_to_id_rsa_file>` with the path to your private key file (e.g., `~/.ssh/id_rsa` or `./id_rsa`).

![alt text](</assets/img/Pasted image 20241225220746.png>)

![alt text](</assets/img/Pasted image 20241225220815.png>)
