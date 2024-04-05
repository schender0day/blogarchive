---
title: "Fuzzing series - Directory Fuzzing with Ffuf - 01"
datePublished: Thu Apr 04 2024 00:57:15 GMT+0000 (Coordinated Universal Time)
cuid: clukj1rwh000208l4b0fpe19h
slug: fuzzing-series-directory-fuzzing-with-ffuf-01
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1712273306372/f15610e0-47a8-44d1-b3e1-0dddb112b7bc.png
tags: fff

---

---

### Objective

This guide aims to equip you with the knowledge and skills to proficiently use Ffuf for discovering hidden directories, files, and functionalities within web applications. Through a step-by-step approach, you'll learn both directory and page fuzzing techniques, ensuring a thorough understanding of web fuzzing practices.

---

### Introduction to Ffuf and Web Fuzzing

**Ffuf (Fuzz Faster U Fool)** stands as a pivotal tool in web security, designed to unveil hidden web resources by employing wordlists and customized fuzzing strategies. Web fuzzing itself involves sending numerous inputs to a web application to discover hidden content, functionalities, or security vulnerabilities, akin to trying every key on a keyring to unlock a door.

---

### Getting Started with Ffuf

* **Installation**:
    
    * **PwnBox Users**: Ffuf comes pre-installed.
        
    * **Others**: Install via `apt install ffuf -y` on Debian-based systems or directly from GitHub.
        
* **Initial Exploration**:
    
    * Familiarize yourself with Ffuf's options by executing `ffuf -h` in your terminal.
        

---

### Directory Fuzzing with Ffuf

1. **Choosing a Wordlist**:
    
    * Select a comprehensive wordlist, such as `/opt/useful/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt`, which Ffuf will iterate over to guess directory names.
        
2. **Crafting Your Command**:
    
    * Use the command format: `ffuf -w /path/to/wordlist:FUZZ -u` [`http://SERVER_IP:PORT/FUZZ`](http://SERVER_IP:PORT/FUZZ). Replace `/path/to/wordlist` with your wordlist path and [`http://SERVER_IP:PORT`](http://SERVER_IP:PORT) with your target URL.
        
    * When using `ffuf`, the `FUZZ` keyword plays a central role. It serves as a marker in the command syntax, indicating where the tool should inject the payloads from the specified wordlist into the request being sent to the target server.
        
3. **Analyzing the Output**:
    
    * Successful discoveries are usually indicated by HTTP status codes other than 404, such as 200 or 301, signaling accessible directories.
        
    * ```powershell
        s-academy-1]─[10.10.14.246]─[htb-ac-79442@htb-y8pfeup9ds]─[~]
        └──╼ [★]$ ffuf -w ./SecLists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://94.237.62.149:53122/FUZZ 
                /'___\  /'___\           /'___\       
               /\ \__/ /\ \__/  __  __  /\ \__/       
               \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
                \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
                 \ \_\   \ \_\  \ \____/  \ \_\       
                  \/_/    \/_/   \/___/    \/_/       
        
               v2.1.0
        ________________________________________________
        
         :: Method           : GET
         :: URL              : http://94.237.62.149:53122/FUZZ
         :: Wordlist         : FUZZ: /home/htb-ac-79442/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt
         :: Follow redirects : false
         :: Calibration      : false
         :: Timeout          : 10
         :: Threads          : 40
         :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
        ________________________________________________
        
        #                       [Status: 200, Size: 986, Words: 423, Lines: 56, Duration: 134ms]
        #                       [Status: 200, Size: 986, Words: 423, Lines: 56, Duration: 132ms]
        # on at least 3 different hosts [Status: 200, Size: 986, Words: 423, Lines: 56, Duration: 132ms]
        blog                    [Status: 301, Size: 322, Words: 20, Lines: 10, Duration: 132ms]
        #                       [Status: 200, Size: 986, Words: 423, Lines: 56, Duration: 133ms]
                                [Status: 200, Size: 986, Words: 423, Lines: 56, Duration: 133ms]
        forum                   [Status: 301, Size: 323, Words: 20, Lines: 10, Duration: 132ms]
        ```
        

---

### Page and Extension Fuzzing

After discovering a directory like `/blog`, your next step is to uncover any hidden pages within it.

1. **Identifying Page Extensions**:
    
    * Determine the server's file types (.php, .html, .aspx, etc.) by examining the server or employing a wordlist of common extensions for fuzzing.
        
    
2. **Executing Page Fuzzing**:
    
    * Commands for fuzzing might look like:
        
        ```bash
        
        ffuf -w /path/to/extension-wordlist:FUZZ -u http://SERVER_IP:PORT/blog/indexFUZZ
        
        ```
        
    * Replace `/path/to/extension-wordlist` with your extensions wordlist path.
        
    * ```powershell
        [10.10.14.246]─[htb-ac-79442@htb-y8pfeup9ds]─[~]
        └──╼ [★]$ ffuf -w ./SecLists/Discovery/Web-Content/web-extensions.txt:FUZZ -u http://94.237.62.149:53122/blog/indexFUZZ 
                /'___\  /'___\           /'___\       
               /\ \__/ /\ \__/  __  __  /\ \__/       
               \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
                \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
                 \ \_\   \ \_\  \ \____/  \ \_\       
                  \/_/    \/_/   \/___/    \/_/       
        
               v2.1.0
        ________________________________________________
        
         :: Method           : GET
         :: URL              : http://94.237.62.149:53122/blog/indexFUZZ
         :: Wordlist         : FUZZ: /home/htb-ac-79442/SecLists/Discovery/Web-Content/web-extensions.txt
         :: Follow redirects : false
         :: Calibration      : false
         :: Timeout          : 10
         :: Threads          : 40
         :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
        ________________________________________________
        
        .phps                   [Status: 403, Size: 281, Words: 20, Lines: 10, Duration: 3960ms]
        .php                    [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 4968ms]
        :: Progress: [41/41] :: Job [1/1] :: 8 req/sec :: Duration: [0:00:05] :: Errors: 0 ::
        ```
        
        Fuzzing the page:
        
    * ```powershell
        ]$ ffuf -w ./SecLists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://94.237.62.149:53122/blog/FUZZ.php
        
                /'___\  /'___\           /'___\       
               /\ \__/ /\ \__/  __  __  /\ \__/       
               \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
                \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
                 \ \_\   \ \_\  \ \____/  \ \_\       
                  \/_/    \/_/   \/___/    \/_/       
        
               v2.1.0
        ________________________________________________
        
         :: Method           : GET
         :: URL              : http://94.237.62.149:53122/blog/FUZZ.php
         :: Wordlist         : FUZZ: /home/htb-ac-79442/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt
         :: Follow redirects : false
         :: Calibration      : false
         :: Timeout          : 10
         :: Threads          : 40
         :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
        ________________________________________________
        
        index                   [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 134ms]
                             [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 3179ms]
                                [Status: 403, Size: 281, Words: 20, Lines: 10, Duration: 3177ms]
        home                    [Status: 200, Size: 1046, Words: 438, Lines: 58, Duration: 4185ms]
                                [Status: 403, Size: 281, Words: 20, Lines: 10, Duration: 133ms]
        [WARN] Caught keyboard interrupt (Ctrl-C)
        ```
        
    * Found flag:
        
    * ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1712181641591/ae708462-159c-4e5b-a651-f7c4b2fca45b.png align="center")
        

