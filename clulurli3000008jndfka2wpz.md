---
title: "Fuzzing series - Sub-domains Fuzzing with Ffuf - 02"
seoDescription: "Learn sub-domain fuzzing with ffuf, a powerful cybersecurity tool, in this guide"
datePublished: Thu Apr 04 2024 23:13:01 GMT+0000 (Coordinated Universal Time)
cuid: clulurli3000008jndfka2wpz
slug: fuzzing-series-sub-domains-fuzzing-with-ffuf-02
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1712272016876/644f4eec-ef5c-4d99-a184-f78efd24a29d.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1712272158360/461de57e-9b2d-4571-8a15-331988cf18eb.png
tags: fluf, ffuf-sub-domain-fuzzing-vhost-fuzzing-cybersecurity-web-penetration-testing-information-gathering-reconnaissance-web-fuzzer-dns-enumeration-seclists-http-headers-fuzzing-web-application-security-domain-discovery-security-assessment-tools-network-security, fffaaa, fffaa

---

#### Introduction

Sub-domain fuzzing is a technique used in cybersecurity to discover existing but unlisted sub-domains of a given website. It plays a crucial role in information gathering and reconnaissance phase of a penetration test, providing insight into the target's infrastructure. This guide will walk you through the process of sub-domain fuzzing using the tool `ffuf`, a fast web fuzzer designed for discovering elements and content within web applications.

#### Understanding Sub-domains

* **Definition**: A sub-domain is a subsection of a main domain, serving as a separate website or a specific service. Example: [`photos.google.com`](http://photos.google.com) is a sub-domain of [`google.com`](http://google.com).
    
* **Purpose**: Sub-domains are used to organize and separate content within a website, often dedicated to different services or applications.
    

#### Prerequisites

1. **Wordlist**: A list of potential sub-domain names, typically common names used across different websites. Example: `subdomains-top1million-5000.txt`.
    
2. **Target Domain**: The website you wish to fuzz for sub-domains. Example: [`inlanefreight.com`](http://inlanefreight.com).
    

#### Step-by-Step Guide to Sub-domain Fuzzing with ffuf

1. **Setup and Installation**
    
    * Ensure `ffuf` is installed on your system. It's available on most Linux distributions and can be easily installed using package managers.
        
2. **Selecting a Wordlist**
    
    * Locate an appropriate wordlist. For our purposes, we use `subdomains-top1million-5000.txt` from the SecLists repository, which is a curated collection of lists used for security assessments.
        
3. **Identifying a Target**
    
    * Choose the domain you want to test. For instructional purposes, we'll use [`inlanefreight.com`](http://inlanefreight.com).
        
4. **Executing ffuf for Sub-domain Fuzzing**
    
    * Use the following command template, replacing the target domain as necessary:
        
        ```powershell
        ffuf -w /path/to/wordlist:FUZZ -u https://FUZZ.targetdomain.com/
        ```
        
    * Example command:
        
        ```powershell
        ffuf -w /opt/useful/SecLists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u https://FUZZ.inlanefreight.com/
        ```
        
5. **Analyzing the Results**
    
    * Hits are indicated by different HTTP status codes, such as `200` (OK), `301` (Moved Permanently). Each successful hit represents a potential sub-domain.
        
6. **Further Steps**
    
    * For each discovered sub-domain, manually verify its accessibility and functionality. This can reveal additional areas for exploration or vulnerabilities.
        

#### Common Errors and Troubleshooting

* **No Hits**: If no sub-domains are found, consider using a more comprehensive wordlist or ensure the domain is correctly spelled and accessible.
    
* **False Positives**: Verify each found sub-domain, as some may not host relevant content.
    

#### Best Practices

* **Respect Legal Boundaries**: Only perform fuzzing on domains you have permission to test.
    
* **Use Comprehensive Wordlists**: Larger, more thorough wordlists increase the chance of discovering hidden sub-domains.
    

#### Checkpoint

* Try running ffuf with a different wordlist or against another domain to practice and solidify your understanding.
    

#### Further Exploration

* Explore additional ffuf options and functionalities to refine your fuzzing techniques.
    
* Study other fuzzing tools and methods for a broader understanding of cybersecurity practices.