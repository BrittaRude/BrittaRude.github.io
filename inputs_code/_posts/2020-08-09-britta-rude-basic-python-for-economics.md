---
layout: post
title: "Python Basics for Economics - Uploading datasets"
date: 2020-08-08
myexcerpt: Python is a programming language that can be used for a lot of different purposes. Its flexiblity, user-friendliness, as well as easy applicability make it one of the most-used programming languages. But how can we use Python for Data Science exactly? This is an introduction about how to upload datasets. 
image: /images/Pandas.jpg
---

## How can we make use of Python in Economics? 

Python is a programming language that can be used for a lot of different purposes. Its flexiblity, user-friendliness, as well as easy applicability make it one of the most-used programming languages. It's community is huge, and new development features are added on a regular basis. Python is used in web development, business applications, game development, web scraping - and also - data science. But how can we use Python for Data Science exactly? 

Python is great for data management (from merging, over crunching to descriptive statistics), that makes up nearly 80 % of the work that involves data science. We can use Python to fastly merge several datasets together, to plot, to download several excel files from a webpage into our analytic toolbox, to plot geodata from space, and Python is also great for Machine Learning. The [Python for Data Science Handbook](https://jakevdp.github.io/PythonDataScienceHandbook/) gives a great overview on how we can make use of Python in data science. 

But let's start simple. What is the value of Python over R, or Stata? Python is a general purpose object-oriented programming language. Python is like an allrounder, that can do a lot of things. It is fast, efficient, and easy to learn and code. R, on the contrary, is made by statisticians for statisticians. It is very specific, and can do really everything concerning that specific field. It is great for running regressions, as well as visualizing data, and the creation of data dashboards. The downside is that it is considered as quite weird, bumpy and complicated by many people. And Stata? Well, first of all, Stata is not open-source. Stata is very easy to use and learn, but only suitable for standard tasks. If you want to do something out of the ordinary, you will probably get to your limits with Stata. Stata is much more a statistical software rather than a programming language. In the end it is a very personal decision, but Python has been picking up over the last years, and more and more people are getting interested in its usage. 

## So what do I need from the Python Universe for Economics? 

Python is nearly like an endless universe with thousands of modules dedicated at different purposes. But we can limit Python to 15 to 20 libraries that are useful for datascience. A library is like a toolbox full of tools (called modules) that somebody else prepared for us, and that we can use without having to build them from zero again. The most important library for data scientistis is our _pandas_ library. Pandas is our toolbox for data manipulation, and contains a lot of valuable tools that we can make use of. 

<a href="https://images.unsplash.com/photo-1599221355214-aebebbfbb3bb?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=600&q=60">
<img src="https://images.unsplash.com/photo-1599221355214-aebebbfbb3bb?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=600&q=60" alt="Pandas" style="max-width:25%;"/>
<a/>

To use _pandas_, we first need to install it. To install _pandas_, open your anaconda prompt and type: 

~~~python
$pip install pandas
~~~
Then open your prefered Python User Interface (as Jupyter Notebook, or Spyder) and load the package: 

~~~python
import pandas as pd
~~~

Now we can get started on some data mining. Let's look at a concrete example, as for example the Time Use Survey published by the government of Paraguay. Yo can find the data <a href="https://www.dgeec.gov.py/microdatos/Encuesta-sobre-uso-del-tiempo.php">here</a>. 
  
First of all, lets upload the to Python via Python pandas. We have to use the read_csv command as our data is in csv-format. Let's then have a look at the columns included in the dataset, as well its shape and data types. Then let's rename some of the columns to better understand them. 

~~~python
hhdata=pd.read_csv("C:/Users/comparablehh2018_stand.csv",  sep=';', na_values=" ")
hhdata.columns
hhdata.shape
hhdata.dtypes
hhdata.rename(columns = {'DPTOREP': 'Department', 'P02': 'Age', 'P03':'hhrole', 'P06':'Gender', 'P09': 'Civilstatus', 'POBREZAI': 'Povertycat', 'A02':'LaborStatus', 'A03':'LaborStatus1h', 'A04A':'NumberJobs', 'B12':'JobTitle', 'B01REC':'Occupation', 'B15': 'LaborUnion', 'B16T': 'MonthlySalary', 'B19': 'Paymentinkind', 'B20T':'Paymentinkind_amount', 'B29':'BusinessType', 'E01F': 'Familyhelp', 'E01I': 'Tekopora', 'E01L': 'Inkindprovision', 'V17':'LandOwnership'}, copy=False, inplace=True)
~~~

One of the most important features for data mining in Python is filtering. Let's for example filter for all rural women in the dataset: 

~~~python
ruralwomen16 = hhdata.query('AREA==6 & Gender==6 & Age>16')
~~~

As a next step, let's create a new variable indicating the employment status

~~~python
ruralwomen16['LaborStatus_Cat'] = 'No' 
ruralwomen16['LaborStatus_Cat']=np.where(ruralwomen16['LaborStatus']==1,'Yes', ruralwomen16['LaborStatus_Cat'])
ruralwomen16.LaborStatus_Cat.value_counts()
~~~

As a last step, we are going to save our new dataset as a csv file to our local computer. 

~~~python
ruralwomen.to_csv('C:/Users/ruralwomen.csv')
~~~

In my next blogpost, I am going to show you how to create graphs using Python. 
