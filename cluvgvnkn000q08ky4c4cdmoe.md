---
title: "SQLi Series - Intro to MySQL and SQL Injection - 01"
seoTitle: "SQL Injection Guide: MySQL Intro - Part 1"
datePublished: Thu Apr 11 2024 16:41:58 GMT+0000 (Coordinated Universal Time)
cuid: cluvgvnkn000q08ky4c4cdmoe
slug: sqli-series-intro-to-mysql-and-sql-injection-01
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1712853698091/72330b65-ef8e-4ad5-bb1d-4540e749253c.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1712853708207/bc60e23b-3206-4a1d-a465-e533e584ff1e.png
tags: mysql, sql, hacking, pentesting, sqlinjection, mysql-tutorial

---

# **What is MySQL?**

MySQL is a popular open-source relational database management system (RDBMS) that uses Structured Query Language (SQL).

## **SQL Injection**

SQL Injection is a code injection technique that exploits vulnerabilities in the database layer of an application. It involves inserting malicious SQL statements into an entry field for execution.

## **Practical Application of MySQL**

* Retrieve, update, and delete data
    
* Create and manipulate tables and databases
    
* Manage users and permissions
    

### **Example: Connecting to MySQL Database**

`mysql -u root -p`

Enter your password to proceed.

## **Command Line Interaction**

### **The** `mysql` Utility

The `mysql` utility is a command-line tool for interacting with MySQL/MariaDB databases.

### Authentication

Use `-u` for the username and `-p` for the password

*Note: Passing the password directly in the command line is discouraged due to security concerns*

### Connecting to a Database

To connect to a specific database:

`mysql -u username -p -h host_address -P port`

### Example: Logging In

`mysql -u root -p`

For remote connection:

`mysql -u root -h your_host -P 3306 -p`

## **Creating and Managing Databases and Tables**

### **Creating a Database**

1. Log into the MySQL terminal
    
2. Execute `CREATE DATABASE users;` to create a new database named `users`
    

```sql
CREATE DATABASE users;

```

### **Defining Tables**

Tables store data in rows and columns. Each column has a specific data type, like `INT` for integers or `VARCHAR` for variable-length strings.

1. Select a database with `USE users;`
    
2. Create a table with `CREATE TABLE`
    

```sql
CREATE TABLE logins (
    id INT,
    username VARCHAR(100),
    password VARCHAR(100),
    date_of_joining DATETIME
);

```

## **SQL Statements**

Let's dive deep into SQL Statements `INSERT`, `SELECT`, `DROP`, `ALTER`, and `UPDATE`.

### `INSERT` Statement

### Objective

Learn how to use the `INSERT` statement to add new records to a database table.

### Explanation and Steps

**Syntax:**

```sql
INSERT INTO table_name VALUES (column1_value, column2_value, column3_value, ...);

```

**Selective Insertion:** You can specify columns to insert values selectively, useful for skipping columns with default values.

```sql
INSERT INTO table_name(column2, column3, ...) VALUES (column2_value, column3_value, ...);

```

### Step-by-Step Application

1. **Basic Insertion:** Insert values into all columns by specifying them in order. Example: `INSERT INTO logins VALUES(1, 'admin', 'p@ssw0rd', '2020-07-02');`
    
2. **Selective Insertion:** Specify column names to insert values into specific columns, skipping those with default or auto-increment values. Example: `INSERT INTO logins(username, password) VALUES('administrator', 'adm1n_p@ss');`
    
3. **Insert Multiple Records:** Insert multiple records in one statement by separating each record with a comma. Example: `INSERT INTO logins(username, password) VALUES ('john', 'john123!'), ('tom', 'tom123!');`
    

### Common Errors and Best Practices

* **Not Null Constraint:** Skipping columns with `NOT NULL` constraint without providing a default value results in an error.
    
* **Security Best Practice:** Never insert passwords in cleartext. Use hashing/encryption.
    

### `SELECT` Statement

### Objective

Retrieve data from tables using the `SELECT` statement.

### Explanation and Steps

**Basic Syntax:** `SELECT * FROM table_name;` retrieves all columns.

**Selective Retrieval:** `SELECT column1, column2 FROM table_name;` fetches specified columns only.

### Application Examples

1. **Retrieve All Records:** `SELECT * FROM logins;` shows all columns and records.
    
2. **Selective Columns:** `SELECT username,password FROM logins;` displays only usernames and passwords.
    

### Tips and Tricks

