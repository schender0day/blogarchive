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
        
3. **Analyzing the Output**:
    
    * Successful discoveries are usually indicated by HTTP status codes other than 404, such as 200 or 301, signaling accessible directories.
        

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
        

---

### Recursive Fuzzing

To automate the exploration of directories and their subdirectories, recursive fuzzing is employed.

* **Enabling Recursive Fuzzing**:
    
    * Use the command: `ffuf -w wordlist:FUZZ -u` [`http://SERVER_IP:PORT/FUZZ`](http://SERVER_IP:PORT/FUZZ) `-recursion -recursion-depth 1 -e .php -v`, enabling Ffuf to navigate through directory levels automatically.
        

### **Recursive Fuzzing Visualization**

### **Starting Point:**

* Your `ffuf` command targets the root of the website.
    
* Wordlist contains entries like `admin`, `login`, and `images`.
    

### **Command:**

```powershell
shellCopy code
ffuf -w wordlist.txt:FUZZ -u <http://192.168.1.100:8080/FUZZ> -recursion -recursion-depth 1 -e .php -v

```

### **Fuzzing Process Visualization:**

1. **Initial Fuzzing at Root Level**:
    
    * [`http://192.168.1.100:8080/admin`](http://192.168.1.100:8080/admin)
        
    * [`http://192.168.1.100:8080/login`](http://192.168.1.100:8080/login)
        
    * [`http://192.168.1.100:8080/images`](http://192.168.1.100:8080/images)
        

Suppose `ffuf` finds that the `/admin` and `/images` directories exist because the server returns a `200 OK` status for these URLs.

1. **Recursion - 1 Level Deep**:
    
    * Since both `/admin` and `/images` directories exist, `ffuf` will now go one level deep into these directories using the same wordlist.
        
    * It appends each wordlist entry to these base URLs and checks again.
        

### **URLs Visited During Recursion:**

For `/admin` directory:

* [`http://192.168.1.100:8080/admin/settings.php`](http://192.168.1.100:8080/admin/settings.php)
    
* [`http://192.168.1.100:8080/admin/dashboard.php`](http://192.168.1.100:8080/admin/dashboard.php)
    
* [`http://192.168.1.100:8080/admin/login.php`](http://192.168.1.100:8080/admin/login.php)
    

For `/images` directory:

* [`http://192.168.1.100:8080/images/upload.php`](http://192.168.1.100:8080/images/upload.php)
    
* [`http://192.168.1.100:8080/images/gallery.php`](http://192.168.1.100:8080/images/gallery.php)
    

Assuming our wordlist includes `settings`, `dashboard`, `login`, `upload`, and `gallery` (with `.php` extension due to `-e .php`), `ffuf` will generate these URLs to test for their existence.

### **Visual Representation:**

Here's a simplified directory tree visualization showing how `ffuf` explores the directories:

```bash
bashCopy code
<http://192.168.1.100:8080/>
│
├── /admin/ [200 OK]
│   ├── settings.php [Fuzzed and Found]
│   ├── dashboard.php [Fuzzed and Found]
│   └── login.php [Fuzzed and Found]
│
└── /images/ [200 OK]
    ├── upload.php [Fuzzed and Found]
    └── gallery.php [Fuzzed and Found]

```

* **First Layer (Root)**: `ffuf` starts with the root directory, appending entries from the wordlist to the base URL.
    
* **Second Layer (1 Level Deep)**: Upon finding accessible directories in the first layer (`/admin/`, `/images/`), it proceeds to append wordlist entries to these directories as well, but it stops after this level because of `recursion-depth 1`.
    

This process allows `ffuf` to methodically identify directories and files that are up to one level deep from the root, based on the entries in the provided wordlist.

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