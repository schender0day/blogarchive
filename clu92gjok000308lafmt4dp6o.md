---
title: "Active Directory Series -Introduction to Domain Trusts Enumeration - Episode 01"
datePublished: Wed Mar 27 2024 00:27:22 GMT+0000 (Coordinated Universal Time)
cuid: clu92gjok000308lafmt4dp6o
slug: active-directory-series-introduction-to-domain-trusts-enumeration-episode-01
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1711498707605/faacefae-c48d-45e7-b690-6169db0fdeaf.png
tags: ad, active-directory, enumeration, activedirectory, htb-cpts

---

Domain trusts establish secure communication channels between different domains within an Active Directory environment, facilitating resource sharing and authentication.

### **Understanding the Get-ADTrust Cmdlet**

The `Get-ADTrust` cmdlet is a PowerShell command used to enumerate domain trust relationships.

### Setup

1. **Preparation**: Ensure you have access to a Windows environment with Active Directory Domain Services role.
    
2. **PowerShell Environment**: Open PowerShell with administrative privileges.
    
3. **Import Active Directory Module**:
    
    * Command: `Import-Module activedirectory`
        
    * Purpose: Loads the Active Directory module to use AD-related cmdlets.
        

### Execution

* **Command**: `Get-ADTrust -Filter *`
    
* **Explanation**: Lists all domain trusts in the Active Directory.
    

```powershell
PS C:\\htb> Import-Module activedirectory
PS C:\\htb> Get-ADTrust -Filter *

Direction               : BiDirectional
DisallowTransivity      : False
DistinguishedName       : CN=LOGISTICS.INLANEFREIGHT.LOCAL,CN=System,DC=INLANEFREIGHT,DC=LOCAL
ForestTransitive        : False
IntraForest             : True
IsTreeParent            : False
IsTreeRoot              : False
Name                    : LOGISTICS.INLANEFREIGHT.LOCAL
ObjectClass             : trustedDomain
ObjectGUID              : f48a1169-2e58-42c1-ba32-a6ccb10057ec
SelectiveAuthentication : False
SIDFilteringForestAware : False
SIDFilteringQuarantined : False
Source                  : DC=INLANEFREIGHT,DC=LOCAL
Target                  : LOGISTICS.INLANEFREIGHT.LOCAL
TGTDelegation           : False
TrustAttributes         : 32
TrustedPolicy           :
TrustingPolicy          :
TrustType               : Uplevel
UplevelOnly             : False
UsesAESKeys             : False
UsesRC4Encryption       : False

Direction               : BiDirectional
DisallowTransivity      : False
DistinguishedName       : CN=FREIGHTLOGISTICS.LOCAL,CN=System,DC=INLANEFREIGHT,DC=LOCAL
ForestTransitive        : True
IntraForest             : False
IsTreeParent            : False
IsTreeRoot              : False
Name                    : FREIGHTLOGISTICS.LOCAL
ObjectClass             : trustedDomain
ObjectGUID              : 1597717f-89b7-49b8-9cd9-0801d52475ca
SelectiveAuthentication : False
SIDFilteringForestAware : False
SIDFilteringQuarantined : False
Source                  : DC=INLANEFREIGHT,DC=LOCAL
Target                  : FREIGHTLOGISTICS.LOCAL
TGTDelegation           : False
TrustAttributes         : 8
TrustedPolicy           :
TrustingPolicy          :
TrustType               : Uplevel
UplevelOnly             : False
UsesAESKeys             : False
UsesRC4Encryption       : False
```

### **Key Output Fields Explained**

* **Direction**: Indicates if the trust is one-way or bidirectional.
    
* **IntraForest**: Shows if the trust is within the same forest.
    
* **TrustType**: Type of trust, such as uplevel (Windows 2000 or later).
    

### **Enumerating Trusts with PowerView**

PowerView is a PowerShell tool for Active Directory enumeration.

### Setup

* Ensure PowerView is installed and imported into your PowerShell session.
    

### Execution

* **Command**: `Get-DomainTrust`
    
* **Purpose**: Enumerates domain trusts, similar to `Get-ADTrust` but from PowerView, offering enhanced enumeration capabilities.
    
    ### Checking Existing Trusts with Get-DomainTrust
    
    ```powershell
    PS C:\htb> Get-DomainTrust 
    
    SourceName      : INLANEFREIGHT.LOCAL
    TargetName      : LOGISTICS.INLANEFREIGHT.LOCAL
    TrustType       : WINDOWS_ACTIVE_DIRECTORY
    TrustAttributes : WITHIN_FOREST
    TrustDirection  : Bidirectional
    WhenCreated     : 11/1/2021 6:20:22 PM
    WhenChanged     : 2/26/2022 11:55:55 PM
    
    SourceName      : INLANEFREIGHT.LOCAL
    TargetName      : FREIGHTLOGISTICS.LOCAL
    TrustType       : WINDOWS_ACTIVE_DIRECTORY
    TrustAttributes : FOREST_TRANSITIVE
    TrustDirection  : Bidirectional
    WhenCreated     : 11/1/2021 8:07:09 PM
    WhenChanged     : 2/27/2022 12:02:39 AM
    ```
    

### **Visualizing Trusts with BloodHound**

BloodHound provides a graphical representation of trust relationships.

### Setup

* Ensure BloodHound and its prerequisites are installed.
    

### Execution

* Use BloodHound to map and visualize domain trusts for easier analysis.
    
* Collecting data with SharpHound.exe, then import the data collected into BloodHound for visualization.
    

### **Common Errors and Troubleshooting**

* **Error**: Module import failure.
    
    * **Resolution**: Verify the installation of the Active Directory module or PowerView script.
        
* **Error**: Access denied.
    
    * **Resolution**: Ensure you have necessary permissions to perform enumeration.
        

### **Best Practices**

* Regularly review domain trusts to avoid potential security risks.
    
* Use visualization tools like BloodHound for easier management and analysis of trust relationships.
    

### **Checkpoint**

Try to enumerate domain trusts using both the `Get-ADTrust` cmdlet and PowerView. Compare the outputs. Ensure you understand the significance of each field in the output.

### **Further Learning**

* **Active Directory Management**: Deepen your understanding of Active Directory management and security.
    
* **PowerShell Scripting**: Enhance your PowerShell skills for more effective automation and management tasks.
    

### **Summary**

This guide provided an overview of how to enumerate domain trust relationships using PowerShell and other tools. Understanding these relationships is crucial for managing security and access within an Active Directory environment. Continue exploring Active Directory features and PowerShell scripting to enhance your skills further.