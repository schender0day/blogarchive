---
title: "Bruteforce Series - Bruteforce attack SSH and FTP - 03"
seoTitle: "Bruteforce Attack SSH/FTP Guide - Part 3"
seoDescription: "Learn to execute brute force attacks on SSH and FTP services using Hydra. Master password cracking techniques and secure network protocols"
datePublished: Wed Apr 10 2024 21:15:32 GMT+0000 (Coordinated Universal Time)
cuid: cluub7mir000508kw4tdee5dt
slug: bruteforce-series-bruteforce-attack-ssh-and-ftp-03
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1712783714463/5415722c-d464-4339-a136-f85a07845e7d.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1712783720732/9761bd6c-e163-4971-92c9-1f506f1560c2.png
tags: passwords, pentesting, bruteforceattack

---

### **SSH Attack Overview**

**Objective:** Understand and execute a brute force attack on an SSH service using Hydra.

### Key Concepts and Skills

* **Brute Force Attack:** An attempt to crack passwords or keys through trial and error.
    
* **Hydra:** A powerful, multi-platform tool designed for password cracking by performing rapid dictionary or brute force attacks.
    

### Step-by-Step Guide

**a) Concept Explanation:**

* **SSH (Secure Shell):** A cryptographic network protocol for operating network services securely over an unsecured network. Commonly used for remote server login and command execution.
    
* **Hydra's Role:** Utilizes username and password lists to systematically attempt logins, identifying valid credentials.
    

**b) Preparing for the Attack:**

1. **Ensure Hydra is Installed:** Confirm that Hydra is available on your system. It can typically be installed via package managers in Linux distributions.
    
    ```bash
    
    sudo apt-get install hydra
    
    ```
    
2. **Gather Wordlists:**
    
    * **Username List (**`bill.txt`): Contains potential usernames.
        
    * **Password List (**`william.txt`): Contains potential passwords.
        
    
    These lists should be prepared based on known information about the target or using common username and password compilations.
    
3. **Identify the Target:** Obtain the IP address and SSH service port (typically 22) of the target system.
    

**c) Executing the Attack:**

```bash

hydra -L bill.txt -P william.txt -u -f ssh://TARGET_IP:22 -t 4

```

* `L bill.txt` specifies the username list.
    
* `P william.txt` specifies the password list.
    
* `u` instructs Hydra to use each username in the list only once.
    
* `f` tells Hydra to stop on the first valid password found for a username.
    
* `ssh://TARGET_IP:22` defines the attack protocol and target.
    
* `t 4` limits the number of parallel attempts to avoid connection drops.
    

**d) Analyzing Results:**

* Successful attempts will be clearly displayed by Hydra, showing valid username-password pairs.
    
* **Login Using SSH:** With valid credentials, you can log into the target system using:
    
    ```bash
    
    ssh USERNAME@TARGET_IP -p 22
    
    ```
    

### **FTP Brute Forcing Overview**

Once access is gained to a system via SSH, further exploration and potential brute force attacks on other services like FTP can be considered.

### Understanding FTP Service Vulnerability

**a) Concept Explanation:**

* **FTP (File Transfer Protocol):** Used for the transfer of files between a client and server on a network.
    
* Vulnerable to brute force attacks similar to SSH, especially when weak passwords are used.
    

**b) Performing the Attack:**

1. **Identify the FTP Service:** Confirm the presence and accessibility of an FTP service on the target system or network.
    
2. **Utilize Hydra for FTP:** Similar to the SSH attack, use Hydra with a targeted wordlist:
    
    ```bash
    
    hydra -l m.gates -P rockyou-10.txt <ftp://127.0.0.1>
    
    ```
    
    Adjust parameters as needed for the specific target and wordlist.
    

**c) Post-Attack Actions:**

* **FTP Login:** With valid credentials, you can login to the FTP service to explore or transfer files.
    
    ```bash
    
    ftp 127.0.0.1
    
    ```
    
* **User Switching:** If system access permits, switching to the compromised user account may provide additional privileges or access.
    
    ```bash
    
    su - USERNAME
    
    ```