* **Using Wildcards:** The asterisk \`\` is a wildcard that selects all columns. Be specific in column selection for efficiency.
    

### `DROP` Statement

### Objective

Understand how to remove tables or databases using the `DROP` statement.

### Explanation and Steps

**Syntax:** `DROP TABLE table_name;`

**Usage:** Removes a table entirely without confirmation.

### Example

To remove the `logins` table: `DROP TABLE logins;`

### Warnings and Best Practices

**Irreversible Action:** `DROP` permanently deletes the table. Use with caution.

### `ALTER` Statement

### Objective

Modify table structures using the `ALTER` statement.

### Key Operations

* **Add Column:** `ALTER TABLE table_name ADD column_name column_type;`
    
* **Rename Column:** `ALTER TABLE table_name RENAME COLUMN old_name TO new_name;`
    
* **Modify Column Type:** `ALTER TABLE table_name MODIFY column_name new_type;`
    
* **Drop Column:** `ALTER TABLE table_name DROP column_name;`
    

### Examples

1. Add a new column: `ALTER TABLE logins ADD newColumn INT;`
    
2. Rename a column: `ALTER TABLE logins RENAME COLUMN newColumn TO oldColumn;`
    
3. Modify column type: `ALTER TABLE logins MODIFY oldColumn DATE;`
    
4. Drop a column: `ALTER TABLE logins DROP oldColumn;`
    

### Tips

* **Privileges:** Ensure you have the necessary permissions to alter tables.
    

### `UPDATE` Statement

### Objective

Update existing records in a table based on specified conditions.

### Explanation and Steps

**Syntax:** `UPDATE table_name SET column1=newvalue1, column2=newvalue2, ... WHERE condition;`

**Application:** Change record values that meet the condition.

### Example

To change passwords for records with `id` greater than 1: `UPDATE logins SET password = 'change_password' WHERE id > 1;`

### Common Pitfalls and Best Practices

* **Conditional Updates:** Always use a `WHERE` clause to avoid unintended mass updates.
    
* **Security Consideration:** Like with `INSERT`, ensure password values are hashed/encrypted.
    

## **Best Practices and Tips**

* **SQL Statements:** While not case-sensitive, writing SQL keywords in uppercase (e.g., `SELECT`, `CREATE TABLE`) and database entities in lowercase is recommended for readability.
    
* **Security Practices:** Always be wary of SQL injection vulnerabilities. Validate and sanitize user inputs.
    

## **What is the difference between** `ALTER` and `UPDATE`?

### `ALTER` Statement

**Purpose:** Used to modify the structure of an existing table or database objects. This includes operations like adding, deleting, or modifying columns in a table, changing data types of columns, renaming tables, and more.

**Operation Level:** The `ALTER` statement operates at the schema or structure level of the database. It changes how the table or its elements are defined but does not directly modify the data within the rows.

**Common Uses:**

* Adding a new column to a table: `ALTER TABLE table_name ADD column_name datatype;`
    
* Removing a column from a table: `ALTER TABLE table_name DROP COLUMN column_name;`
    
* Changing the data type of a column: `ALTER TABLE table_name MODIFY COLUMN column_name new_datatype;`
    
* Renaming a table: `ALTER TABLE old_table_name RENAME TO new_table_name;`
    

### `UPDATE` Statement

**Purpose:** Used to modify existing records in a table. It updates the values of specified columns in rows that meet the condition defined in the `WHERE` clause.

**Operation Level:** The `UPDATE` statement operates at the data level. It directly changes the contents of one or more rows in a table but does not alter the table's structure.

**Common Uses:**

* Updating the value of a column in specific rows: `UPDATE table_name SET column1 = value1 WHERE condition;`
    
* Updating multiple columns at once: `UPDATE table_name SET column1 = value1, column2 = value2 WHERE condition;`
    

### **Key Differences**

* **Scope:** `ALTER` changes the table design or structure, while `UPDATE` modifies the data within the table.
    
* **Operation:** `ALTER` is used for modifying the schema of the database, such as adding or deleting columns. `UPDATE` is used for changing the data stored in the table rows.
    
* **Impact:** Changes made by `ALTER` can affect the overall layout and functionality of the database and its relations. `UPDATE` affects the actual data stored, which can impact the results of queries but not the structure of the database.
    

In summary, `ALTER` is used for structural changes to the database, whereas `UPDATE` is used for modifying the data within the tables. Each serves a distinct purpose in database management and manipulation in MySQL.

### **Checkpoint**

**Exercise:** Try creating a new table named `test_table` with columns `test_id` (INT) and `test_name` (VARCHAR(50)). Use the `CREATE TABLE` statement.

**Feedback:** Verify the table creation with `SHOW TABLES;` and describe its structure using `DESCRIBE test_table;`.

## **Query Results**

In this section, we will learn how to control the results output of any query.

### **Sorting Results**

### Explanation

Sorting results allows you to order the output of a query based on one or more columns in ascending or descending order.

### Steps

1. Use the `ORDER BY` clause followed by the column name to sort the results. Example: `SELECT * FROM logins ORDER BY password;`
    
2. By default, the sort is done in ascending order. To sort in descending order, use `DESC` after the column name. Example: `SELECT * FROM logins ORDER BY password DESC;`
    
3. To sort by multiple columns, separate the column names with commas. The secondary sort is applied for duplicate values in the first column. Example: `SELECT * FROM logins ORDER BY password DESC, id ASC;`
    

### Output Examples

```powershell
mysql> SELECT * FROM logins ORDER BY password;
+----+---------------+------------+---------------------+
| id | username      | password   | date_of_joining     |
+----+---------------+------------+---------------------+
|  2 | administrator | adm1n_p@ss | 2020-07-02 11:30:50 |
|  3 | john          | john123!   | 2020-07-02 11:47:16 |
|  1 | admin         | p@ssw0rd   | 2020-07-02 00:00:00 |
|  4 | tom           | tom123!    | 2020-07-02 11:47:16 |
+----+---------------+------------+---------------------+

