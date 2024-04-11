---
title: "SQLi Series - Intro to MySQL and SQL Injection - 01"
seoTitle: "SQL Injection Guide: MySQL Intro - Part 1"
datePublished: Thu Apr 11 2024 16:41:58 GMT+0000 (Coordinated Universal Time)
cuid: cluvgvnkn000q08ky4c4cdmoe
slug: sqli-series-intro-to-mysql-and-sql-injection-01
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1712853698091/72330b65-ef8e-4ad5-bb1d-4540e749253c.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1712853708207/bc60e23b-3206-4a1d-a465-e533e584ff1e.png

---

**a) Concept Explanation:**

* **MySQL:** A popular open-source relational database management system (RDBMS) that uses Structured Query Language (SQL).
    
* **SQL Injection:** A code injection technique that exploits vulnerabilities in the database layer of an application. It involves inserting malicious SQL statements into an entry field for execution.
    

**b) Practical Application:**

1. **Exploring MySQL Syntax:**
    
    * Launch MySQL terminal using the command `mysql -u root -p`. Enter your password when prompted.
        
    * Understand that MySQL syntax can vary slightly across different RDBMS, but all adhere to the ISO SQL standard.
        
2. **Perform Basic Operations:**
    
    * Retrieve, update, and delete data.
        
    * Create and manipulate tables and databases.
        
    * Manage users and permissions.
        

**c) Example Configuration:**

* **Connect to MySQL Database:**
    
    ```bash
    
    mysql -u root -p
    
    ```
    
    Enter your password to proceed.
    
* **Creating a Database:**
    
    ```sql
    
    CREATE DATABASE example;
    
    ```
    

### Command Line Interaction

**a) Concept Explanation:**

* **mysql Utility:** A command-line tool for interacting with MySQL/MariaDB databases.
    

**b) Practical Application:**

1. **Authentication:**
    
    * Use `u` for the username and `p` for the password. Remember, passing the password directly in the command line is discouraged due to security concerns.
        
2. **Connecting to a Database:**
    
    * To connect to a specific database: `mysql -u username -p -h host_address -P port`.
        

**c) Example Configuration:**

* **Logging In:**
    
    ```bash
    
    mysql -u root -p
    
    ```
    
    * For remote connection:
        
    
    ```bash
    
    mysql -u root -h your_host -P 3306 -p
    
    ```
    

### **Creating and Managing Databases and Tables**

**a) Creating a Database:**

* **Step-by-Step Instructions:**
    
    1. Log into the MySQL terminal.
        
    2. Execute `CREATE DATABASE users;` to create a new database named `users`.
        
* **Code Snippet:**
    
    ```sql
    
    CREATE DATABASE users;
    
    ```
    

**b) Defining Tables:**

* **Concept Explanation:**
    
    * Tables store data in rows and columns. Each column has a specific data type, like `INT` for integers or `VARCHAR` for variable-length strings.
        
* **Step-by-Step Instructions:**
    
    1. Select a database with `USE users;`.
        
    2. Create a table with `CREATE TABLE`.
        
* **Code Snippet:**
    
    ```sql
    
    CREATE TABLE logins (
        id INT,
        username VARCHAR(100),
        password VARCHAR(100),
        date_of_joining DATETIME
    );
    
    ```
    

### **Best Practices and Tips**

* **SQL Statements:** While not case-sensitive, writing SQL keywords in uppercase (e.g., `SELECT`, `CREATE TABLE`) and database entities in lowercase is recommended for readability.
    
* **Security Practices:** Always be wary of SQL injection vulnerabilities. Validate and sanitize user inputs.
    

### **Checkpoint**

* **Exercise:** Try creating a new table named `test_table` with columns `test_id` (INT) and `test_name` (VARCHAR(50)). Use the `CREATE TABLE` statement.
    

**Feedback:** Verify the table creation with `SHOW TABLES;` and describe its structure using `DESCRIBE test_table;`.

### **Further Exploration**

* **Read More:** Explore the official MySQL documentation for advanced features like stored procedures, triggers, and indexing.
    
* **Practice:** Use online platforms like Hack The Box or TryHackMe for hands-on SQL injection challenges.