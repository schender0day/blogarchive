---
title: "LFI 101 - Basic Vulnerabilities"
seoTitle: "LFI Vulnerabilities: A Beginner's Guide"
seoDescription: "Explore the basics of Local File Inclusion (LFI) vulnerabilities, their occurrence, exploitation techniques, and effective prevention strategies"
datePublished: Mon May 13 2024 06:02:41 GMT+0000 (Coordinated Universal Time)
cuid: clw4k4smp000y08laar8i7tnf
slug: lfi-101-basic-vulnerabilities
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1715580126587/6d93c5ea-a5cb-477b-9db5-1734cb0a2039.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1715580144304/b3aecad0-0965-40cc-9eae-e90fc90dc76d.png

---

## 1\. Introduction

Local File Inclusion (LFI) vulnerabilities are a common issue in web applications that dynamically load content based on user-supplied input. They allow an attacker to manipulate parameters to read the contents of local files on the server, potentially leading to sensitive data exposure, source code disclosure, and in some cases, remote code execution.

## 2\. How LFI Vulnerabilities Occur

LFI vulnerabilities are often found in templating engines that load content dynamically. For example:

```php
http://<SERVER_IP>:<PORT>/index.php?language=es.php

```

In this case, the `language` parameter is used to specify which file should be included in the page. If the application does not properly validate and sanitize this input, an attacker can manipulate it to read arbitrary files.

## 3\. Exploiting Basic LFI

To exploit a basic LFI vulnerability, an attacker can simply modify the `language` parameter to point to a sensitive file, such as `/etc/passwd` on Linux or `C:\\Windows\\boot.ini` on Windows:

```sql
http://<SERVER_IP>:<PORT>/index.php?language=/etc/passwd

```

If the application is vulnerable, this will display the contents of the specified file.

## 4\. Path Traversal Techniques

In some cases, the application may append or prepend a string to the user input before using it in the `include()` function. For example:

```php
include("./languages/" . $_GET['language']);

```

To bypass this, an attacker can use relative path traversal techniques by adding `../` to move up directories:

```sql
http://<SERVER_IP>:<PORT>/index.php?language=../../../../etc/passwd

```

This effectively cancels out the prepended directory and allows the attacker to reach the root directory and specify an absolute path.

### Let's break down the concept of "Filename Prefix" in more detail, step by step.

1. Understanding the Vulnerability:
    
    * In this scenario, the web application is using the `include()` function to dynamically load files based on user input.
        
    * The user input is provided through the `language` parameter in the URL.
        
    * However, instead of directly using the user input as the filename, the application prepends a prefix to it.
        
2. Example Vulnerable Code:
    
    * Let's consider the following PHP code snippet:
        
        ```php
        include("lang_" . $_GET['language']);
        
        ```
        
    * In this code, the application takes the value of the `language` parameter from the URL and prepends the string `"lang_"` to it.
        
    * The resulting string is then passed to the `include()` function to load the corresponding file.
        
3. Attacker's Attempt to Exploit:
    
    * An attacker, aware of the LFI vulnerability, tries to manipulate the `language` parameter to read sensitive files.
        
    * The attacker attempts to use directory traversal techniques by providing a value like `../../../etc/passwd` to the `language` parameter.
        
    * However, due to the prefix being appended, the final string becomes `lang_../../../etc/passwd`, which is an invalid file path.
        
4. Bypassing the Filename Prefix:
    
    * To bypass the filename prefix and successfully exploit the vulnerability, the attacker can modify their input.
        
    * Instead of directly using the directory traversal payload, the attacker can prefix their input with a forward slash (`/`).
        
    * For example, the attacker can provide the following input to the `language` parameter:
        
        ```sql
        /../../../etc/passwd
        
        ```
        
    * By adding the forward slash before the payload, the application will treat the prefix `"lang_"` as a directory name.
        
    * The resulting string becomes `lang_/../../../etc/passwd`, which effectively cancels out the `lang_` prefix and allows the attacker to traverse directories and reach the intended file.
        
5. Limitations and Considerations:
    
    * It's important to note that this technique may not always work, depending on the server's configuration and the existence of the `lang_/` directory.
        
    * If the `lang_/` directory doesn't exist, the relative path used by the attacker may not be correct, and the exploitation attempt will fail.
        
    * Additionally, any prefix appended to the user input may interfere with other file inclusion techniques that will be discussed later, such as using PHP wrappers, filters, or Remote File Inclusion (RFI).
        
6. Preventing Filename Prefix Vulnerabilities:
    
    * To prevent vulnerabilities related to filename prefixes, it's crucial to properly validate and sanitize user input.
        
    * Instead of directly concatenating user input with file paths, use a whitelist approach to map user input to predefined file names or paths.
        
    * Implement strict input validation to allow only expected and safe characters in the user input.
        
    * Avoid using user-supplied input directly in file inclusion functions without proper validation and sanitization.
        

## 5\. Bypassing Filename Prefix and Extension Restrictions

If the application prepends a filename prefix, like `lang_`, to the user input:

```php
include("lang_" . $_GET['language']);

```

An attacker can bypass this by adding a `/` before the payload:

```sql
Copy codehttp://<SERVER_IP>:<PORT>/index.php?language=/../../../etc/passwd

```

Similarly, if the application appends a file extension, like `.php`, to the user input:

```php
include($_GET['language'] . ".php");

```

There are several techniques to bypass this, which will be discussed in upcoming sections.

## 6\. Second-Order LFI Attacks

Second-order LFI attacks occur when the application unsafely pulls files based on user-controlled parameters stored in a database or other backend storage.

For example, if a user's avatar is loaded based on their username:

```sql
/profile/$username/avatar.png

```

An attacker could set their username to a malicious LFI payload during registration:

```sql
username: ../../../etc/passwd

```

Then, when the application tries to load the avatar, it will instead read the specified file.

## 7\. Mitigation and Prevention

To prevent LFI vulnerabilities:

* Properly validate and sanitize all user input before using it in file operations
    
* Use allowlists to restrict file access to a predefined set of safe values
    
* Avoid using user input directly in file paths; instead, map the input to safe values on the server-side
    
* Implement strict file and directory permissions to limit what can be read by the web server
    

## 8\. Conclusion

LFI vulnerabilities can have severe consequences if exploited, potentially allowing attackers to read sensitive files, disclose source code, and in some cases, achieve remote code execution. Understanding how these vulnerabilities occur and the various techniques attackers use to exploit them is crucial for developers and security professionals to effectively mitigate and prevent these issues in web applications.