---
title: AWS RDS
date: 2022-07-05 14:21:14
categories: 
- Fullstack
tags:
- AWS
---

## What is `RDS`

**RDS**: AWS Relational Database Service, which is a service that aids in the administration and management of databases. RDS assists with database administrative tasks that include upgrades, patching, installs, backups, monitoring, performance checks, security, etc.

### Database Engine Support

- Oracle
- PostgreSQL
- MySQL
- MariaDB
- SQL Server

## Configure a database

### What do we need to configure

- **Backups**: A copy of your database can be made on a regular interval to avoid losing data if something goes wrong.
- **Public or private**: A database could be made available on the open web, or only within a private Internet network.
- **Multiple Availability Zones:** You can configure a database to be physically in multiple data-centers.
- **Server specs:** You can choose the size of the server.

### Steps

1. Navigate to the [RDS dashboard](https://console.aws.amazon.com/rds/home) and create a PostgreSQL database with the following configuration, and leave the remaining fields as default.

| **Field**                                                    | **Value**                                                    |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Database creation method                                     | **Standard create**. Easy create option creates a private database by default. |
| Engine option                                                | PostgreSQL 12 or 13, any release candidate                   |
| Templates                                                    | Free tier                                                    |
| **Settings** DB instance identifier, master username, and password | Your choice                                                  |
| **Instance configuration** DB instance class                 | Burstable classes with minimal size like db.t3.micro or db.t2.micro |
| **Storage**                                                  | Default                                                      |
| **Connectivity** VPC and subnet Public access VPC security group Availability Zone Database port | Default YES Default No preferencce `5432`                    |
| **Additional configuration** Initial database name           | `postgres`                                                   |

### Some more steps

- Once the database is created successfully, copy and save the database endpoint, master username, and password. It will help your application discover the database.
- **Allow access to the database**: Edit the security group's inbound rule to allow incoming connections from anywhere (`0.0.0.0/0`). It will allow your local application to connect to the database.

![](https://video.udacity-data.com/topher/2022/June/629dfaac_screenshot-2022-06-06-at-6.01.39-pm/screenshot-2022-06-06-at-6.01.39-pm.jpeg)

![](https://video.udacity-data.com/topher/2022/June/629dfa8f_screenshot-2022-06-06-at-6.02.14-pm/screenshot-2022-06-06-at-6.02.14-pm.jpeg)

## Test the connection using the PostgreSQL client.

```bash
# Assuming the endpoint is: database-1.csxbuclmtj3c.us-east-1.rds.amazonaws.com
psql -h database-1.csxbuclmtj3c.us-east-1.rds.amazonaws.com -U postgres postgres
# It will open the "postgres=>" prompt if the connection is successful.
# Provide the database password when prompted.
```
