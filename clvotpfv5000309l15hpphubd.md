---
title: "Xss 101 -"
seoTitle: "Understanding the Basics of XSS Vulnerabilities"
seoDescription: "Explore the basics of non-persistent XSS, including types, characteristics, and mitigation strategies in web security"
datePublished: Thu May 02 2024 05:46:22 GMT+0000 (Coordinated Universal Time)
cuid: clvotpfv5000309l15hpphubd
slug: xss-101

---

## 1\. Introduction

### 1.1. Types of Non-Persistent XSS

* Reflected XSS: Processed by the back-end server
    
* DOM-based XSS: Completely processed on the client-side, never reaches the back-end server
    

### 1.2. Characteristics of Non-Persistent XSS

* Temporary and not persistent through page refreshes
    
* Affects only the targeted user, not other users visiting the page
    

## 2\. Reflected XSS Vulnerabilities

### 2.1. Definition

Reflected XSS vulnerabilities occur when user input reaches the back-end server and is returned without being filtered or sanitized.

### 2.2. Common Scenarios

Error messages or confirmation messages that include the user's input are potential vectors for Reflected XSS.

## 3\. Hands-on Example: Vulnerable To-Do List App Based on CPTS XSS Module

### 3.1. Testing with a Benign Input

1. Enter a test string (e.g., "test") into the input field.
    
2. Observe how the input is handled in the error message.
    

### 3.2. Testing with an XSS Payload

1. Enter a basic XSS payload (e.g., `<script>alert(window.origin)</script>`) into the input field.
    
2. Click the "Add" button to submit the payload.
    
3. Observe the alert pop-up, indicating successful execution of the XSS payload.
    

### 3.3. Analyzing the Page Source

1. View the page source after submitting the XSS payload.
    
2. Confirm that the error message includes the XSS payload.
    

### 3.4. Verifying Non-Persistence

1. Revisit the Reflected page after submitting the XSS payload.
    
2. Observe that the error message no longer appears, and the XSS payload is not executed.
    

## 4\. Exploiting Reflected XSS Vulnerabilities

### 4.1. Determining the HTTP Request Method

1. Open the browser's Developer Tools (e.g., Firefox Developer Tools).
    
2. Select the "Network" tab.
    
3. Submit a payload and observe the request method (e.g., GET request).
    

### 4.2. Crafting a Malicious URL

1. Copy the URL containing the XSS payload from the address bar or the Network tab.
    
2. Share the malicious URL with the target user.
    

### 4.3. Execution of the XSS Payload

When the target user visits the malicious URL, the XSS payload is executed in their browser.

## 5\. Mitigating Reflected XSS Vulnerabilities

### 5.1. Input Validation and Sanitization

* Implement server-side input validation and sanitization techniques to filter out malicious characters and scripts.
    

### 5.2. Encoding Output

* Properly encode user-supplied data before including it in the server's response to prevent the execution of scripts.
    

### 5.3. Content Security Policy (CSP)

* Implement a strict CSP to restrict the sources of executable scripts and mitigate the impact of XSS attacks.
    

## 6\. Conclusion

### 6.1. Recap of Key Points

* Reflected XSS vulnerabilities occur when user input is returned by the server without proper filtering or sanitization.
    
* Non-Persistent XSS affects only the targeted user and is not persistent through page refreshes.
    
* Attackers can exploit Reflected XSS by crafting malicious URLs containing XSS payloads.
    

### 6.2. Importance of Preventing Reflected XSS

* Reflected XSS vulnerabilities can lead to unauthorized actions, data theft, and compromised user accounts.
    
* Developers must implement proper input validation, output encoding, and security measures to mitigate the risk of Reflected XSS attacks.