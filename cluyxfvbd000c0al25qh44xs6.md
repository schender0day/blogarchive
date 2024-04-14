---
title: "SQLi Series - Basic Bypassing Techniques - 02"
seoTitle: "SQL Injection Bypass Techniques: Part 2"
seoDescription: "Explore SQL injection basics: Learn to bypass web authentication using OR operators and SQL comments. Perfect for beginners in cybersecurity"
datePublished: Sun Apr 14 2024 02:48:53 GMT+0000 (Coordinated Universal Time)
cuid: cluyxfvbd000c0al25qh44xs6
slug: sqli-series-basic-bypassing-techniques-02
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1712896555307/4fe362bb-bdac-479a-9792-f519e8451888.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1712896567429/53c18fde-ce5d-432b-9a37-ef08f3104a3a.png
tags: sql, hacking, websecurity, sqlinjection

---

## **1\. Introduction**

Now that we have a basic understanding of how SQL statements work, let's dive into SQL injection. Before executing entire SQL queries, we will learn how to modify the original query by injecting the OR operator and using SQL comments to subvert the query's logic. We will demonstrate this by bypassing web authentication in this section.

## **2\. Authentication Bypass**

### **2.1. Example: Administrator Login Page**

Consider an administrator login page that takes a username and password as input. The page displays the SQL query being executed in the background:

```sql
SELECT * FROM logins WHERE username='admin' AND password = 'p@ssw0rd';

```

Our goal is to log in as the admin user without using the existing password.

### **2.2. How the Authentication Works**

The page takes in the credentials and uses the AND operator to select records matching the given username and password. If the MySQL database returns matched records, the credentials are valid, and the PHP code evaluates the login attempt condition as true. If the condition is true, the admin record is returned, and the login is validated.

When incorrect credentials are entered, the login fails due to the wrong password, leading to a false result from the AND operation.

## **3\. SQLi Discovery**

### **3.1. Testing for SQL Injection Vulnerability**

Before attempting to bypass the authentication, we need to test whether the login form is vulnerable to SQL injection. We will try to add one of the following payloads after our username and see if it causes any errors or changes in page behavior:

| Payload | URL Encoded |
| --- | --- |
| ' | %27 |
| " | %22 |
| # | %23 |
| ; | %3B |
| ) | %29 |

Note: In some cases, we may have to use the URL encoded version of the payload, especially when putting the payload directly in the URL (i.e., HTTP GET request).

Let's start by injecting a single quote. If an SQL error is thrown instead of the Login Failed message, it indicates that the page is vulnerable to SQL injection. The resulting query would be:

```sql
SELECT * FROM logins WHERE username=''' AND password = 'something';

```

The injected quote results in an odd number of quotes, causing a syntax error. One option would be to comment out the rest of the query and write the remainder of the query as part of our injection to form a working query. Another option is to use an even number of quotes within our injected query, such that the final query would still work.

## **4\. OR Injection**

### **4.1. Abusing the OR Operator**

To bypass the authentication, we need the query to always return true, regardless of the username and password entered. We can abuse the OR operator in our SQL injection to achieve this.

According to MySQL documentation on operation precedence, the AND operator is evaluated before the OR operator. This means that if there is at least one TRUE condition in the entire query along with an OR operator, the entire query will evaluate to TRUE since the OR operator returns TRUE if one of its operands is TRUE.

An example of a condition that always returns true is `'1'='1'`. To keep the SQL query working and maintain an even number of quotes, instead of using `('1'='1')`, we will remove the last quote and use `('1'='1)`, so the remaining single quote from the original query would be in its place.

If we inject the following condition and have an OR operator between it and the original condition, it should always return true:

```sql
admin' or '1'='1

```

The final query should be:

```sql
SELECT * FROM logins WHERE username='admin' or '1'='1' AND password = 'something';

```

This means:

* If username is admin OR
    
* If 1=1 (which always returns true) AND
    
* If password is something
    

The AND operator will be evaluated first, and it will return false. Then, the OR operator will be evaluated, and if either of the statements is true, it will return true. Since `1=1` always returns true, this query will return true and grant us access.

Note: The payload we used above is one of many auth bypass payloads we can use to subvert the authentication logic. You can find a comprehensive list of SQLi auth bypass payloads in PayloadAllTheThings, each of which works on certain types of SQL queries.

### **4.2. Bypassing Authentication with OR Operator**

Let's try using `admin' or '1'='1` as the username. If we are able to log in successfully as admin, it confirms that the OR injection worked.

However, what if we did not know a valid username? Let's try the same request with a different username this time, like `notAdmin' or '1'='1`. If the login fails, it indicates that the username does not exist in the table, resulting in a false query overall.

