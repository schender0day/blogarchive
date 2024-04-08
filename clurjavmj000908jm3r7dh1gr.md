---
title: "Basic HTTP Authentication and Brute-Forcing /w Hydra"
seoTitle: "Hydra Tutorial: HTTP Auth Brute-Force"
seoDescription: "Learn to secure and test HTTP authentication with Hydra's brute-forcing guide, covering setup, attack execution, and result analysis"
datePublished: Mon Apr 08 2024 22:38:43 GMT+0000 (Coordinated Universal Time)
cuid: clurjavmj000908jm3r7dh1gr
slug: basic-http-authentication-and-brute-forcing-w-hydra
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1712615776815/4b5021b4-78e1-432f-bd15-3c1447bcc8fe.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1712615862704/7de271db-552b-4220-b3c2-4f1d5c313f80.png
tags: hacking, passwords, pentesting, bruteforceattack, hacking-tutorial

---

### A. Basic HTTP Authentication Scheme

* **Concept**: It’s a method for an HTTP server to request authentication from a client, using a username and password. The credentials are sent in headers, encoded with Base64.
    
* **Process**:
    
    1. The client sends a request without authentication.
        
    2. The server responds with a `WWW-Authenticate` header, asking for credentials.
        
    3. The client resends the request with an `Authorization` header containing encoded credentials.
        

### **Step-by-Step Guide to Using Hydra for Brute-Forcing**

### Setting Up Hydra

1. **Installation**: On most Linux distributions, Hydra can be installed via package managers (`apt`, `yum`, etc.). It’s also pre-installed on Pwnbox.
    
    * Command: `sudo apt install hydra -y`
        
2. **Understanding Hydra's Syntax and Options**:
    
    * Use `hydra -h` to view all options and flags. Key flags include `l` for specifying a single login, `L` for a list of logins, `p` for a single password, `P` for a list of passwords, and `C` for a file with login:password pairs.
        

### Conducting a Brute Force Attack

1. **Choosing Targets and Credentials**:
    
    * Identify the web service and port you’re targeting.
        
    * Decide whether to use single credentials (`l` and `p`), lists (`L` and `P`), or combined credentials (`C`).
        
2. **Preparation**:
    
    * Gather or create wordlists for usernames and passwords. SecLists is a valuable resource for finding such lists.
        
    * Command Example: `hydra -C /path/to/credentials.txt SERVER_IP -s PORT http-get /`
        
3. **Executing the Attack**:
    
    * Assemble your Hydra command based on your target and chosen method.
        
    * Example: `hydra -C /opt/useful/SecLists/Passwords/Default-Credentials/ftp-betterdefaultpasslist.txt 178.211.23.155 -s 31099 http-get /`
        
    * This command attempts to log in using a list of default credentials against a web server running on a non-standard port.
        

## Visualize the Hydra Attack

> **1\. Preparation Phase**

> **Hydra Initialization**: Hydra starts up and reads the command-line arguments you've provided.

> **Wordlist Loading**: Hydra loads the combined username:password pairs from the file located at `/opt/useful/SecLists/Passwords/Default-Credentials/ftp-betterdefaultpasslist.txt`. Each line in this file likely contains a username and password separated by a colon (`:`), representing a set of credentials to test.

> **2\. Configuration Phase**

> **Target Specification**: You've specified the target's IP address as `94.237.54.170`, directing Hydra where to send the authentication attempts.

> **Port Specification**: With `s 56220`, you're instructing Hydra to direct its attempts to port `56220` on the target machine, suggesting that the web service you're targeting is hosted on a non-standard port.

> **Service Module Selection**: By including `http-get` in the command, you're telling Hydra to use the HTTP GET method for the attack. This means Hydra will craft HTTP GET requests for each username:password pair, attempting to authenticate against a resource on the server.

> **3\. Execution Phase**

> **Request Sending**: For each username:password pair in the wordlist, Hydra sends an HTTP GET request to [`http://94.237.54.170:56220`](http://94.237.54.170:56220). The authentication credentials are encoded (typically in Base64 when Basic Authentication is used) and included in the HTTP `Authorization` header of each request.

> **Response Analysis**: The server responds to each request. If the credentials are incorrect, the server typically responds with a `401 Unauthorized` status code. For correct credentials, the server's response varies but will not include a `401` status.

> **4\. Result Handling**

> **Success Detection**: Hydra checks the response from the server for each request. When it detects a response that implies successful authentication (not a `401`), it considers the credentials that generated this response to be valid.

> **Logging**: Upon finding a valid username:password pair, Hydra logs the successful credentials. Depending on the command-line flags, it might stop the attack or continue testing other pairs.

> **5\. Post-Execution**

> **Report**: After completing the process or finding a valid credential set, Hydra provides a summary of the attack outcomes, including any successfully authenticated credentials.

> **Cleanup**: Hydra concludes its operation, freeing up any resources it used during the attack process.

### **Checkpoint Exercise**

* **Task**: Use Hydra to conduct a brute force attack on a test web server you have permission to test. Start with default credential pairs and then try separate wordlists for usernames and passwords.
    
* **Verification**: Confirm any found credentials by accessing the web service directly.