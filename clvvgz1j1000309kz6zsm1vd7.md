---
title: "XSS 101 - Prevention Techniques"
seoDescription: "Learn essential XSS prevention techniques using JavaScript and PHP, including input validation, sanitization, and safe output encoding"
datePublished: Mon May 06 2024 21:24:18 GMT+0000 (Coordinated Universal Time)
cuid: clvvgz1j1000309kz6zsm1vd7
slug: xss-101-prevention-techniques

---

## Frontend Prevention Techniques

### Input Validation with JavaScript

Example:

```jsx
function validateEmail(email) {
  const re = /^(([^<>()[\\]\\\\.,;:\\s@"]+(\\.[^<>()[\\]\\\\.,;:\\s@"]+)*)|(".+"))@((\\[[0-9]{1,3}\\.[0-9]{1,3}\\.[0-9]{1,3}\\.[0-9]{1,3}\\])|(([a-zA-Z\\-0-9]+\\.)+[a-zA-Z]{2,}))$/;
  return re.test(String(email).toLowerCase());
}

```

How it works:

* The `validateEmail` function uses a regular expression to check if the provided email matches the expected format.
    
* It converts the email to lowercase and then uses the `test()` method to test the email against the regex pattern.
    
* If the email matches the pattern, it returns `true`, indicating a valid email format. Otherwise, it returns `false`.
    

Potential issues:

* While this regex pattern covers most common email formats, it may not catch all valid email variations.
    
* It doesn't check if the email actually exists or is deliverable.
    

### Input Sanitization with JavaScript

Example:

```jsx
import DOMPurify from 'dompurify';
const sanitizedInput = DOMPurify.sanitize(userInput);

```

How it works:

* The `DOMPurify` library is used to sanitize the user input.
    
* It removes any potentially malicious HTML or JavaScript code from the input.
    
* The sanitized input is then safe to be inserted into the DOM without risk of XSS.
    

Potential issues:

* DOMPurify is a reliable library, but it's important to keep it up to date to ensure protection against the latest vulnerabilities.
    

### Avoiding user input in HTML contexts

Example (Unsafe):

```jsx
const userName = "John<script>alert('XSS')</script>";
document.getElementById('user').innerHTML = `Welcome, ${userName}!`;

```

How it doesn't work:

* The unsafe example directly inserts the user input (`userName`) into the HTML using `innerHTML`.
    
* If the user input contains any malicious script tags or JavaScript code, it will be executed when inserted into the DOM.
    
* This allows an attacker to inject and execute arbitrary JavaScript code, leading to XSS vulnerabilities.
    

How to fix:

* Instead of using `innerHTML`, use safe methods like `textContent` or `innerText` to insert user input as plain text.
    
* Alternatively, properly escape or sanitize the user input before inserting it into the HTML.
    

## Backend Prevention Techniques

### Input Validation

Example (PHP):

```php
if (filter_var($_POST['email'], FILTER_VALIDATE_EMAIL)) {
// Process valid email
} else {
// Reject invalid email
}

```

How it works:

* The `filter_var` function in PHP is used to validate the email input.
    
* It checks if the provided email matches the `FILTER_VALIDATE_EMAIL` filter, which uses a built-in regex pattern for email validation.
    
* If the email is valid, it proceeds with processing. Otherwise, it rejects the invalid email.
    

Potential issues:

* Similar to the frontend example, this built-in email validation may not cover all possible valid email formats.
    
* It's important to have additional server-side validation and sanitization to ensure the email is safe to use.
    

### Input Sanitization

Example (PHP):

```php
$sanitizedInput = addslashes($_POST['username']);

```

How it works:

* The `addslashes` function in PHP is used to sanitize the user input.
    
* It adds backslashes before certain characters (like single quotes, double quotes, and backslashes) to escape them.
    
* This helps prevent SQL injection attacks when the input is used in SQL queries.
    

Potential issues:

* While `addslashes` can help prevent SQL injection, it's not a complete solution.
    
* It's recommended to use prepared statements or parameterized queries for more robust protection against SQL injection.
    

### Output HTML Encoding

Example (PHP):

```php
echo "Welcome, " . htmlspecialchars($username) . "!";

```

How it works:

* The `htmlspecialchars` function in PHP is used to encode special characters in the user input.
    
* It converts characters like `<`, `>`, `&`, `"`, and `'` to their corresponding HTML entities (`&lt;`, `&gt;`, `&amp;`, `&quot;`, `&#039;`).
    
* This prevents the browser from interpreting these characters as HTML tags or entities, mitigating XSS risks.
    

Potential issues:

* `htmlspecialchars` is effective for encoding output, but it's important to apply it consistently whenever user input is displayed.
    
* It's also crucial to set the appropriate character encoding (e.g., UTF-8) to ensure proper encoding of special characters.