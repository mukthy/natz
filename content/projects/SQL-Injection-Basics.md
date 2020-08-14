---
title: "SQL Injection Basics"
date: 2020-08-14T20:18:29+05:30
draft: false
---

# SQL Injection 

### Introduction:

SQL injection is a web security vulnerability that allows an attacker to interfere with the queries that an application makes to its database. It generally allows an attacker to view data that they are not normally able to retrieve. This might include data belonging to other users, or any other data that the application itself is able to access. In many cases, an attacker can modify or delete this data, causing persistent changes to the application's content or behavior. 

Let's say there is a shopping website.

https://insecure-website.com

When a user clicks on a ‘Gifts’ category, this is the below URL which will appear.

https://insecure-website.com/products?category=Gifts

This request executes the below SQL query in the database to retrieve the information.

```toml
SELECT * FROM products WHERE category = ‘Gifts’ AND released = 1
```

We can breakdown the SQL query as below:

`*`  => ALL Details

`FROM products`  => From the products table

`WHERE category = ‘Gifts’`  => now selecting only those data from the table which has the category ‘Gifts’

`AND released = 1`  => it is also one of the conditions to show the products from the table which are released.

This could also mean there might be a possible value of `release = 0`, this is what the not released product will be.

An Attacker can modify the below URL which can cause the DB to show all the Gift products which are not released.

https://insecure-website.com/products?category=Gifts’--

SQL Query Executed in the Database is as follows:

```toml
SELECT * FROM products WHERE category = ‘Gifts’--’AND released = 1
```
The main thing about this query or request is this ‘’--’ is interpreted as a comment and the rest of the query after this ‘--’ will be treated as a comment in the SQL Query while executing in the database.
This means if the rest of the red marked query part is commented out, then the below query will be executed, and it will show all the products (gifts) which are not released as well.

```toml
SELECT * FROM products WHERE category = ‘Gifts’
```

Now an Attacker can display all the products including the categories that they do not know.

For this the attacker has to modify the URL
https://insecure-website.com/products?category=Gifts’+OR+1=1--

Now the SQL Query for this request will be executed in DB as follows:

```toml
SELECT * FROM products WHERE category = ‘Gifts’ OR 1=1--’ AND released = 1
```

This query will return all the products either with the category is equal to “Gifts” or 1 = 1.
Since 1 = 1 is always true the query will return all the Items or Products. 

### SQL Injection in a Login Page:

Let’s take a web application’s login page which takes username and password to login.
If we enter the username = admin and password = admin123 the web application will run a SQL query in the Database to check if it is correct or not.

The SQL Query which is executed in the database looks similar to the below query:

```toml
SELECT * FROM users WHERE username = ‘admin’ AND password = ‘admin123’
```

Since there is an `AND` condition so both values have to be true to make the entire query as true which will make the login successful. If either username or password or both does not match the value stored in the database the login will be rejected.

As an attacker I will try to login as any random username with no password, which can be achieved by using a SQL comment sequence `--`. We can use `username = admin’--` which will remove the `password` check from the `WHERE` clause from the query.
Now the SQL query will be similar to what is shown below:

```toml
SELEC * FROM users WHERE username = ‘admin’--’ AND password = ‘admin123’
```

```toml
--’ AND password = ‘admin123’
```

The above highlighted part of the query will be removed from the execution since we are including the comment `--` sequence.