---

### Recursive Fuzzing

To automate the exploration of directories and their subdirectories, recursive fuzzing is employed.

* **Enabling Recursive Fuzzing**:
    
    * Use the command: `ffuf -w wordlist:FUZZ -u` [`http://SERVER_IP:PORT/FUZZ`](http://SERVER_IP:PORT/FUZZ) `-recursion -recursion-depth 1 -e .php -v`, enabling Ffuf to navigate through directory levels automatically.
        
        ```powershell
        └──╼ [★]$ ffuf -w ./SecLists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://94.237.62.149:53122/blog/FUZZ -recursion --recursion-depth 1 -e .php -v
        
        ```
        
* Above command will end up finding below flag.
    

---

### Practical Exercises and Checkpoints

* **Directory Fuzzing Exercise**: Try discovering hidden directories on a test site of your choosing.
    
* **Page Fuzzing Exercise**: Aim to uncover hidden pages within a previously found directory.
    
* **Recursive Fuzzing Challenge**: Apply recursive fuzzing to discover nested directories or files.
    

---

### Advanced Tips and Best Practices

* **Ethical Testing**: Ensure you have authorization before testing targets.
    
* **Wordlist Optimization**: Tailor your wordlist based on the specific target for more efficient fuzzing.
    
* **Request Rate Management**: Adjust the number of threads and request rates judiciously to avoid overloading the target server.
    

---

### Further Learning

* Explore advanced features of Ffuf, such as POST data fuzzing and custom headers.
    
* Delve into broader web security testing methodologies and tools beyond Ffuf.
    

### Suggested Resources

* **SecLists**: A valuable collection of wordlists for various security assessments.
    
* **OWASP Testing Guide**: Offers comprehensive guidance on web application security testing.
    

---

### Conclusion

Mastering Ffuf for web fuzzing empowers cybersecurity professionals to uncover hidden web resources effectively. This guide has laid the foundation for proficient fuzzing practices, from understanding the basics of Ffuf to applying advanced techniques. Continue exploring, learning, and ethically testing to enhance your web security testing capabilities.