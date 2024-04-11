---
title: "SQLi Series - Intro to MySQL and SQL Injection - 01"
seoTitle: "SQL Injection Guide: MySQL Intro - Part 1"
datePublished: Thu Apr 11 2024 16:41:58 GMT+0000 (Coordinated Universal Time)
cuid: cluvgvnkn000q08ky4c4cdmoe
slug: sqli-series-intro-to-mysql-and-sql-injection-01
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1712853698091/72330b65-ef8e-4ad5-bb1d-4540e749253c.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1712853708207/bc60e23b-3206-4a1d-a465-e533e584ff1e.png
tags: mysql, hacking, pentesting, sqlinjection

---

# **What is MySQL?**

MySQL is a popular open-source **relational database management system (RDBMS)** that uses **Structured Query Language (SQL)**.

## **SQL Injection**

SQL Injection is a code injection technique that exploits vulnerabilities in the database layer of an application. It involves inserting malicious SQL statements into an entry field for execution.

### **Practical Application of MySQL**

* Retrieve, update, and delete data
    
* Create and manipulate tables and databases
    
* Manage users and permissions
    

#### Example: Connecting to MySQL Database

```powershell
mysql -u root -p
```

Enter your password to proceed.

# **Command Line Interaction**

## **The** `mysql` Utility

The `mysql` utility is a command-line tool for interacting with MySQL/MariaDB databases.

### **Authentication**

* Use `-u` for the username and `-p` for the password
    
    * Note: Passing the password directly in the command line is discouraged due to security concerns
        

### **Connecting to a Database**

To connect to a specific database:

```powershell
mysql -u username -p -h host_address -P port
```

#### Example: Logging In

```powershell
mysql -u root -p
```

For remote connection:

```powershell
mysql -u root -h your_host -P 3306 -p
```

# **Creating and Managing Databases and Tables**

## **Creating a Database**

1. Log into the MySQL terminal
    
2. Execute `CREATE DATABASE users;` to create a new database named `users`
    

```powershell
CREATE DATABASE users;
```

## **Defining Tables**

Tables store data in rows and columns. Each column has a specific data type, like `INT` for integers or `VARCHAR` for variable-length strings.

1. Select a database with `USE users;`
    
2. Create a table with `CREATE TABLE`
    

```powershell
CREATE TABLE logins (
    id INT,
    username VARCHAR(100),
    password VARCHAR(100), 
    date_of_joining DATETIME
);
```

# **SQL Statements**

Let's dive deep into SQL Statements `INSERT`, `SELECT`, `DROP`, `ALTER`, and `UPDATE`.

## **INSERT Statement**

### **Objective**

Learn how to use the `INSERT` statement to add new records to a database table.

### **Explanation and Steps**

Syntax:

```powershell
INSERT INTO table_name VALUES (column1_value, column2_value, column3_value, ...);
```

Selective Insertion: You can specify columns to insert values selectively, useful for skipping columns with default values.

```powershell
INSERT INTO table_name(column2, column3, ...) VALUES (column2_value, column3_value, ...);
```

### **Step-by-Step Application**

1. Basic Insertion: Insert values into all columns by specifying them in order. Example: `INSERT INTO logins VALUES(1, 'admin', 'p@ssw0rd', '2020-07-02');`
    
2. Selective Insertion: Specify column names to insert values into specific columns, skipping those with default or auto-increment values. Example: `INSERT INTO logins(username, password) VALUES('administrator', 'adm1n_p@ss');`
    
3. Insert Multiple Records: Insert multiple records in one statement by separating each record with a comma. Example: `INSERT INTO logins(username, password) VALUES ('john', 'john123!'), ('tom', 'tom123!');`
    

### **Common Errors and Best Practices**

* Not Null Constraint: Skipping columns with `NOT NULL` constraint without providing a default value results in an error.
    
* Security Best Practice: Never insert passwords in cleartext. Use hashing/encryption.
    

## **SELECT Statement**

### **Objective**

Retrieve data from tables using the `SELECT` statement.

### **Explanation and Steps**

* Basic Syntax: `SELECT * FROM table_name;` retrieves all columns.
    
* Selective Retrieval: `SELECT column1, column2 FROM table_name;` fetches specified columns only.
    

### **Application Examples**

1. Retrieve All Records: `SELECT * FROM logins;` shows all columns and records.
    
2. Selective Columns: `SELECT username,password FROM logins;` displays only usernames and passwords.
    

### **Tips and Tricks**

* Using Wildcards: The asterisk `*` is a wildcard that selects all columns. Be specific in column selection for efficiency.
    

## **DROP Statement**

### **Objective**

Understand how to remove tables or databases using the `DROP` statement.

### **Explanation and Steps**

Syntax: `DROP TABLE table_name;`

Usage: Removes a table entirely without confirmation.

### **Example**

To remove the `logins` table: `DROP TABLE logins;`

### **Warnings and Best Practices**

Irreversible Action: `DROP` permanently deletes the table. Use with caution.

## **ALTER Statement**

### **Objective**

Modify table structures using the `ALTER` statement.

### **Key Operations**

* Add Column: `ALTER TABLE table_name ADD column_name column_type;`
    
* Rename Column: `ALTER TABLE table_name RENAME COLUMN old_name TO new_name;`
    
* Modify Column Type: `ALTER TABLE table_name MODIFY column_name new_type;`
    
* Drop Column: `ALTER TABLE table_name DROP column_name;`
    

### **Examples**

* Add a new column: `ALTER TABLE logins ADD newColumn INT;`
    
* Rename a column: `ALTER TABLE logins RENAME COLUMN newColumn TO oldColumn;`
    
* Modify column type: `ALTER TABLE logins MODIFY oldColumn DATE;`
    
* Drop a column: `ALTER TABLE logins DROP oldColumn;`
    

### **Tips**

Privileges: Ensure you have the necessary permissions to alter tables.

## **UPDATE Statement**

### **Objective**

Update existing records in a table based on specified conditions.

### **Explanation and Steps**

Syntax: `UPDATE table_name SET column1=newvalue1, column2=newvalue2, ... WHERE condition;`

Application: Change record values that meet the condition.

### **Example**

To change passwords for records with `id` greater than 1: `UPDATE logins SET password = 'change_password' WHERE id > 1;`

### **Common Pitfalls and Best Practices**

* Conditional Updates: Always use a `WHERE` clause to avoid unintended mass updates.
    
* Security Consideration: Like with `INSERT`, ensure password values are hashed/encrypted.
    

# **Best Practices and Tips**

* SQL Statements: While not case-sensitive, writing SQL keywords in uppercase (e.g., `SELECT`, `CREATE TABLE`) and database entities in lowercase is recommended for readability.
    
* Security Practices: Always be wary of SQL injection vulnerabilities. Validate and sanitize user inputs.
    

## **Checkpoint**

Exercise: Try creating a new table named `test_table` with columns `test_id` (INT) and `test_name` (VARCHAR(50)). Use the `CREATE TABLE` statement.

Feedback: Verify the table creation with `SHOW TABLES;` and describe its structure using `DESCRIBE test_table;`.

# **Further Exploration**

* Read More: Explore the official MySQL documentation for advanced features like stored procedures, triggers, and indexing.
    
* Practice: Use online platforms like Hack The Box or TryHackMe for hands-on SQL injection challenges.