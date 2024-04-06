---
title: "Fuzzing series - Vhost Fuzzing and filtering with Ffuf - 03"
seoTitle: "Mastering Vhost Fuzzing: Identifying Hidden Sub-domains & Virtual Host"
seoDescription: "Discover hidden sub-domains and VHosts with our Vhost Fuzzing guide using Ffuf. Uncover both public and non-public domains on the same server"
datePublished: Sat Apr 06 2024 18:42:45 GMT+0000 (Coordinated Universal Time)
cuid: cluofzq8s000108ld1aoz0yw7
slug: fuzzing-series-vhost-fuzzing-and-filtering-with-ffuf-03
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1712428879723/a3b5fa05-2128-439a-b887-376ed6ce3bcd.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1712428924258/603e0026-2f9c-4e8e-b251-dc25f9374c5b.png
tags: hacking, pentesting, cybersecurity-1, pentesting-tools, fluf

---

Vhost fuzzing is a crucial technique for identifying both public and non-public sub-domains and virtual hosts (VHosts) that are served from the same server but do not have public DNS records. This document is structured to ensure clarity and thoroughness, guiding you through the process from the basic concepts to advanced application steps.

### Introduction to Vhosts and Sub-domains

* **What is a VHost?** A VHost, or Virtual Host, is essentially a sub-domain that is served from the same server as the main domain and shares the same IP address. This enables a single server to host multiple websites or sub-domains.
    
* **Difference Between VHosts and Sub-domains:** The primary distinction lies in their accessibility and DNS records. While sub-domains may have public DNS records, VHosts may not, making them invisible to public DNS queries and thus not directly accessible via standard web browsing methods.
    

### The Concept of Vhost Fuzzing

* **Objective:** Vhost Fuzzing is employed to identify both public and non-public sub-domains/VHosts on a server by fuzzing HTTP headers, particularly the `Host:` header.
    
* **How It Works:** By modifying the `Host:` header in HTTP requests, it's possible to simulate requests to different VHosts on the same server, thereby revealing those that are not publicly listed in DNS records.
    

### Step-by-Step Guide to Vhost Fuzzing

1. **Preparation:**
    
    * Ensure you have access to a command-line tool like `ffuf` for fuzzing purposes.
        
    * Obtain a wordlist such as `/opt/useful/SecLists/Discovery/DNS/subdomains-top1million-5000.txt`. This list will be used to fuzz the `Host:` header.
        
    
    ### **MATCHER OPTIONS:**
    
    * `mc` (Match HTTP status codes): This option allows `ffuf` to only consider responses with specific HTTP status codes as positive hits. You can specify a single status code, a comma-separated list of codes, or a range. Using `"all"` makes `ffuf` match every status code. For example, `mc 200,301,302` would only match responses with status codes 200, 301, or 302. The default value is `200,204,301,302,307,401,403`.
        
    * `ml` (Match amount of lines in response): With this option, responses that have a specific number of lines are considered hits. This can be useful for finding pages that deviate from a standard error or redirect page, which might have a consistent line count.
        
    * `mr` (Match regexp): This allows matching responses based on a regular expression. It's particularly useful for identifying specific content or patterns within the response body that signify a successful find.
        
    * `ms` (Match HTTP response size): This option matches responses based on their size in bytes. Like with line counts, deviations in size from the norm can indicate a noteworthy response.
        
    * `mw` (Match amount of words in response): This matcher considers the number of words in a response. It can be used similarly to `ml` and `ms` to identify responses that stand out from the majority.
        
    
    ### **FILTER OPTIONS:**
    
    * `fc` (Filter HTTP status codes from response): This option is the inverse of `mc`. It tells `ffuf` to ignore responses with certain HTTP status codes. You can specify individual codes, a list, or ranges to exclude from results.
        
    * `fl` (Filter by amount of lines in response): This filters out responses that have a specific number of lines. It's useful for excluding common error pages or redirects that have a predictable line count.
        
    * `fr` (Filter regexp): Responses matching the specified regular expression will be excluded from the results. This is useful for filtering out common error messages or other irrelevant content patterns.
        
    * `fs` (Filter HTTP response size): This excludes responses of a certain size in bytes from the results. By filtering out common sizes, you can focus on responses that are more likely to be interesting.
        
    * `fw` (Filter by amount of words in response): Responses with a specific word count are ignored. This can help in excluding pages with standard lengths, like error messages or placeholders.
        