To successfully log in once again, we need an overall true query. This can be achieved by injecting an OR condition into the password field, so it always returns true. Let's try `something' or '1'='1` as the password.

If the login is successful, it means that the additional OR condition resulted in a true query overall, as the WHERE clause returns everything in the table, and the user present in the first row is logged in. In this case, as both conditions will return true, we do not have to provide a test username and password and can directly start with the `'` injection and log in with just `' or '1' = '1`.

This works since the query evaluates to true irrespective of the username or password.

## **5\. Using Comments**

In this section, we will learn how to use comments to subvert the logic of more advanced SQL queries and end up with a working SQL query to bypass the login authentication process.

### **5.1. Comments in SQL**

Just like any other language, SQL allows the use of comments as well. Comments are used to document queries or ignore a certain part of the query. We can use two types of line comments with MySQL: `--` and `#`, in addition to an in-line comment `/**/` (though this is not usually used in SQL injections).

Example of using `--` comment:

```sql
SELECT username FROM logins;-- Selects usernames from the logins table

```

Note: In SQL, using two dashes only is not enough to start a comment. There has to be an empty space after them, so the comment starts with `(-- )`, with a space at the end. This is sometimes URL encoded as `(--+)`, as spaces in URLs are encoded as `(+)`. To make it clear, we will add another `(-)` at the end `(-- -)`, to show the use of a space character.

Example of using `#` comment:

```sql
SELECT * FROM logins WHERE username = 'admin';# You can place anything here AND password = 'something'

```

Tip: If you are inputting your payload in the URL within a browser, a `(#)` symbol is usually considered as a tag, and will not be passed as part of the URL. In order to use `(#)` as a comment within a browser, we can use `%23`, which is an URL encoded `(#)` symbol.

The server will ignore the part of the query with `AND password = 'something'` during evaluation.

### **5.2. Auth Bypass with Comments**

Let's go back to our previous example and inject `admin'--` as our username. The final query will be:

```sql
SELECT * FROM logins WHERE username='admin'-- ' AND password = 'something';

```

As we can see from the syntax highlighting, the username is now `admin`, and the remainder of the query is now ignored as a comment. Also, this way, we can ensure that the query does not have any syntax issues.

Let's try using these on the login page, and log in with the username `admin'--` and anything as the password. If we are able to bypass the authentication, it confirms that the new modified query checks for the username, with no other conditions.

### **5.3. Another Example**

SQL supports the usage of parentheses if the application needs to check for particular conditions before others. Expressions within the parentheses take precedence over other operators and are evaluated first. Let's look at a scenario like this:

```sql
SELECT * FROM logins where (username='admin' AND id > 1) AND password = md5('p@ssw0rd');

```

The above query ensures that the user's id is always greater than 1, which will prevent anyone from logging in as admin. Additionally, we also see that the password was hashed before being used in the query. This will prevent us from injecting through the password field because the input is changed to a hash.

Let's try logging in with valid credentials `admin / p@ssw0rd` to see the response. As expected, the login fails even though we supplied valid credentials because the admin's ID equals 1.

So let's try logging in with the credentials of another user, such as `tom`. Logging in as the user with an id not equal to 1 is successful.

So, how can we log in as the admin? We know from the previous section on comments that we can use them to comment out the rest of the query. Let's try using `admin'--` as the username.

The login fails due to a syntax error, as a closed one did not balance the open parenthesis. To execute the query successfully, we will have to add a closing parenthesis. Let's try using the username `admin')--` to close and comment out the rest.

If the login is successful, it means that our query modification worked. The final query as a result of our input is:

```sql
SELECT * FROM logins where (username='admin')-- ' AND id > 1) AND password = md5('p@ssw0rd');

```

## **Checkpoint: Understanding SQL Injection with Comments**

1. What are the two types of line comments used in MySQL?
    
2. How can you use comments to bypass authentication in SQL queries?
    
3. Why is it necessary to add a closing parenthesis when injecting `admin'--` in a query with parentheses?
    

### **Answers:**

1. The two types of line comments used in MySQL are `-` (with a space after it) and `#`.
    
2. Comments can be used to bypass authentication by injecting a valid username followed by a comment symbol (e.g., `admin'--`). This comments out the rest of the query, effectively ignoring any other conditions like password checks.
    
3. When injecting `admin'--` in a query with parentheses, it is necessary to add a closing parenthesis (e.g., `admin')--`) to balance the open parenthesis and avoid syntax errors. This ensures that the query is properly closed before commenting out the remaining parts.