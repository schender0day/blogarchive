---
title: "LFI 101 - Introduction of File Inclusion Vulnerabilities"
seoTitle: "Understanding LFI Vulnerabilities"
seoDescription: "Explore the risks and mitigation strategies of file inclusion vulnerabilities affecting multiple programming languages and frameworks"
datePublished: Thu May 09 2024 05:57:48 GMT+0000 (Coordinated Universal Time)
cuid: clvyu746n000j08l38zd8dqb0
slug: lfi-101-introduction-of-file-inclusion-vulnerabilities
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1715234248153/8b19640c-b1f7-46af-827e-fd2ca75d1fd9.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1715234241600/c169cc0b-dade-42c9-9f24-9f44856f4519.png

---

## 1\. Introduction to File Inclusion Vulnerabilities

### 1.1 What are File Inclusion vulnerabilities?

* **Explanation:** File Inclusion vulns allow attacker to manipulate parameters to display contents of local files on server
    
* Can lead to source code disclosure, sensitive data exposure, remote code execution
    

### 1.2 How File Inclusion vulns commonly occur

* Often found in templating engines that dynamically load content based on parameters
    
* **Example:** `/index.php?page=about` loads `about.php` file
    
* If parameter is user-controlled without proper sanitization, attacker can exploit to read arbitrary files
    

## 2\. File Inclusion Vulnerabilities in Different Languages/Frameworks

### 2.1 PHP

* **Vulnerable functions:** `include()`, `include_once()`, `require()`, `require_once()`, `file_get_contents()`
    
* **Example vulnerable code:**
    
    ```php
    if (isset($_GET['language'])) {
        include($_GET['language']);
    }
    
    ```
    

### 2.2 NodeJS

* **Vulnerable functions:** `fs.readFile()`, `res.render()` in Express.js
    
* **Example vulnerable code:**
    
    ```sql
    if(req.query.language) {
        fs.readFile(path.join(__dirname, req.query.language), function (err, data) {
            res.write(data);
        });
    }
    
    ```
    

### 2.3 Java

* **Vulnerable tags/functions:** `<c:if>`/`<jsp:include>`, `<c:import>`
    
* **Example vulnerable code:**
    
    ```sql
    <c:if test="${not empty param.language}">
        <jsp:include file="<%= request.getParameter('language') %>" />
    </c:if>
    
    ```
    

### 2.4 .NET

* **Vulnerable functions:** `Response.WriteFile()`, `@Html.Partial()`, `#include`
    
* **Example vulnerable code:**
    
    ```sql
    @if (!string.IsNullOrEmpty(HttpContext.Request.Query['language'])) {
        <% Response.WriteFile("<% HttpContext.Request.Query['language'] %>"); %>
    }
    
    ```
    

## 3\. Impact of File Inclusion Vulnerabilities

### 3.1 Reading vs Executing Files

* Some vuln functions only read file contents, others can execute code
    
* Ability to execute code can lead to Remote Code Execution
    
* Reading source code can still be severe - may reveal other vulns, sensitive info like keys/creds
    

### 3.2 Local vs Remote File Inclusion

* Some functions restricted to local files, others can load remote URLs
    
* Loading remote URLs increases impact - can pull attacker's malicious code and execute it
    

## 4\. Identifying File Inclusion Vulnerabilities

### 4.1 In Black Box testing

* Look for parameters that seem to control what content/template is loaded
    
* Try manipulating parameter to load different files - `/etc/passwd`, web app source code, config files
    

### 4.2 In White Box testing / Code Review

* Look for dynamic file loading based on user input, without proper sanitization
    
* References to functions in table in section 3.1, with user-controlled parameters
    

## 5\. Mitigating File Inclusion Vulnerabilities

### 5.1 Secure Coding Practices

* Avoid letting user input control paths for dynamic loading wherever possible
    
* Always validate and sanitize any user input before using it
    
* Use allowlists to restrict paths to a set of allowed values
    

### 5.2 Web Application Firewalls and other Protections

* WAFs can block requests containing suspicious file paths
    
* Disable unneded functions like remote URL fetching
    
* Ensure server configured to prevent directory traversal
    

## 6\. Summary

* File inclusion is a severe vuln that can lead to file disclosure or RCE
    
* Can affect web apps in many languages, appears in functions that dynamically load files
    
* Proper input validation and secure coding practices are key to prevention
    
* Identifying instances of dynamic file loading and testing parameters is crucial
    

---

### Additional Resources:

* [OWASP guide to testing for LFI](https://owasp.org/www-project-web-security-testing-guide/stable/4-Web_Application_Security_Testing/07-Input_Validation_Testing/11.1-Testing_for_Local_File_Inclusion)
    
* [Configuring PHP securely](https://www.php.net/manual/en/security.php)