2. **Executing the Fuzz:**
    
    * Run the following command to start fuzzing:
        
        ```bash
        
        ffuf -w /opt/useful/SecLists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u <http://academy.htb>:PORT/ -H 'Host: FUZZ.academy.htb'
        
        ─[us-academy-1]─[10.10.14.221]─[htb-ac-79442@htb-vy5wmjt9vv]─[~]
        └──╼ [★]$ ffuf -w /opt/useful/SecLists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u <http://admin.academy.htb:48721/> -H 'Host: FUZZ.academy.htb'
        
                /'___\\  /'___\\           /'___\\       
               /\\ \\__/ /\\ \\__/  __  __  /\\ \\__/       
               \\ \\ ,__\\\\ \\ ,__\\/\\ \\/\\ \\ \\ \\ ,__\\      
                \\ \\ \\_/ \\ \\ \\_/\\ \\ \\_\\ \\ \\ \\ \\_/      
                 \\ \\_\\   \\ \\_\\  \\ \\____/  \\ \\_\\       
                  \\/_/    \\/_/   \\/___/    \\/_/       
        
               v2.1.0
        ________________________________________________
        
         :: Method           : GET
         :: URL              : <http://admin.academy.htb:48721/>
         :: Wordlist         : FUZZ: /opt/useful/SecLists/Discovery/DNS/subdomains-top1million-5000.txt
         :: Header           : Host: FUZZ.academy.htb
         :: Follow redirects : false
         :: Calibration      : false
         :: Timeout          : 10
         :: Threads          : 40
         :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
        ________________________________________________
        
        localhost               [Status: 200, Size: 986, Words: 423, Lines: 56, Duration: 141ms]
        smtp                    [Status: 200, Size: 986, Words: 423, Lines: 56, Duration: 140ms]
        ns4                     [Status: 200, Size: 986, Words: 423, Lines: 56, Duration: 140ms]
        webmail                 [Status: 200, Size: 986, Words: 423, Lines: 56, Duration: 141ms]
        webdisk                 [Status: 200, Size: 986, Words: 423, Lines: 56, Duration: 140ms]
        dev                     [Status: 200, Size: 986, Words: 423, Lines: 56, Duration: 140ms]
        imap                    [Status: 200, Size: 986, Words: 423, Lines: 56, Duration: 140ms]
        mx                      [Status: 200, Size: 986, Words: 423, Lines: 56, Duration: 140ms]
        ns1                     [Status: 200, Size: 986, Words: 423, Lines: 56, Duration: 140ms]
        old                     [Status: 200, Size: 986, Words: 423, Lines: 56, Duration: 140ms]
        m                       [Status: 200, Size: 986, Words: 423, Lines: 56, Duration: 140ms]
        mysql                   [Status: 200, Size: 986, Words: 423, Lines: 56, Duration: 140ms]
        admin                   [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 140ms]
        
        ```
        
    * This command uses `ffuf` to fuzz the `Host:` header with entries from the provided wordlist, targeting the specified URL.
        
3. **Interpreting Results:**
    
    * Initially, you may observe many `200 OK` responses, indicating successful connections. However, this does not directly confirm the existence of a VHost.
        
    * **Key Indicator:** A different response size or status code for certain requests can hint at the presence of a VHost, as it likely serves a different page.
        
4. **Filtering Results:**
    
    * To refine the results, apply a filter to exclude common response sizes that indicate non-specific responses, e.g., `fs 900` to exclude responses of size 900 bytes.
        
    * Rerun the fuzzing command with the added filter to identify anomalies more efficiently.
        
5. **Verification:**
    
    * Once a potential VHost is identified (e.g., [`admin.academy`](http://admin.academy)`.htb`), attempt to access it directly through a browser. Remember to update your `/etc/hosts` file to resolve the domain locally if necessary.
        
    * Accessing a unique page (not found on the main domain) or receiving a different response further confirms the presence of the VHost.
        
6. **Advanced Scanning:**
    
    * For deeper exploration, consider running a recursive scan on the identified VHost to discover specific pages or applications hosted.
        

### Common Errors and Troubleshooting

* **Empty Responses or Unusual Status Codes:** Verify that the `Host:` header is correctly formatted and that you are using the correct port. Also, ensure that the domain is correctly mapped in your `/etc/hosts` file.
    
* **No Results After Filtering:** Adjust your filter criteria. The response size or status code used for filtering may not accurately capture the variance in responses from different VHosts.
    

### Best Practices and Tips

* **Thorough Wordlist Selection:** The quality and relevance of your wordlist significantly impact the success rate of Vhost Fuzzing. Ensure your wordlist is comprehensive and tailored to the target domain.
    
* **Ethical Considerations:** Always conduct fuzzing and other reconnaissance activities responsibly and with permission to avoid legal and ethical issues.
    
* **Continuous Learning:** Stay updated on the latest tools and techniques in network security to refine your fuzzing strategies and improve outcomes.