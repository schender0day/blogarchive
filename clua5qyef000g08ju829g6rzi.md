---
title: "Active Directory Series - SID History Attribute  - Episode 02"
seoTitle: "Active Directory Series - SID History Attribute"
seoDescription: "The SID History attribute plays a crucial role in Active Directory migration scenarios, ensuring users maintain access to resources in their original domain"
datePublished: Wed Mar 27 2024 18:47:13 GMT+0000 (Coordinated Universal Time)
cuid: clua5qyef000g08ju829g6rzi
slug: active-directory-series-sid-history-attribute-episode-02
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1711565159316/4d5bcf18-ba19-44a6-b26a-14d6052f4d47.jpeg

---

1. Introduction
    
    * The SID History attribute plays a crucial role in Active Directory migration scenarios, ensuring users maintain access to resources in their original domain.
        
    * Understanding the functionality and potential security risks associated with the SID History attribute is essential for maintaining a secure Active Directory environment.
        
2. SID History in Migration Scenarios
    
    * When a user is migrated from one domain to another, a new account is created in the target domain.
        
    * The original user's Security Identifier (SID) is added to the SID History attribute of the new account.
        
    * This allows the migrated user to access resources in the original domain using their new account, ensuring a smooth transition.
        
3. Cross-Domain and Intra-Domain Functionality
    
    * SID History is primarily designed to function across domains, facilitating resource access for migrated users.
        
    * However, SID History can also work within the same domain, which can lead to potential security risks if not properly managed.
        
4. Exploiting SID History with Mimikatz
    
    * Attackers can use tools like Mimikatz to perform SID History injection attacks.
        
    * By injecting the SID of an administrator account into the SID History attribute of a compromised account, attackers can gain elevated privileges.
        
    * This technique allows the compromised account to access resources associated with the injected administrator account.
        
5. Access Tokens and SID History
    
    * When a user logs in, all the SIDs associated with their account, including those in the SID History attribute, are added to the user's access token.
        
    * The access token determines the resources and permissions available to the user during their session.
        
    * If a Domain Admin account's SID is present in the SID History of a compromised account, the attacker gains Domain Admin-level access.
        
6. Persistent Domain Compromise
    
    * With Domain Admin privileges obtained through SID History injection, attackers can perform various malicious activities.
        
    * They can execute DCSync attacks to replicate Active Directory data, including password hashes, which can be used for offline cracking or pass-the-hash attacks.
        
    * Attackers can create Golden Tickets or Kerberos Ticket-Granting Tickets (TGTs), allowing them to impersonate any account in the domain and maintain persistent access.
        
7. Mitigating SID History Risks
    
    * Regularly monitor and audit SID History attributes for suspicious modifications or unauthorized SID injections.
        
    * Implement strict access controls and least privilege principles to minimize the impact of potential SID History abuse.
        
    * Use security tools and techniques, such as SID filtering and monitoring, to detect and prevent unauthorized SID History modifications.
        
    * Regularly review and remove unnecessary SID History entries, especially for high-privileged accounts.
        
8. Best Practices and Recommendations
    
    * Educate system administrators and security teams about the potential risks associated with SID History and how to properly manage it.
        
    * Implement strong password policies and regularly rotate passwords, particularly for privileged accounts.
        
    * Enable multi-factor authentication for sensitive accounts to add an extra layer of security.
        
    * Conduct regular security assessments and penetration testing to identify and remediate vulnerabilities related to SID History.
        
9. Conclusion
    
    * The SID History attribute is a valuable feature for ensuring smooth user migration between domains, but it can also introduce security risks if not properly managed.
        
    * By understanding the potential vulnerabilities associated with SID History and implementing appropriate security measures, organizations can mitigate the risk of privilege escalation and persistent domain compromise.
        
    * Regularly monitoring, auditing, and maintaining SID History attributes is crucial for maintaining a secure Active Directory environment.
        
10. Further Reading and Resources
    
    * Microsoft Documentation on SID History: \[[link](https://learn.microsoft.com/en-us/defender-for-identity/security-assessment-unsecure-sid-history-attribute)\]
        
    * "Mimikatz: Active Directory Security Technical Implementation Guide" by IRED Team: \[[link](https://adsecurity.org/?page_id=1821)\]
        
    * "Hack Tricks SID History Injection" : \[[link](https://book.hacktricks.xyz/windows-hardening/active-directory-methodology/sid-history-injection)\]
        

Key Takeaways:

* SID History enables user access to resources in their original domain after migration.
    
* Attackers can exploit SID History using tools like Mimikatz to gain elevated privileges.
    
* Injecting a Domain Admin SID into a compromised account's SID History grants the attacker Domain Admin-level access.
    
* With Domain Admin privileges, attackers can perform DCSync attacks, create Golden Tickets, and maintain persistent domain access.
    
* Regularly monitoring, auditing, and securing SID History attributes is crucial for preventing privilege escalation and mitigating risks.
    

By analyzing the SID History attribute, its role in migration scenarios, and the potential security risks associated with its exploitation, readers can gain a comprehensive understanding of this critical aspect of Active Directory security. Armed with this knowledge, they can take proactive steps to secure their environments and defend against SID History-based attacks.