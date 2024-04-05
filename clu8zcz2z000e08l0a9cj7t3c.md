---
title: "Active Directory Series - Understanding Domain Trusts"
seoTitle: "Domain Trusts in Active Directory Overview"
seoDescription: "Learn Active Directory domain trusts: types, management, and security for smooth cross-domain access"
datePublished: Tue Mar 26 2024 23:00:37 GMT+0000 (Coordinated Universal Time)
cuid: clu8zcz2z000e08l0a9cj7t3c
slug: active-directory-series-understanding-domain-trusts
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1711494625142/261e3cd7-a699-4592-b1a8-c252027be535.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1712300364850/266f38bc-e84a-4f00-a256-a904f5048831.png
tags: ad, active-directory, windows-server

---

## Introduction to Domain Trusts

The purpose of this article is explaining the basics of domain trusts within Active Directory.

Imagine two nations allowing their citizens to visit each other. A trust is like a treaty between them stating who can visit and what they can do.

Main Ideas

* Domain Trust: An agreement that permits users in one domain to access resources in another.
    
* Connection with Authentication Systems: It's similar to having a visa for entering another country because both nations trust each other's citizen verification process.
    

# Types of Domain Trusts

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1711497273808/d659e928-247c-47e3-85df-db4364426ea7.png align="center")

* Parent-Child Trust: Think of it as the default relationship between a parent company and its subsidiary, where resources are shared both ways.
    
* Shortcut Trust: This serves as a shortcut for direct resource access between two subsidiaries without involving the parent company.
    
* External Trust: Envision two companies agreeing to share resources without merging their entire infrastructures.
    
* Forest Trust: When two different forests (collections of domains) decide to share resources.
    
* Transitive vs. Non-Transitive: Refers to whether a trust extends to other trusts (transitive) or remains limited to the direct relationship (non-transitive).
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1711497251740/0d7478af-1d17-4437-a7ff-512af3883193.png align="center")

## Establishing and Managing Domain Trusts

Below are step by step guidance about on the setup and maintenance of trusts with a focus on security.

### Step-by-Step Guidance

1. Assessing Need and Security: Prior to creating a trust, assess its necessity and potential security implications.
    
2. Establishing Trusts: Follow instructions on how to set up each type of trust within Active Directory.
    
3. Ensuring Security: Regularly assess trust relationships to confirm their necessity and security.
    

## Common Challenges and Security Considerations

This section is to identify mistakes in configuring trusts and strategies for addressing security risks.

### Typical Mistakes

* Creating trusts that expose sensitive resources.
    
* Neglecting to remove or update trusts following mergers or acquisitions.
    

### Troubleshooting

Steps for auditing existing trusts and implementing measures like SID filtering to enhance security, along with reviews of trust relationships.

### Best Practices

* Principle of Least Privilege: Grant access as needed for tasks.
    
* Regular Audits: Periodically review trusts to ensure they are still essential and secure.
    

## Recommended Materials

* [**Create a two-way forest trust in Microsoft Entra Domain Services with an on-premises domain**](https://learn.microsoft.com/en-us/entra/identity/domain-services/tutorial-create-forest-trust)