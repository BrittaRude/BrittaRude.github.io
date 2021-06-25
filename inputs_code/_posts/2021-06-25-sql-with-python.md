---
layout: post
title: "Relational Database Management Systems and Python - The case of SQL "
date: 2021-06-25
myexcerpt: 
image: /images/forecasting.jpg
---

## Relational Database Management Systems and Python 

Often, especially in the case of big data, information is stored in relational database management systems instead of simple spreadsheets (one example is [Patstat](https://www.epo.org/searching-for-patents/business/patstat.html)). When we then want to analyze this data, it may make sense to first filter these large sources of information for what we really need instead of extracting the entire database to our analysis tool, such as R and Python. One example of a relational database management system is [SQLite](https://www.sqlite.org/index.html) or [Amazon Athena](https://aws.amazon.com/athena/?nc=sn&loc=0&whats-new-cards.sort-by=item.additionalFields.postDateTime&whats-new-cards.sort-order=desc). SQL, on the other hand, is a structured query language to retrieve information from portable database resources, such as SQLite. Python, then again, comes along with several SQL libraries. These libraries make it possible to directly connect with SQLite, MySQL, and PostgreSQL databases. 

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


