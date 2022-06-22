---
title: Databases and SQL
date: 2022-05-09 20:37:41
categories: 
- Fullstack
---

## Different kinds of databases

### SQL/Relational Type Databases

SQL type databases are organized to be query-able using SQL (Structured Query Language) and organize information in tables. These are pretty much like giant spreadsheets, where an item stored in the database is a row in the table, and columns hold data points on each item.

#### Ideal Use Cases:

Repeating, structured data, such as:

- user information
- product inventories
- blogs

#### Common SQL/Relational Database Technologies:

- MySQL
- PostgreSQL
- MariaDB
- Microsoft SQL Server

### NoSQL

As you might have guessed, a NoSQL database ...doesn't use SQL. Really this means it isn't set up like a spreadsheet. These databases can take a few different forms and are used for large sets of distributed data (like for use in micro service architectures).

#### Ideal Use Cases:

Partially structured or un-structured data: really big collections of complex data, caches

#### Types of NoSQL Databases:

- Key-Value store
  - A key-value store is a non-relational, noSQL database type that stores data in key-value pairs (exactly like objects or dictionaries in programming). These databases are fast because the keys are unique and easily searchable, and they are flexible, because these key value pairs can store any combination of data types required.
- Document store
  - A document store is a non-relational, noSQL database type that organizes data into documents. Documents can hold any shape of data, which means document stores can easily handle data with no structure or that is arbitrarily nested, which can be a headache to account for in a relational way.
- Column-oriented
  - Data organized by column instead of by row. This architecture scales easily and makes fast, efficient queries. I'm including this architecture as a NoSQL type dbms, but this architecture can actually be used with SQL as well.

### Common NoSQL Database Technologies:

- Redis [Key Value store]
- MongoDB [Document store]
- Elasticsearch [Document store]
- Apache Cassandra [Column-oriented]

## PostgreSQL Cheat Sheet

### Meta Commands

- `\l` **List** databases
- `\c` **Connect** to a database
- `\dt` **Display Tables** in a database
- `\q` **Quit** out of psql to normal terminal

### Queries

- CREATE `INSERT INTO worlds (name) VALUES ('Asgard');`
- READ `SELECT* FROM herbs;`
- UPDATE `UPDATE herbs SET sighting_date = '2021-01-10' WHERE id='1';`
- DELETE `DELETE FROM herbs WHERE id='1';`
- Filters
  - BETWEEN`SELECT * FROM trips WHERE start_date BETWEEN '2021-02-01' AND '2021-02-12';`
  - LIKE `SELECT * FROM books WHERE title LIKE '%ship%';`

## Connect PostSQL using Docker

### Steps

1. Add `pg` to dependencies
2. Create environment variables
3. Use `.env` file to save the db name, user name and password
4. 

### Environment Variables

Working with sensitive information can be hard, especially when your application relies on keys and passwords in order to connect to and access databases or APIs. The instructions below will walk you through adding a library for environment variables in Node so that we can safely store information away from public eyes without moving it out of reach.

1. The library we will use for environment variables is called dotenv. You can add it via npm or yarn like this: `yarn add dotenv`
2. Once we have dotenv listed in the package.json dependencies, we need to create one file. Make a new file called `.env` in the root of the project. In that file, add this: `TEST_VAR=testing123`. This is our first environment variable!
3. One last, **super** important step. The .env file hides sensitive information and makes it available to our application via a variable, so it holds a lot of really important, secret information. Information we don't want shared even in a respository. If a gitignore file exists in your project add the .env file there. If there isn't a gitignore, add a file called `.gitignore` to the root of the project and add a single line in the file that says just `.env`. **If you include your env file in a public repository, you have completely negated the purpose of adding environment variables.**
