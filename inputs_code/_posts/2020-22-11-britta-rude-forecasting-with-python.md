---
layout: post
title: "Forecasting with Python - and how machine learning can help"
date: 2020-22-11
image: /images/Pandas.jpg
---

## The art of forecasting with python 

Often we need to make predictions about the future. In the private sector we would like to know how certain markets relevant to our businesses develop in the next months or years to make the right investment decisions, and in the public sector we would like to know when to expect the next episode of economic decline. There is an entire art behind the development of future forecasts. 

Economists have tried to improve their predictions through modeling for decades now, but models still tend to fail, and there is a lot of room for improvement. Lately, machine learning has fed into the art of forecasting. This blog post gives an example of how to build a forecasting model in python. For that, let's assume I am interested in the development of global wood demand during the next 10 years. Let's rely on data published by FAOSTAT for that purpose. You can find the data on this <a href="http://www.fao.org/faostat/en/#data/FO">link</a>. Let's download the import quantity data for all years, items and countries and assume that it is a good proxy for global wood demand. 

Let's know prepare the dataset for our purpose through grouping it by year. 

```python
dataset = pd.read_excel('C:/Users/FAOSTAT_data_11-13-2020.xls')

demand = dataset[dataset["Element"]=="Import Quantity"]
demand = demand[["Area", "Element", "Item", "Year", "Value"]]
demand = demand.groupby("Year").sum().reset_index()
demand.head()
```

To do forecasts in Python, we need to create a time series. This we do through passing a date-type variable to the index: 

```python
demand["Year_str"] = demand["Year"].astype(str)
demand['Year_date'] = demand['Year_str'] + "-01-01"
demand["Year_date"] = pd.to_datetime(demand["Year_date"])
demand = demand.set_index("Year_date")
demand.dtypes
```

Let's plot our graph now to see how the time series looks over time: 

```python
plt.figure(figsize=(14,8))
ax = sns.lineplot(data=demand, x="Year", y="Value")
plt.title('Global Import Quantity by Year (m3)', fontsize=12, fontweight='bold')
plt.ylabel("Value (in 100 million")
plt.savefig('C:/Users/Rude/Documents/World Bank/Forestry/Paper/Forecast/LinePlot.png')
```

So we are all set up now to do our forecast. But first, let's have a look at which economic model we will use to do our forecast. 

## The theory behind the practice - ARIMAX forecasting 

