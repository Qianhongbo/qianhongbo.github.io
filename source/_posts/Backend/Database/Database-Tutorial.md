---
title: Database Tutorial
date: 2022-03-28 22:20:28
categories: 
- Database
---

# Database Tutorial

## Install MYSQL as a Docker Container

First, we need to install Docker to our system. After that we just run the following code.

```
docker run -p 3306:3306 --name ECE141MySQL -e MYSQL_ROOT_PASS
WORD=root -e MYSQL_DATABASE=testing -d mysql
```

This creates a mysql container(ECE141MySQL) in docker, a database called "testing", and a root user (root) with a passward of "root".

```
docker exec -it ECE141MySQL bash
mysql -p // use "root" when asked for password
```

Now, we can use mysql!

## Create the first database

### Create database

```sql
create databsed <dbname>; // <dbname> can be test...
```

### Show databases

```sql
show databases;
```

### Select a database

```sql
use testing;
```

### Create a table

```sql
CREATE TABLE Authors ( 
  id INT NOT NULL auto_increment PRIMARY KEY, 
  firstname VARCHAR(50) NOT NULL, 
  lastname VARCHAR(50) NOT NULL 
);
```

### Describe the table 

```sql
describe authors;
```

```sql
+-----------+-------------+------+-----+---------+----------------+
| Field     | Type        | Null | Key | Default | Extra          |
+-----------+-------------+------+-----+---------+----------------+
| id        | int         | NO   | PRI | NULL    | auto_increment |
| firstname | varchar(50) | NO   |     | NULL    |                |
| lastname  | varchar(50) | NO   |     | NULL    |                |
+-----------+-------------+------+-----+---------+----------------+
3 rows in set (0.00 sec)
```

### Insert data into a table

```sql
INSERT INTO Authors (firstname, lastname) 
VALUES ('Elon', 'Must'), ('Bill', 'Gates'), ('Bill', 'Chen');
```

### Show rows in a table

```sql
SELECT * from Authors;
```

```sql
+----+-----------+----------+
| id | firstname | lastname |
+----+-----------+----------+
|  1 | Elon      | Must     |
|  2 | Bill      | Gates    |
|  3 | Bill      | Chen     |
+----+-----------+----------+
3 rows in set (0.00 sec)
```

```sql
SELECT * from Authors WHERE firstname='Bill' order by lastname;
```

```sql
+----+-----------+----------+
| id | firstname | lastname |
+----+-----------+----------+
|  3 | Bill      | Chen     |
|  2 | Bill      | Gates    |
+----+-----------+----------+
2 rows in set (0.01 sec)
```

```sql
SELECT count(id), firstname from Authors group by firstname;
```

```sql
+-----------+-----------+
| count(id) | firstname |
+-----------+-----------+
|         1 | Elon      |
|         2 | Bill      |
+-----------+-----------+
2 rows in set (0.00 sec)
```

### Update and delete rows

```
UPDATE Authors set lastname="Musk" where lastname="Must";
```

```
DELETE from Authors where firstname="Bill";
```

### Join data from tables

```sql
CREATE TABLE Books (
  id integer auto_increment PRIMARY KEY,
  title VARCHAR(50) NOT NULL,
  author_id int
);
```

```sql
INSERT INTO Books (title, author_id)
VALUES ("Of Earth and Mars", 1), 
("The Road Ahead", 2), 
("Business at the Speed of Thought", 2), 
("THe Mathematics of Poker", 3);
```

```sql
SELECT firstname, lastname, title
FROM Authors left join Books on Authors.id=Books.author_id;
```

```sql
+-----------+----------+-------------------+
| firstname | lastname | title             |
+-----------+----------+-------------------+
| Elon      | Musk     | Of Earth and Mars |
| Bill      | Gates    | NULL              |
| Bill      | Chen     | NULL              |
+-----------+----------+-------------------+
3 rows in set (0.00 sec)
```

