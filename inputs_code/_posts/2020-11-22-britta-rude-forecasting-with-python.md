---
layout: post
title: "Forecasting with Python - and how machine learning can help"
date: 2020-11-22
image: /images/forecasting.jpg
---

## The art of forecasting with python 

Often we need to make predictions about the future. In the private sector we would like to know how certain markets relevant to our businesses develop in the next months or years to make the right investment decisions, and in the public sector we would like to know when to expect the next episode of economic decline. There is an entire art behind the development of future forecasts. 

Economists have tried to improve their predictions through modeling for decades now, but models still tend to fail, and there is a lot of room for improvement. Lately, machine learning has fed into the art of forecasting. This blog post gives an example of how to build a forecasting model in Python. For that, let's assume I am interested in the development of global wood demand during the next 10 years. Let's rely on data published by FAOSTAT for that purpose. You can find the data on this <a href="http://www.fao.org/faostat/en/#data/FO">link</a>. Let's download the import quantity data for all years, items and countries and assume that it is a good proxy for global wood demand. 

Let's know prepare the dataset for our purpose through grouping it by year. 

```python
dataset = pd.read_excel('C:/Users/FAOSTAT_data_11-13-2020.xls')

demand = dataset[dataset["Element"]=="Import Quantity"]
demand = demand[["Area", "Element", "Item", "Year", "Value"]]
demand = demand.groupby("Year").sum().reset_index()
demand.head()
```

To do forecasts in Python, we need to create a time series. A time-series is a data sequence which has timely data points, e.g. one data point for each day, month or year. In Python, we indicate a time series through passing a date-type variable to the index: 

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

## The theory behind the practice - SARIMAX forecasting 

There are a lot of ways to do forecasts, and a lot of different models which we can apply. In this blogpost I will just focus on one particular model, called the SARIMAX model, or Seasonal Autoregressive Integrated Moving Average with Explanatory Variable Model. Let's look at this one by one: 
1. <b>Seasonal (S)</b>: Seasonal means that our data has a seasonal trend, as for example business cycles, which occur over and over again at a certain point in time. 
2. <b>Autoregressive (AR)</b>: Autoregressive is a time series that depends on past values, that is, you autoregresse a future value on its past values. You define the number of past values you want to consider for your forecast, the so called order of your AR term through the parameter p. 
3. <b>Intgrated Moving Average (IMA)</b>: The integrated moving average part of an SARIMAX model comes from the fact that you take into account the past forecasting errors to correct your future forecasts. What does this means? Let's assume you have a time-series of 4 values, April, May, June and July. Now - as a first step, you predict the value in June based on the observed predictions in April and May. You then compare your actual value in June with the forecasted value, and take the deviation into account to make your prediction for July. You define the number of Moving Average terms you want to include into your model through the parameter q. 
4. <b>Explanatory Variable (X)</b>: This means that the evolution of the time series of interest does not only depend on itself, but also on external variables. Wood demand, for example, might depend on how the economy in general evolves, and on population growth. This is what marks the difference between a univariate and a multivariate forecasting model. 

But before starting to build or optimal forecasting model, we need to make our time-series stationary. Stationary means that the statistical properties like mean, variance, and autocorrelation of your dataset stay the same over time. This can be achieved through differencing our time series. Sometimes it is sufficient to difference our data once, but sometimes it might be necessary to difference it two, three or even more times. This you define through the parameter d. 


