---
title: "Active Directory Series - Cross-Forest Kerberoasting with Rubeus - Episode 6"
datePublished: Thu Mar 28 2024 20:30:10 GMT+0000 (Coordinated Universal Time)
cuid: clubov72s000p08l64qu62e6n
slug: active-directory-series-cross-forest-kerberoasting-with-rubeus-episode-6

---

# **Cross-Forest Kerberoasting: A Step-by-Step Guide**

## **1\. Introduction**

Cross-Forest Kerberoasting is a technique that allows attackers to compromise user accounts and gain unauthorized access across trusted domains in an Active Directory environment. This guide will provide a step-by-step explanation of how to perform Cross-Forest Kerberoasting using PowerView and Rubeus.

## **2\. Prerequisites**

Before proceeding with the steps outlined in this guide, ensure that you have the following:

* Access to a Windows machine joined to a domain with a bidirectional forest trust
    
* PowerView and Rubeus tools installed on the Windows machine
    
* Basic understanding of Active Directory, Kerberos, and Service Principal Names (SPNs)
    

## **3\. Enumerating Accounts with Associated SPNs**

To perform Cross-Forest Kerberoasting, we first need to identify user accounts in the target domain that have SPNs associated with them. We can use PowerView's `Get-DomainUser` cmdlet to achieve this.

### **3.1. Using Get-DomainUser to Enumerate Accounts with SPNs**

1. Open a PowerShell console on the Windows machine.
    
2. Load the PowerView module by running the following command:
    
    ```powershell
     .\PowerView.ps1
    ```
    
3. Run the following command to enumerate accounts with SPNs in the target domain: Replace `FREIGHTLOGISTICS.LOCAL` with the target domain's name.
    
    ```powershell
    Get-DomainUser -SPN -Domain FREIGHTLOGISTICS.LOCAL | select SamAccountName
    ```
    
4. The command will output the `samaccountname` of the accounts with associated SPNs.
    

In the example provided, we see that there is one account named `mssqlsvc` with an SPN in the target domain.

### **3.2. Enumerating the Identified Account**

To gather more information about the identified account, we can use the following command:

```powershell
Get-DomainUser -Domain FREIGHTLOGISTICS.LOCAL -Identity mssqlsvc |select samaccountname,memberof
```

This command will output the `samaccountname` and the groups the account is a member of. In the example, we see that the `mssqlsvc` account is a member of the `Domain Admins` group in the target domain.

## **4\. Performing Cross-Forest Kerberoasting with Rubeus**

Now that we have identified an account with an SPN, we can perform a Kerberoasting attack across the trust using the Rubeus tool.

### **4.1. Using Rubeus to Perform Cross-Forest Kerberoasting**

1. Open a PowerShell console on the Windows machine.
    
2. Navigate to the directory where Rubeus is located.
    
3. Run the following command to perform the Kerberoasting attack: Replace `FREIGHTLOGISTICS.LOCAL` with the target domain's name and `mssqlsvc` with the identified account's name.
    
    ```powershell
    .\Rubeus.exe kerberoast /domain:FREIGHTLOGISTICS.LOCAL /user:mssqlsvc /nowrap
    ```
    
4. Rubeus will attempt to request a Kerberos ticket-granting service (TGS) ticket for the specified account and return the associated hash.
    

### **4.2. Cracking the Obtained Hash**

The hash obtained from the Kerberoasting attack can be cracked offline using tools like Hashcat. If the hash is successfully cracked, it will reveal the plaintext password of the compromised account.

With access to the plaintext password of an account that is a member of the `Domain Admins` group in the target domain, an attacker can gain full control over both the current and target domains by leveraging the bidirectional forest trust.

## **5\. Conclusion**

Cross-Forest Kerberoasting is a powerful technique that allows attackers to expand their access across trusted domains in an Active Directory environment. By enumerating accounts with associated SPNs and performing Kerberoasting attacks, attackers can compromise high-privileged accounts and gain unauthorized access to multiple domains.

To mitigate the risk of Cross-Forest Kerberoasting, consider implementing the following measures:

* Limit the number of accounts with SPNs and regularly review and clean up unnecessary SPNs.
    
* Use strong and unique passwords for accounts with SPNs.
    
* Implement least privilege principles and minimize the number of accounts with administrative privileges.
    
* Monitor and analyze Kerberos traffic for suspicious activities and TGS ticket requests.
    

By understanding the techniques used in Cross-Forest Kerberoasting and implementing appropriate security controls, organizations can enhance their defense against this type of attack and protect their Active Directory environment.

## **6\. Practical Exercise**

To reinforce your understanding of Cross-Forest Kerberoasting, try the following exercise:

1. Set up a lab environment with two Active Directory domains that have a bidirectional forest trust.
    
2. Create a user account in the target domain and assign it an SPN.
    
3. Use PowerView to enumerate accounts with SPNs in the target domain.
    
4. Perform a Kerberoasting attack using Rubeus against the identified account.
    
5. Attempt to crack the obtained hash offline using Hashcat.
    

By practicing these steps in a controlled environment, you can gain hands-on experience with Cross-Forest Kerberoasting and deepen your understanding of the technique.

Remember to always obtain proper authorization and conduct such exercises in an ethical and legal manner.

## **7\. Additional Resources**

For further reading and exploration, consider the following resources:

* [Harmj0y's Blog: Kerberoasting Without Mimikatz](https://www.harmj0y.net/blog/powershell/kerberoasting-without-mimikatz/)
    
* [Harmj0y's Blog: Roasting AS-REPs](https://www.harmj0y.net/blog/activedirectory/roasting-as-reps/)
    
* [Active Directory Security: Kerberoasting Explained](https://adsecurity.org/?p=3458)
    
* [MITRE ATT&CK: Kerberoasting](https://attack.mitre.org/techniques/T1558/003/)