mysql> SELECT * FROM logins ORDER BY password DESC;
+----+---------------+------------+---------------------+
| id | username      | password   | date_of_joining     |
+----+---------------+------------+---------------------+
|  4 | tom           | tom123!    | 2020-07-02 11:47:16 |
|  1 | admin         | p@ssw0rd   | 2020-07-02 00:00:00 |
|  3 | john          | john123!   | 2020-07-02 11:47:16 |
|  2 | administrator | adm1n_p@ss | 2020-07-02 11:30:50 |
+----+---------------+------------+---------------------+

mysql> SELECT * FROM logins ORDER BY password DESC, id ASC;
+----+---------------+-----------------+---------------------+
| id | username      | password        | date_of_joining     |
+----+---------------+-----------------+---------------------+
|  1 | admin         | p@ssw0rd        | 2020-07-02 00:00:00 |
|  2 | administrator | change_password | 2020-07-02 11:30:50 |
|  3 | john          | change_password | 2020-07-02 11:47:16 |
|  4 | tom           | change_password | 2020-07-02 11:50:20 |
+----+---------------+-----------------+---------------------+

```

### `LIMIT` Results

### Explanation

The `LIMIT` clause allows you to restrict the number of records returned by a query. This is useful when dealing with large result sets.

### Steps

1. Use the `LIMIT` keyword followed by the number of records you want to retrieve. Example: `SELECT * FROM logins LIMIT 2;`
    
2. To specify an offset (starting point) for the records, include the offset before the limit count, separated by a comma. Example: `SELECT * FROM logins LIMIT 1, 2;` Note: The offset starts from 0, so `LIMIT 1, 2` will start from the 2nd record and return 2 records.
    

### Output Examples

```powershell
mysql> SELECT * FROM logins LIMIT 2;
+----+---------------+------------+---------------------+
| id | username      | password   | date_of_joining     |
+----+---------------+------------+---------------------+
|  1 | admin         | p@ssw0rd   | 2020-07-02 00:00:00 |
|  2 | administrator | adm1n_p@ss | 2020-07-02 11:30:50 |
+----+---------------+------------+---------------------+

mysql> SELECT * FROM logins LIMIT 1, 2;
+----+---------------+------------+---------------------+
| id | username      | password   | date_of_joining     |
+----+---------------+------------+---------------------+
|  2 | administrator | adm1n_p@ss | 2020-07-02 11:30:50 |
|  3 | john          | john123!   | 2020-07-02 11:47:16 |
+----+---------------+------------+---------------------+

```

### `WHERE` Clause

### Explanation

The `WHERE` clause is used to filter results based on specified conditions. It allows you to retrieve specific records that meet the given criteria.

### Steps

Use the `WHERE` keyword followed by the condition to filter the results. Example: `SELECT * FROM logins WHERE id > 1;`

### Output Example

```powershell
mysql> SELECT * FROM logins WHERE id > 1;
+----+---------------+------------+---------------------+
| id | username      | password   | date_of_joining     |
+----+---------------+------------+---------------------+
|  2 | administrator | adm1n_p@ss | 2020-07-02 11:30:50 |
|  3 | john          | john123!   | 2020-07-02 11:47:16 |
|  4 | tom           | tom123!    | 2020-07-02 11:47:16 |
+----+---------------+------------+---------------------+

