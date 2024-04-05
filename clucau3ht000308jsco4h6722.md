---
title: "Active Directory Series - Cross-Forest Trust Abuse from Linux - Episode 7"
datePublished: Fri Mar 29 2024 06:45:10 GMT+0000 (Coordinated Universal Time)
cuid: clucau3ht000308jsco4h6722
slug: active-directory-series-cross-forest-trust-abuse-from-linux-episode-7

---

## **Objective**

To provide a detailed, step-by-step guide on how to perform cross-forest trust abuse using Kerberoasting and bloodhound-python from a Linux attack host, based on the provided technical documentation.

## **Section 1: Introduction to Cross-Forest Trust Abuse**

### **1.1 Understanding Cross-Forest Trusts**

**Definition:** A cross-forest trust is a trust relationship between two separate Active Directory forests.  
**Purpose:** Cross-forest trusts allow users from one forest to access resources in another forest.  
**Types of cross-forest trusts:**

* a) One-way trust: Users from the trusted forest can access resources in the trusting forest, but not vice versa.
    
* b) Two-way trust (bidirectional): Users from both forests can access resources in the other forest.
    

### **1.2 Kerberoasting Across Forest Trusts**

**Concept:** Kerberoasting is an attack technique that allows an attacker to request Kerberos service tickets for Service Principal Names (SPNs) associated with user accounts.  
**Cross-forest Kerberoasting:** If a cross-forest trust exists, an attacker can potentially perform Kerberoasting across the trust boundary to obtain service tickets for user accounts in the trusted forest.

#### Checkpoint 1:

1. What is a cross-forest trust, and why is it used?
    
2. Explain the difference between a one-way and two-way cross-forest trust.
    
3. What is Kerberoasting, and how can it be used in a cross-forest trust scenario?
    

## **Section 2: Performing Cross-Forest Kerberoasting with GetUserSPNs.py**

### **2.1 Prerequisites**

* Linux attack host with Impacket installed
    
* Credentials for a user that can authenticate into the target domain
    
* DNS configured to resolve the target domain
    

### **2.2 Step-by-Step Guide**

On your Linux attack host, use the GetUserSPNs.py script from Impacket to perform cross-forest Kerberoasting:

```powershell
GetUserSPNs.py -target-domain FREIGHTLOGISTICS.LOCAL INLANEFREIGHT.LOCAL/wley
```

* `-target-domain`: Specifies the target domain (FREIGHTLOGISTICS.LOCAL in this example)
    
* `INLANEFREIGHT.LOCAL/wley`: Specifies the user credentials for authenticating into the target domain
    

Enter the password for the specified user when prompted.  
The script will retrieve the list of user accounts with associated SPNs in the target domain. In this example, we see one SPN entry for the mssqlsvc account.

### **2.3 Obtaining the TGS Ticket**

To obtain the TGS (Ticket Granting Service) ticket, rerun the GetUserSPNs.py command with the `-request` flag:

```powershell
GetUserSPNs.py -request -target-domain FREIGHTLOGISTICS.LOCAL INLANEFREIGHT.LOCAL/wley
```

The script will output the TGS ticket in the Hashcat format, which can be used for offline cracking.

### **2.4 Offline Cracking and Password Reuse**

* Attempt to crack the obtained TGS ticket offline using Hashcat with mode 13100.
    
* If successful, you will have the plaintext password for the user account associated with the SPN.
    
* Check if the cracked account exists in your current domain and if it suffers from password reuse. This could potentially provide access to the current domain if you haven't already escalated privileges.
    
* Consider performing a single password spray with the cracked password against other service accounts, as there is a possibility of password reuse across similarly named accounts in different domains.
    

#### Checkpoint 2:

1. What is the purpose of the `-target-domain` flag in the GetUserSPNs.py command?
    
2. How can you obtain the TGS ticket for offline cracking?
    
3. Why is it important to check for password reuse across domains after cracking the TGS ticket?
    

## **Section 3: Hunting Foreign Group Membership with bloodhound-python**

### **3.1 Understanding Foreign Group Membership**

**Concept:** In a bidirectional forest trust relationship, it is possible for users or admins from one domain to be members of groups in another domain.  
**Domain Local Groups:** Only Domain Local Groups allow users from outside their forest to be members.  
**Common scenario:** Highly privileged users from Domain A being members of the built-in administrators group in Domain B.

### **3.2 Configuring DNS Resolution**

If your Linux attack host does not have DNS configured to resolve the target domain, edit the `/etc/resolv.conf` file using sudo rights. Comment out the existing nameserver entries and add the domain name and IP address of the target domain controller as the nameserver:

```powershell
domain INLANEFREIGHT.LOCAL
nameserver 172.16.5.5
```

### **3.3 Running bloodhound-python**

Run the bloodhound-python tool against the target domain:

```powershell
bloodhound-python -d INLANEFREIGHT.LOCAL -dc ACADEMY-EA-DC01 -c All -u forend -p Klmcargo2
```

* `-d`: Specifies the target domain (INLANEFREIGHT.LOCAL in this example)
    
* `-dc`: Specifies the domain controller (ACADEMY-EA-DC01 in this example)
    
* `-c`: Specifies the collection method (All in this example)
    
* `-u`: Specifies the username for authentication
    
* `-p`: Specifies the password for authentication
    

The tool will collect data from the target domain and generate JSON files containing the domain information.

### **3.4 Analyzing the Results in BloodHound**

Compress the generated JSON files into a single zip file:

```powershell
zip -r ilfreight_bh.zip *.json
```

Repeat the process for the FREIGHTLOGISTICS.LOCAL domain, updating the `/etc/resolv.conf` file and running bloodhound-python with the appropriate domain information.  
Upload the compressed JSON files into the BloodHound GUI.  
In the BloodHound GUI, go to the "Analysis" tab and select "Users with Foreign Domain Group Membership".  
Choose the source domain (e.g., INLANEFREIGHT.LOCAL) to view users from that domain with group memberships in other domains.  
Identify any highly privileged users (e.g., the built-in Administrator account) from one domain that are members of privileged groups (e.g., the built-in Administrators group) in another domain.

#### Checkpoint 3:

1. What is the purpose of editing the `/etc/resolv.conf` file before running bloodhound-python?
    
2. Which BloodHound GUI feature allows you to identify users with foreign domain group memberships?
    
3. Why is it significant to find highly privileged users from one domain as members of privileged groups in another domain?
    

## **Section 4: Conclusion and Further Reading**

### **4.1 Key Takeaways**

* Cross-forest trust abuse allows attackers to perform Kerberoasting and obtain service tickets for user accounts in trusted forests.
    
* Cracked passwords from Kerberoasting can be used to check for password reuse across domains and potentially gain access to additional resources.
    
* Bloodhound-python can be used to collect data from multiple domains and identify users with foreign domain group memberships, particularly highly privileged users.
    

### **4.2 Further Reading and Resources**

* Impacket Documentation: [https://www.secureauth.com/labs/open-source-tools/impacket](https://www.secureauth.com/labs/open-source-tools/impacket)
    
* Kerberoasting Explained: [https://www.blackhillsinfosec.com/an-introduction-to-kerberoasting/](https://www.blackhillsinfosec.com/an-introduction-to-kerberoasting/)
    
* BloodHound Documentation: [https://bloodhound.readthedocs.io/](https://bloodhound.readthedocs.io/)
    
* Active Directory Security Best Practices: [https://docs.microsoft.com/en-us/windows-server/identity/ad-ds/plan/security-best-practices](https://docs.microsoft.com/en-us/windows-server/identity/ad-ds/plan/security-best-practices)