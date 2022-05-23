Metabase connection pool exhaustion
===============================

## What

A simple environment that chokes the connection pool.

Contents of the docker-compose:
- Metabase Enterprise v1.41.3 with MB_JDBC_DATA_WAREHOUSE_MAX_CONNECTION_POOL_SIZE=2 (just 2 connections max to the data warehouse)
- Postgres 14.2 as the app database
- Setup container with some tweaks (waits for MariaDB to be alive and then it creates 10 questions out of it)
- A MariaDB container that is built from scratch with the sample database

Metabase is exposed in port 3000, the MySQL database exposes 3306 so you can connect from outside and see the connections (use MySQL Workbench or )

All containers are inside the same network for visibility

## How

1) Clone this repo
2) cd into it
3) docker-compose up 
4) use an incognito session and go to localhost:3000, authenticate with 'a@b.com' as the user and 'metabot1' as password
5) create a new dashboard and add the 10 questions you have, save it, you see that the questions are answered
6) Now refresh the dashboard (so you fire 10 questions at the same time), see some questions being answered, all the rest won't, you clogged the connection pool, and also completely killed the instance (but the app is alive!).

# How, another way

1) Clone this repo
2) cd into it
3) docker-compose up 
4) use an incognito session and go to localhost:3000, authenticate with 'a@b.com' as the user and 'metabot1' as password
5) go to 'Our Analytics' and click on the question 'Orders, Average of Quantity, Grouped by Product → Vendor and Product → Category' (I'm sure any other works)
6) click on "Show editor" and then on "View the SQL", convert it to SQL
7) after the 'avg' column, add 'sleep(5)' without the quotes. Save the question (it will never save)
8) Repeat steps 5-7 again. You clogged the connection pool

## Requirements

- Docker
- Docker-compose