---
title: "XSS 101 - Stored XSS"
seoTitle: "Understanding Stored XSS: A Beginner's Guide"
seoDescription: "Explore the critical nature of Stored XSS vulnerabilities, their impact on web users, and best practices for prevention in modern web applications"
datePublished: Wed May 01 2024 06:11:18 GMT+0000 (Coordinated Universal Time)
cuid: clvnf5ndw001508l1efl6amkf
slug: xss-101-stored-xss

---

## 1\. Introduction

### 1.1. Definition of Stored XSS

Stored XSS, also known as Persistent XSS, occurs when an attacker's malicious payload is stored on the target server and executed whenever a user visits the affected web page.

### 1.2. Criticality of Stored XSS compared to other XSS types

Stored XSS is considered the most critical type of XSS vulnerability because it affects a wider audience, as any user visiting the compromised page will fall victim to the attack.

### 1.3. Impact on users visiting affected pages

Users visiting pages with Stored XSS vulnerabilities may inadvertently execute the attacker's malicious script, potentially leading to session hijacking, data theft, or unauthorized actions on their behalf.

## 2\. Example of a Vulnerable Web Application

### 2.1. Setting up a simple "To-Do List" app

Consider a basic web application that allows users to create and manage a list of tasks without proper input validation or sanitization.

### 2.2. Inputting data and observing page behavior

When a user enters a task and submits the form, the application displays the input on the page without any filtering or validation.

### 2.3. Potential for XSS if input is not sanitized or filtered

If the application does not sanitize or filter user input, an attacker can inject malicious scripts that will be stored and executed whenever the page is loaded.

## 3\. Testing for Stored XSS

### 3.1. Basic XSS testing payload: `<script>alert(window.origin)</script>`

* This payload attempts to execute an alert function, displaying the current page's origin.
    
* If the page is vulnerable, the alert will be triggered, confirming the XSS vulnerability.
    

### 3.2. Confirming XSS vulnerability

* Observe the alert popup displaying the page URL, indicating successful script execution.
    
* Check the page source to verify the presence of the injected payload.
    

### 3.3. Alternative XSS payloads for testing

* `<plaintext>` payload: Stops HTML rendering and displays the remaining content as plain text.
    
* `<script>print()</script>` payload: Triggers the print dialog, which is less likely to be blocked by browsers.
    
* These payloads can be useful when the `alert()` function is blocked by certain browsers.
    

## 4\. Persistence of Stored XSS Payloads

### 4.1. Refreshing the page after payload injection

After injecting the XSS payload, refresh the page to observe its persistent behavior.

### 4.2. Observing persistent behavior of the alert

If the XSS vulnerability is indeed stored, the alert will continue to appear even after page refreshes.

### 4.3. Implications of persistent XSS for all page visitors

Any user visiting the affected page will trigger the XSS payload, demonstrating the widespread impact of Stored XSS vulnerabilities.

## 5\. Modern Web Application Considerations

### 5.1. Usage of cross-domain IFrames for handling user input

Some modern web applications use cross-domain IFrames to handle user input, potentially isolating XSS vulnerabilities from the main application.

### 5.2. Importance of `window.origin` in alert payload

* Using `window.origin` in the alert payload helps identify the vulnerable form.
    
* It differentiates between XSS vulnerabilities in the main application and those in cross-domain IFrames.
    

## 6\. Best Practices for Preventing Stored XSS

### 6.1. Input validation and sanitization techniques

* Escape user input by converting special characters to their HTML entity equivalents.
    
* Whitelist allowed characters and remove or encode any characters not on the whitelist.
    
* Use secure coding libraries designed to handle user input safely.
    

### 6.2. Secure coding practices

* Implement a Content Security Policy (CSP) to restrict the execution of unauthorized scripts.
    
* Set the HTTPOnly flag on sensitive cookies to prevent client-side access.
    
* Limit the length of user input to mitigate the impact of potential XSS payloads.
    

### 6.3. Regular security testing and code reviews

Conduct regular security audits, penetration testing, and code reviews to identify and address XSS vulnerabilities promptly.

## 7\. Conclusion

### 7.1. Recap of key points about Stored XSS

Stored XSS vulnerabilities are critical because they persist on the server and affect all users visiting the compromised page.

### 7.2. Importance of understanding and mitigating Stored XSS risks

Developers must understand the risks associated with Stored XSS and implement appropriate prevention techniques to safeguard user data and maintain application security.

### 7.3. Encouragement to continue learning about web application security

Continuous learning and staying updated with the latest web security best practices is crucial for developers to effectively combat evolving XSS attacks and other security threats.