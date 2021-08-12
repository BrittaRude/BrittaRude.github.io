---
layout: post
title: "Using Tweepy to analyze Twitter Data with Python"
date: 2021-08-01
myexcerpt: Twitter data contains large amounts of data. Users express feelings, opinions, make statements or political claims. Twitter data has large potentials for sentiment analysis or to analyze attitudes. The downside is that it is quite messy. Python can help us to access the twitter universe more easily. This post gives an overview of how to use tweepy. 
image: /images/Tweepy.jpg
---


## How can we use Python to analyze Twitter Data? The power of Tweepy.  

<img src="/images/Tweepy.jpg" alt="Tweepy" style="float:left;margin: 2px 2px 2px 2px;max-width:20%;"/>

Twitter data contains large amounts of data. Users express feelings, opinions, make statements or political claims. Twitter data has large potentials for sentiment analysis or to analyze attitudes. The downside is that it is quite messy. Python can help us to access the twitter universe more easily. In this notebook we will have a first look on how to analyze twitter data with Python. We will use the tweepy package to do so. For more information on tweepy see: https://docs.tweepy.org/en/stable/auth_tutorial.html. *[Foto by Chris J. Davis]

## Getting a Twitter API 

The good news is that twitter simplifies our lives through providing an API (Application programming interface). What is an API? An API is a special type of code that governs the access to a server or database (in this case, the twitter server). Instead of writing an email to twitter requesting the information you are interested in, you can just query it yourself through their API.

Before starting our analysis, we need to create a twitter app. To do that go to https://developer.twitter.com/en/apps and create a developer account. After that you can create an app for your project. 

## Interacting with the API through Tweepy 

To interact with the twitter API there is a Python package called Tweepy, which we will be using in the following.

But before starting you need to create a twitter account (in case you do not have one yet). Twitter shows you how to do that here.

```python
import os
import tweepy as tw
import pandas as pd
```

## Enter keys

Retrieve the access token and access key from the developer platform. Go to Dashboard - Project App - and click on the key symbol.

```python
consumer_key= 'XXX'
consumer_secret= 'XXX'
access_token= 'XXX'
access_token_secret= 'XXX'
```

## Authentification process

Let's connect to the API through our credentials. 

```python
auth = tw.OAuthHandler(consumer_key, consumer_secret)
auth.set_access_token(access_token, access_token_secret)
api = tw.API(auth, wait_on_rate_limit=True)
```

## The content of a twitter tweet

Twitter tweets are objects which contain a variety of information. It containts *root-level* attributes as well as *child-level* attributes. For a detailed overview of the different attributes see this link: https://developer.twitter.com/en/docs/twitter-api/v1/data-dictionary/object-model/tweet 
        
Twitter tweets are rendered in JavaScript Object Notation (JSON). We can then access the JSON information through specific queries taking advantage of our *Tweepy* infrastructure.     

In general, a **twitter object** consists of the following items (a twitter object can consist of up to 150 attributes!): 

1. Twitter text

2. A user ID

3. A timestamp of when it is posted

4. Geo metadata shared by the user (only sometimes)

Each tweet also generates **entity objects** (hashtags, urls, media, and others). 

The **user object**, on the other hand, is another object containing different items describing the twitter user. In general, the following items describe a twitter user: 

1. id (is always the same)

2. name (can change)

3. location (can be empty)

4. description (can be empty)

5. followers count

6. friends count

among others. For a detailed overview see: https://developer.twitter.com/en/docs/twitter-api/v1/data-dictionary/object-model/user

The third type of object released by twitter is a **geo object**. The geo object containts the location information of a tweet. This can be an exact location of a "bounding box" indicating the region of a tweet. The following items are associated with a geo object: 

1. Place (always present when a tweet is geo-tagged)

2. Coordinates (only available under the *exact location* scenario

For deatiled information on geo objects see: https://developer.twitter.com/en/docs/twitter-api/v1/data-dictionary/object-model/geo

## Filtering tweets in twitter

We will filter our tweets for the term "sexual harassment". 

But before getting started, have a look at the different terms you can access here: https://developer.twitter.com/en/docs/twitter-api/premium/search-api/overview

This works through a query. Our query will consist of two things: 
    
1. The search term: #sexualharassment

2. The start date of our search


```python
# Define the search term and the date_since date as variables
search_words = "#sexualharassment"
date_since = "2020-07-14"
```

Let's now apply our .Cursor to skim the tweets. We will restrict the number of tweets to 1,000 for now. 


```python
# Collect tweets
tweets = tw.Cursor(api.search,
              q=search_words,
              lang="en",
              since=date_since).items(1000)
tweets
```




    <tweepy.cursor.ItemIterator at 0x2211566cf28>



The .Cursos has handed out an object which we can loop over to access the information contained. Each item consists of several subitems: 

* The text of the tweet
* The user 
* The date

## Accessing the text of the tweets in a cursor object. 

Let's loop over the .Cursos object and extract the text of each tweet. 


```python
# Iterate and print tweets
for tweet in tweets:
    print(tweet.text)    
```

## Collect the text in the tweets as a list

Now let's use list comprehension


```python
# Collect tweets
tweets = tw.Cursor(api.search,
                       q=search_words,
                       lang="en",
                       since=date_since).items(30)

# Collect a list of tweets
[tweet.text for tweet in tweets]
```



## Take care of retweets 

People tend to share (retweet) content created by others. Sometimes we want to eliminate these retweets, as they can be interpreted as dublicates. There is a command called *-filter:retweets* which can help us to take care of this. 


```python
new_search = search_words + " -filter:retweets"
new_search
```




    '#sexualharassment -filter:retweets'




```python
tweets = tw.Cursor(api.search,
                       q=new_search,
                       lang="en",
                       since=date_since).items(30)

[tweet.text for tweet in tweets]
```


## Let's find out who tweets about sexual harassment 

We can access the other items contained in a twitter tweet, such as the user's id or location 


```python
tweets = tw.Cursor(api.search, 
                           q=new_search,
                           lang="en",
                           since=date_since).items(30)

users_locs = [[tweet.user.screen_name, tweet.user.location] for tweet in tweets]
users_locs
```


## Create a pandas dataframe from a list of tweet data


```python
tweet_text = pd.DataFrame(data=users_locs, 
                    columns=['user', "location"])
tweet_text
```




## You can customize your twitter queries


```python
new_search = "sexualharassment+workplace -filter:retweets"

tweets = tw.Cursor(api.search,
                   q=new_search,
                   lang="en",
                   since='2020-07-14').items(100)

all_tweets = [tweet.text for tweet in tweets]
all_tweets[:5]
```

In my next blogpost, I will show you how to access the location information in twitter. 
