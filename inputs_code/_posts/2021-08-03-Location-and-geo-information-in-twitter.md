---
layout: post
title: "Extracting geographic location information from twitter"
date: 2021-08-01
myexcerpt: Some twitter users publish information about locations in twitter. They might indicate their place of residence or tweet from a specific location. They might also mention a location in their tweet either by simple words or by hashtags. How can we access this information from twitter and make use of it? 
image: /images/geodata.webp
---


## How can we use Python to analyze Twitter Data? The power of Tweepy.  

<img src="/images/geodata.webp" alt="geodata" style="float:left;margin: 2px 2px 2px 2px;max-width:25%;"/>

Some twitter users publish information about locations in twitter. They might indicate their place of residence or tweet from a specific location. They might also mention a location in their tweet either by simple words or by hashtags. How can we access this information from twitter and make use of it? This blogpost gives an overview of the different forms of location information in twitter tweets and gives some examples on how to access this kind of information. *[Foto by Kyle Glenn]*

## Let's filter for a specific location

There are two sources of information for geocoded data in twitter: 

1. The tweet location (Available if users share their location at the time of the tweet)
2. The account location (Available if users share their home location in their public profile) 

Geographic information is provided in [LONG, LAT] order. For more information on location data in twitter see [this link](https://developer.twitter.com/en/docs/tutorials/filtering-tweets-by-location).

You can filter for the location using Twitter's operators, which are listed [here](https://developer.twitter.com/en/docs/twitter-api/enterprise/rules-and-filtering/enterprise-operators#listofoperators).

### The tweet location

There are two different categories with respect to geographic information on twitter tweets: 

1. **Point Coordinates**: Tweets from GPS enabled devices will have point coordinates. The only information present here is the point coordinate (latitude/longitude). The JSON code is associated with two attributes - the *geo* attribute (showing [latitude, LONGITUDE]) and the *coordinates* attribute (showing [LONGITUDE, latitude]). 
2. **Twitter places**: A twitter place is a 4 lon-lat coordinate polygon. Twitter places contain information on the polygon coordinates, a display name, the type of the twitter place as well as the country. The JSON code consists of two attributes: The *place* atrribute (here you can find the place id, type, country code and country) as well as the *bounding box* (here you can find the coordinates of the underlying polygon). 

**There are several possibilities to filter for locations:**

* The *place* operator: This operator filters for tweets with a specific location or Twitter place ID. 
* The *place_country* operator: Here you can filter for tweets which match the ISO alpha-2 characteristics, which you can find [here](http://en.wikipedia.org/wiki/ISO_3166-1_alpha-2). 

You can find an overview of how to filter for locations [here](https://developer.twitter.com/en/docs/tutorials/filtering-tweets-by-location). 

## Let's filter for a place using the twitter tweet place operator


```python
new_search = "EdTech -filter:retweets"

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
new_search = "EdTech -filter:retweets"

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
new_search = "EdTech -filter:retweets"

tweets = tw.Cursor(api.search,
                   q=new_search,
                   place_country="DE",
                   lang="de",
                   since='2020-07-14').items(100)

all_tweets = [tweet.text for tweet in tweets]
all_tweets[:5]
```

```python
new_search = "EdTech -filter:retweets"

tweets = tw.Cursor(api.search,
                   q=new_search,
                   place_country="DE",
                   lang="de",
                   since='2020-07-14').items(100)

users_locs = [[tweet.text, tweet.user.location, tweet.place] for tweet in tweets]
users_locs
```

```python
new_search = "EdTech -filter:retweets"

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
new_search = "EdTech -filter:retweets"
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
new_search = "EdTech -filter:retweets"

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

There are several problems when using location data in Twitter. This [blogpost](https://towardsdatascience.com/twitter-location-analysis-c488c967a41f) summarizes it quite well. 














