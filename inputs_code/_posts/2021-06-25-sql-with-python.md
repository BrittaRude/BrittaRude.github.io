---
layout: post
title: "Relational Database Management Systems and Python - The case of SQUL "
date: 2021-06-25
myexcerpt: 
image: /images/forecasting.jpg
---

## Relational Database Management Systems 

Often, especially in the case of big data, information is stored in relational database management systems instead of simple spreadsheets (one example is [Patstat](https://www.epo.org/searching-for-patents/business/patstat.html)). When we then want to analyze this data, it may make sense to first filter these large sources of information for what we really need instead of extracting the entire database to our analysis tool, such as R and Python. One example of a relational database management system is [SQLite](https://www.sqlite.org/index.html) or [Amazon Athena](https://aws.amazon.com/athena/?nc=sn&loc=0&whats-new-cards.sort-by=item.additionalFields.postDateTime&whats-new-cards.sort-order=desc). SQL, on the other hand, is a structured query language to retrieve information from portable database resources, such as SQLite. Python, then again, comes along with several SQL libraries. These libraries make it possible to directly connect with SQLite, MySQL, and PostgreSQL databases. 

Let's start by uploaded our required packages (This tutorial is inspired by [Bryan Cafferky](https://www.youtube.com/watch?v=xY54Emo8rQM)). 

```python
import pandas as pd
from pandasql import sqldf 
from pandasql import load_births

births = load_births()
```

