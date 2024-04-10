---
title: "Brute-Force Series - Using Hydra to Brute Force Login Forms - 02"
seoDescription: "Master brute-forcing login forms with Hydra on `http://www.inlanefreight.htb` to find admin credentials efficiently, reducing network traffic"
datePublished: Tue Apr 09 2024 22:19:42 GMT+0000 (Coordinated Universal Time)
cuid: clusy2aay000608lccxn4acny
slug: brute-force-series-using-hydra-to-brute-force-login-forms-02
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1712701123027/1e4c845a-3a37-4318-ba76-bf6af8adaa6d.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1712701155619/da98f48a-8db3-46ce-8178-5deb114c29b5.png
tags: pentesting, bruteforceattack, redteaming

---

## **1\. Understand the Target**

* **a.** The example target is a login form for administrators on the website [`http://www.inlanefreight.htb`](http://www.inlanefreight.htb)
    
* **b.** Gaining access to the admin panel could allow executing OS commands on the server
    
* **c.** Want to find valid credentials while generating minimal network traffic
    

## **2\. Analyze the Login Form**

* **a.** The login form is at the URL [`http://www.inlanefreight.htb/login.php`](http://www.inlanefreight.htb/login.php)
    
* **b.** It uses a POST request to submit the form, evident by no input being included in the URL after a login attempt
    
* **c.** The page uses HTTP, not HTTPS
    

## **3\. Select the Appropriate Hydra Module**

* **a.** Use the `http-post-form` module since it's a login form using a POST request over HTTP
    
* **b.** Syntax: `hydra http-post-form -U` to see usage
    

## **4\. Determine the Hydra Command Syntax**

Hydra requires 3 colon-separated parameters for `http-post-form`:

* **a.** URL path containing the form
    
* **b.** POST parameters for username & password
    
* **c.** A string to distinguish failed vs successful logins
    
    * **Parameter 1:** URL path: `/login.php`
        
    * **Parameter 2:** POST params: `[user param]=^USER^&[password param]=^PASS^`
        
    * **Parameter 3:** Failure/success string:
        
        * Use `F=` to specify string that indicates a failed login
            
        * Hard to determine success string without valid creds
            
        * Instead, find a distinct string on the login page unlikely to exist after login
            

## **5\. Find a String to Use for the Failure Condition**

* **a.** View page source and look for login-related strings
    
* **b.** Select part of the HTML form code: `<form name='login'`
    
* **c.** If this string is found, it means still on the login page (failed)
    
* **d.** If not found, likely means login succeeded and on a different page
    

## **6\. Construct the Final Hydra Command**

```bash

hydra http-post-form "/login.php:USER_PARAM=^USER^&PASS_PARAM=^PASS^:F=<form name='login'"
```

* Replace `USER_PARAM` and `PASS_PARAM` with the actual parameter names used in the HTML form
    

## **7\. Run Hydra with a Small Username/Password List First**

* **a.** Start with just a few common credential pairs like `admin:admin`
    
* **b.** Avoid generating excessive traffic initially
    
* **c.** If no success, can try a larger wordlist
    

## **8\. Complete break down of the hydra commands**

### **Command Breakdown:**

```bash

hydra -l admin -P passwordlist.txt [target-IP] http-post-form "/login:username=^USER^&password=^PASS^:F=Invalid Login Details"
```

1. **Hydra Invocation**:
    
    * `hydra`: This is the command to start Hydra, a powerful brute-forcing tool used for various types of services.
        
2. **Login Flag**:
    
    * `l admin`: This flag specifies the login name to use for the brute force attempt. In this case, you're attempting to log in as the user "admin".
        
3. **Password List Flag**:
    
    * `P passwordlist.txt`: This tells Hydra to use a list of passwords from the file `passwordlist.txt` for the brute force attempt. Hydra will try each password from this file in conjunction with the specified username.
        
4. **Target Specification**:
    
    * `[target-IP]`: This should be replaced with the actual IP address of the target system you are attempting to brute force. It directs Hydra where to send the login attempts.
        
5. **Service Specification**:
    
    * `http-post-form`: This specifies the type of service Hydra will attack, indicating you're targeting a form that submits data using the HTTP POST method.
        
6. **Form Path and Data**:
    
    * `"/login:username=^USER^&password=^PASS^:F=Invalid Login Details"`: This part is split into several components:
        
        * `/login`: The path to the login form on the server.
            
        * `username=^USER^`: The form field where the username is entered. `^USER^` is a placeholder that Hydra replaces with the username specified by `l` or `L` (for a list).
            
        * `&password=^PASS^`: The form field for the password. `^PASS^` is a placeholder that Hydra replaces with each password from the `passwordlist.txt`.
            
        * `:F=Invalid Login Details`: This is a condition for Hydra to identify a failed login attempt. It tells Hydra that if the response page contains the phrase "Invalid Login Details," then the login attempt with that specific password has failed.
            

### **Step-by-Step Process:**

1. **Start**: Hydra begins its operation using the parameters provided in the command.
    
2. **Username**: Hydra sets the username to "admin" for all attempts.
    
3. **Password Loop**: Hydra reads the `passwordlist.txt` file and begins looping through each password.
    
4. **Attempt Login**:
    
    * For each password, Hydra constructs an HTTP POST request to the path `/login` on the target IP.
        
    * In the request, it replaces `^USER^` with "admin" and `^PASS^` with the current password from the list.
        
5. **Check Response**:
    
    * After each login attempt, Hydra checks the server's response for the string "Invalid Login Details".
        
    * If the string is found, Hydra understands the login attempt failed and moves on to the next password in the list.
        
    * If the string is not found, it implies the login might have succeeded. Hydra then either stops (if `f` is used) or continues (depending on command options) with the next password or reports the successful credential pair.
        
6. **Repeat or End**:
    
    * This process repeats for each password in the list until either a successful login is detected, the list is exhausted, or Hydra is manually stopped.
        
7. **Report**:
    
    * Upon completion or discovery of a valid login, Hydra reports the outcome. If successful, it presents the username and password that succeeded.
        

> Note: Remember to exercise caution to avoid locking out accounts or generating suspicious traffic. A slow and steady approach is often the best when brute-forcing logins.

### **Pratical Login Form Attacks**

#### Section 1: Introduction to Login Form Attacks

**Concept Explanation:**

* **Login form attacks** are attempts to gain unauthorized access to a web application by exploiting its login form. These attacks can vary in technique, with some common methods including using default credentials or brute force attacks.
    

**Technical Terms:**

* **Default Credentials**: Predefined sets of usernames and passwords provided by the manufacturer or developer. Often, these credentials are generic, such as admin/admin, and are intended to be changed by the user.
    
* **Brute Force Attack**: A trial-and-error method used to decode encrypted data such as passwords or Data Encryption Standard (DES) keys through exhaustive effort rather than employing intellectual strategies.
    

**Real-world Example:**

* An attacker tries to log into a web application's admin panel using commonly known default usernames and passwords. If the credentials are not changed from the default, the attacker gains access.
    

#### Section 2: Preparing for the Attack

**Prerequisites and Setup:**

1. Enumerate all available ports and services on the target.
    
2. Ensure you have the necessary tools installed, such as Hydra.
    
3. Prepare or obtain a list of default credentials and a comprehensive password list like `rockyou.txt`.
    

**Step-by-Step Instructions:**

1. **Tool Installation:** Verify that Hydra is installed on your system. If not, download and install it from the official repository or use package management tools like `apt` or `brew`.
    
2. **Enumeration:** Use tools like Nmap to scan the target's ports to find open web application ports, typically 80 (HTTP) or 443 (HTTPS).
    
3. **Credentials Preparation:** Gather default credentials and password lists. For default credentials, you can use lists available in SecLists under `Passwords/Default-Credentials/`.
    

#### Section 3: Executing the Attack

##### **Using Default Credentials**

**Step-by-Step Instructions:**

1. Open your terminal and navigate to the directory containing Hydra.
    
2. Execute the Hydra command to try the default credentials. Use the syntax provided in the input, replacing the IP address and port number with those of your target.
    

**Code Snippet:**

```powershell
bashCopy codehydra -C /path/to/default/credentials.txt <target-ip> -s <port> http-post-form "/login.php:username=^USER^&password=^PASS^:F=<form name='login'"
```

**Common Errors or Pitfalls:**

* Not updating Hydra to the latest version, leading to compatibility issues.
    
* Incorrect syntax in the Hydra command.
    
* Using outdated or irrelevant default credentials lists.
    

##### **Brute Forcing with a Specific Username**

**Step-by-Step Instructions:**

1. Determine a likely username for the admin panel, such as "admin."
    
2. Use the `rockyou.txt` password list for the attack. Ensure you have this file downloaded and know its path.
    
3. Execute the Hydra command with the specified username and password list.
    

**Code Snippet:**

```powershell
bashCopy codehydra -l admin -P /path/to/rockyou.txt <target-ip> -s <port> http-post-form "/login.php:username=^USER^&password=^PASS^:F=<form name='login'"
```

**Troubleshooting:**

* If the attack seems to run indefinitely, check the network connection and the responsiveness of the target application.
    
* Ensure the syntax of the Hydra command is correct, especially the path to the password list.
    

#### Checkpoint:

* Try executing a default credentials attack against a safe, legal practice environment.
    
* Reflect on the process: Were there any errors? How could the efficiency of the attack be improved?
    

### **Conclusion and Further Reading**

**Summary:** This guide covered the essentials of launching login form attacks, from preparation to execution. The use of default credentials and brute force attacks with Hydra were highlighted as key techniques.