```

In the example above, only records where the value of `id` is greater than 1 are returned.

### **Checkpoint**

1. Write a query to sort the `logins` table by `date_of_joining` in descending order.
    
2. Retrieve the first 3 records from the `logins` table.
    
3. Find all records in the `logins` table where the `username` starts with the letter 'a'.
    

## **SQL Operators**

SQL supports logical operators to combine multiple conditions in a query. The most common logical operators are `AND`, `OR`, and `NOT`.

### `AND` Operator

### Explanation

The `AND` operator takes two conditions and returns true if both conditions evaluate to true.

### Syntax

```sql
condition1 AND condition2

```

### Examples

```sql
mysql> SELECT 1 = 1 AND 'test' = 'test';
+---------------------------+
| 1 = 1 AND 'test' = 'test' |
+---------------------------+
|                         1 |
+---------------------------+

mysql> SELECT 1 = 1 AND 'test' = 'abc';
+--------------------------+
| 1 = 1 AND 'test' = 'abc' |
+--------------------------+
|                        0 |
+--------------------------+

```

In MySQL, any non-zero value is considered true (usually represented as 1), and 0 is considered false.

### `OR` Operator

### Explanation

The `OR` operator takes two conditions and returns true if at least one of the conditions evaluates to true.

### Syntax

```sql
condition1 OR condition2

```

### Examples

```sql
mysql> SELECT 1 = 1 OR 'test' = 'abc';
+-------------------------+
| 1 = 1 OR 'test' = 'abc' |
+-------------------------+
|                       1 |
+-------------------------+

mysql> SELECT 1 = 2 OR 'test' = 'abc';
+-------------------------+
| 1 = 2 OR 'test' = '

```

### `NOT` Operator

### Explanation

The `NOT` operator toggles a boolean value, converting true to false and vice versa.

### Syntax

```sql
NOT condition

```

### Examples

```sql
mysql> SELECT NOT 1 = 1;
+-----------+
| NOT 1 = 1 |
+-----------+
|         0 |
+-----------+

mysql> SELECT NOT 1 = 2;
+-----------+
| NOT 1 = 2 |
+-----------+
|         1 |
+-----------+

```

### **Symbol Operators**

### Explanation

The `AND`, `OR`, and `NOT` operators can also be represented using symbols:

* `AND`: `&&`
    
* `OR`: `||`
    
* `NOT`: `!`
    

### Examples

```sql
mysql> SELECT 1 = 1 && 'test' = 'abc';
+-------------------------+
| 1 = 1 && 'test' = 'abc' |
+-------------------------+
|                       0 |
+-------------------------+

mysql> SELECT 1 = 1 || 'test' = 'abc';
+-------------------------+
| 1 = 1 || 'test' = 'abc' |
+-------------------------+
|                       1 |
+-------------------------+

mysql> SELECT 1 != 1;
+--------+
| 1 != 1 |
+--------+
|      0 |
+--------+

```

### **Using Operators in Queries**

### Examples

1. List all records where the `username` is NOT 'john':
    

```sql
SELECT * FROM logins WHERE username != 'john';

```

1. Select users who have an `id` greater than 1 AND a `username` NOT equal to 'john':
    

```sql
SELECT * FROM logins WHERE username != 'john' AND id > 1;

```

### **Operator Precedence**

### Explanation

SQL supports various operations such as arithmetic, comparison, and logical operations. The order of evaluation is determined by operator precedence.

### Precedence Order (from highest to lowest)

1. Division (`/`), Multiplication (\`\`), and Modulus (`%`)
    
2. Addition (`+`) and Subtraction (\`\`)
    
3. Comparison (`=`, `>`, `<`, `<=`, `>=`, `!=`, `LIKE`)
    
4. `NOT` (`!`)
    
5. `AND` (`&&`)
    
6. `OR` (`||`)
    

### Example

```sql
SELECT * FROM logins WHERE username != 'tom' AND id > 3 - 2;

```

Evaluation steps:

1. Subtraction: `3 - 2 = 1`
    
2. Comparison: `username != 'tom'` AND `id > 1`
    
3. `AND`: Return records where both conditions are true
    

### **Checkpoint**

1. Write a query to find all records where the `username` is 'admin' OR 'administrator'.
    
2. Retrieve records where the `id` is less than 5 AND the `username` is NOT 'john'.
    

That concludes the MySQL guide covering SQL statements, query results, operators, and best practices.