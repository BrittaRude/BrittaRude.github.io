---
layout: post
title: "Relational Database Management Systems and Python - The case of SQL "
date: 2021-06-25
myexcerpt: Often, especially in the case of big data, information is stored in relational database management systems. How can we access information saved in these systems through Python? How can we take advantage of this data structure to extract only the information we really need to be more data-efficient? 
image: /images/Database.jpg
---

## Relational Database Management Systems and Python 

<img src="/images/Database.jpg" alt="database" style="float:left;margin: 2px 2px 2px 2px;max-width:40%;"/>

Often, especially in the case of big data, information is stored in relational database management systems instead of simple spreadsheets (one example is [Patstat](https://www.epo.org/searching-for-patents/business/patstat.html)). When we then want to analyze this data, it may make sense to first filter these large sources of information for what we really need instead of extracting the entire database to our analysis tool, such as R and Python. One example of a relational database management system is [SQLite](https://www.sqlite.org/index.html) or [Amazon Athena](https://aws.amazon.com/athena/?nc=sn&loc=0&whats-new-cards.sort-by=item.additionalFields.postDateTime&whats-new-cards.sort-order=desc). SQL, on the other hand, is a structured query language to retrieve information from portable database resources, such as SQLite. Python, then again, comes along with several SQL libraries. These libraries make it possible to directly connect with SQLite, MySQL, and PostgreSQL databases [Foto by Tobias Fischer]. 

Let's start by uploading our required packages as well as an example dataset coming along with Python (load_births) (This tutorial is inspired by [Bryan Cafferky](https://www.youtube.com/watch?v=xY54Emo8rQM)). 

```python
import pandas as pd
from pandasql import sqldf 
from pandasql import load_births

births = load_births()
```

Before retrieving information from a relational database management system, we need to connect to it. In the case of Athena, this can be easily done via the PyAthenaJDBC wrapper and defining a function that will help us to connect. In our example we need credentials (an access and a secret key) to connect to the database. We also define our working directory through s3_staging_dir. 

```python
#Import packages
frompyathenajdbc import connect

# creating the connection function
conn=connect(access_key='XX',secret_key='XY',region_name='eu-central-1',schema_name='default',s3_staging_dir='s3://aws-athena-query-results-980872539443-eu-central-1/')
```
This is similar for the case of SQLite (inspired by [Real Python](https://realpython.com/python-sql-libraries/)): 

```python
#Import packages
import sqlite3
from sqlite3 import Error

# creating the connection function
def create_connection(path):

connection = None

try:

connection = sqlite3.connect(path)
print("Connection to SQLite DB successful")
    except Error as e:
        print(f"The error '{e}' occurred")
return connection
```

The above code creates a function .create_connection() which connects to an SQL Database through a predefined path, or creates a new SQL Database in case none is found in the final datapath. So let's execute to see what happens: 

```python
connection = create_connection("E:\\example.sqlite")
```
A new database is created in your root directory calles example.sqlite. 

## Create tables in SQL through Python 

Now let's create tables (a form of organizing unstructured data) in our SQLite database. In order to execute queries, we need to use cursor.execute(). Instead of typing this command directly, we will define a function using cursos.execute(): 

```python
def execute_query(connection, query):
    cursor = connection.cursor()
    try:
        cursor.execute(query)
        connection.commit()
        print("Query executed successfully")
    except Error as e:
        print(f"The error '{e}' occurred")
```
The above function will execute the query or hand out an error message. Let's create our table next. We will create a table containing country-level information about GDP, containing 5 columns: 

```python
create_country_table = """
CREATE TABLE IF NOT EXISTS countries (
  countryid INTEGER PRIMARY KEY AUTOINCREMENT,
  countryname TEXT NOT NULL,
  GDP INTEGER,
  continent TEXT,
  region TEXT
);
"""
```
We now have to execute: 

```python
execute_query(connection, create_country_table)  
```
We now have an empty table with 5 columns. How can we fill this table with values? Let's use our execute_query() function, but fill it with a different sub-command, which we will call create_countries. The logic behind this is that we store our INSERT INTO query as a string and then exectue it through execute_query. 

```python
create_countries = """
INSERT INTO
  countries (countryid, countryname, GDP, continent, region)
VALUES
  ('Germany', 3.8, 'Europe', 'Central Europe'),
  ('Spain', 1.4, 'Europe', 'South Europe'),
  ('France', 2.7, 'Europe', 'Central Europe'),
  ('Colombia', 0.3, 'Americas', 'Soutamerica'),
  ('Mexico', 1.3, 'Americas', 'Southamerica');
"""

execute_query(connection, create_countries)   
````
The country table will fill in the countryid column automatically, by incrementing order. 

## How to filter information

This is probably the most valuable and important thing we will learn in this post - how do we filter databases with high amounts of information for what we really need? We can do this through defining a function similar to our previous cursor.execute(), but including .fetchall() (something that we need to select subsets of our overall dataset): 

```python
def execute_read_query(connection, query):
    cursor = connection.cursor()
    result = None
    try:
        cursor.execute(query)
        result = cursor.fetchall()
        return result
    except Error as e:
        print(f"The error '{e}' occurred")
```

As a first try, let's collect all columns from our country table: 

```python
select_countries = "SELECT * from countries"
countries = execute_read_query(connection, select_countries)

for country in countries:
    print(country)
```

## Retrieve information from two different tables in a relational database

Let's now assume that next to our countries table there is a table with subnational regions and their respective GDP values in our Relational Database Management System. Let's assume that we want to subtract the countries with their respective GDP values as well as the subregions with their respective GDP values. To executive, we first have to join the two tables. 

```python
select_countries_subcountries = """
SELECT
  countries.countryid,
  countries.GDP,
  subcountries.subcountryid
  subcountries.subGDP
FROM
  posts
  INNER JOIN users ON countries.countryid = subcountries.country_countryid
"""

country_subcountries = execute_read_query(connection, select_users_posts)

for country_subcountry in country_subcountries:
    print(country_subcountry)
```
You can also select data from more than 2 data tables through incorporating multiple joins (see [RealPython](https://realpython.com/python-sql-libraries/) for an example). 

There are many more options of how to make use of information stored in Relational Database Management Systems. We can for example count the number of subregions per country through the command COUNT and GROUP BY. We can also filter on the matched observations in our two respective databases through WHERE, and many more. You will get the grip as soon as you start experimenting with Pyhton and SQL on your own. 

