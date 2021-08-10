---
layout: post
title: "Using Tweepy to analyze Twitter Data with Python"
date: 2021-08-01
myexcerpt: Twitter data contains large amounts of data. Users express feelings, opinions, make statements or political claims. Twitter data has large potentials for sentiment analysis or to analyze attitudes. The downside is that it is quite messy. Python can help us to access the twitter universe more easily. This post gives an overview of how to use tweepy. 
image: /images/Tweepy.jpg
---


## How can we use Python to analyze Twitter Data? The power of Tweepy.  

<img src="/images/Tweepy.jpg" alt="Tweepy" style="float:left;margin: 2px 2px 2px 2px;max-width:20%;"/>

Twitter data contains large amounts of data. Users express feelings, opinions, make statements or political claims. Twitter data has large potentials for sentiment analysis or to analyze attitudes. The downside is that it is quite messy. Python can help us to access the twitter universe more easily. In this notebook we will have a first look on how to analyze twitter data with Python. We will use the tweepy package to do so. For more information on tweepy see: https://docs.tweepy.org/en/stable/auth_tutorial.html 




```python
import os
import tweepy as tw
import pandas as pd
```

## Enter keys

#Retrieve the access token and access key from the developer platform. Go to Dashboard - Project App - and click on the key symbol. In my case: https://developer.twitter.com/en/portal/projects/1414266105841917952/apps/21388601/keys



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




## Let's filter on a specific location

There are two sources of information for geocoded data in twitter: 

1. The tweet location (Available if users share their location at the time of the tweet)
2. The account location (Available if users share their home location in their public profile) 

Geographic information is provided in [LONG, LAT] order. For more information on location data in twitter see: https://developer.twitter.com/en/docs/tutorials/filtering-tweets-by-location

You can filter for the location using Twitter's operators, which are listed here: https://developer.twitter.com/en/docs/twitter-api/enterprise/rules-and-filtering/enterprise-operators#listofoperators

**There are several possibilities to filter for locations:**
* The *place* operator: This operator filters for tweets with a specific location or Twitter place ID. 
* The *place_country* operator: Here you can filter for tweets which match the ISO alpha-2 characteristics, which you can find here: http://en.wikipedia.org/wiki/ISO_3166-1_alpha-2

You can find an overview of how to filter for locations here: https://developer.twitter.com/en/docs/tutorials/filtering-tweets-by-location

## Let's filter for a place using the twitter tweet place operator


```python
new_search = "sexualharassment+workplace -filter:retweets"

tweets = tw.Cursor(api.search,
                   q=new_search,
                   place="München",
                   lang="en",
                   since='2020-07-14').items(100)

all_tweets = [tweet.text for tweet in tweets]
all_tweets[:5]
```



## Let's filter on a specific location and extract the location together with the text

**The below filters for the user's location**


```python
new_search = "sexualharassment+workplace -filter:retweets"

tweets = tw.Cursor(api.search,
                   q=new_search,
                   place="München",
                   lang="en",
                   since='2020-07-14').items(100)

users_locs = [[tweet.text, tweet.user.location] for tweet in tweets]
users_locs
```


## Let's filter for a twitter tweet's location using the place_country operator

**The below filters for all tweets which are geocoded. These automatically have a country code and we filter for tweets in Germany.**


```python
new_search = "sexuellebelästigung -filter:retweets"

tweets = tw.Cursor(api.search,
                   q=new_search,
                   place_country="DE",
                   lang="de",
                   since='2020-07-14').items(100)

all_tweets = [tweet.text for tweet in tweets]
all_tweets[:5]
```



```python
new_search = "sexuellebelästigung -filter:retweets"

tweets = tw.Cursor(api.search,
                   q=new_search,
                   place_country="DE",
                   lang="de",
                   since='2020-07-14').items(100)

users_locs = [[tweet.text, tweet.user.location, tweet.place] for tweet in tweets]
users_locs
```



```python
new_search = "sexuellebelästigung OR sexualharassment -filter:retweets"

tweets = tw.Cursor(api.search,
                   q=new_search,
                   place_country="DE",
                   lang="de"or"en",
                   since='2020-07-14').items(100)

all_tweets = [tweet.text for tweet in tweets]
```


```python
all_tweets
```



## Another way to filter for a twitter tweet's location is using coordinates 

This is possible through accessing the *bounding box* object. The longitude and latitude of the Tweet’s location, as a collection in the form [longitude, latitude]. The radius must be less than 25 miles. 


```python
#Let's search for tweets in Munich 
new_search = "sexuellebelästigung OR sexualharassment -filter:retweets"
geoc = "48.137154, 11.576124, 25mi"

tweets = tw.Cursor(api.search,
                   q=new_search,
                   place_country="DE",
                   geo_code=geoc, 
                   lang="de"or"en",
                   since='2020-07-14').items(1000)

all_tweets = [[tweet.coordinates, tweet.user._json['geo_enabled']] for tweet in tweets]
```


```python
all_tweets
```


## Let's create a pandas dataframe from our tweets


```python
new_search = "sexuellebelästigung -filter:retweets"

tweets = tw.Cursor(api.search,
                   q=new_search,
                   place_country="DE",
                   lang="de",
                   since='2019-07-14').items(100)

users_locs = [[tweet.text, tweet.user.location, tweet.place, tweet.coordinates, tweet.created_at] for tweet in tweets]
users_locs

tweet_df = pd.DataFrame(users_locs, columns=['text', 'user loc', 'tweet loc', 'tweet coordinates', 'tweet date'])
tweet_df.head()
```



```python
tweet_df['user loc'].hist();
```  


There are several problems when using location data in Twitter. This blogpost summarizes it quite well: https://towardsdatascience.com/twitter-location-analysis-c488c967a41f
