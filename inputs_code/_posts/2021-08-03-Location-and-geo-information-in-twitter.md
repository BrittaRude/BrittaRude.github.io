---
layout: post
title: "Extracting geographic location information from twitter"
date: 2021-08-01
myexcerpt: Some twitter users publish information about locations in twitter. They might indicate their place of residence or tweet from a specific location. They might also mention a location in their tweet either by simple words or by hashtags. How can we access this information from twitter and make use of it? 
image: /images/geodata.webp
---


## How can we use Python to analyze Twitter Data? The power of Tweepy.  

<img src="/images/geodata.webp" alt="geodata" style="float:left;margin: 2px 2px 2px 2px;max-width:30%;"/>

Some twitter users publish information about locations in twitter. They might indicate their place of residence or tweet from a specific location. They might also mention a location in their tweet either by simple words or by hashtags. How can we access this information from twitter and make use of it? This blogpost gives an overview of the different forms of location information in twitter tweets and gives some examples on how to access this kind of information. *[Foto by Kyle Glenn]*

## Let's filter for a specific location

There are two sources of information for geocoded data in twitter: 

1. The tweet location (Available if users share their location at the time of the tweet)
2. The account location (Available if users share their home location in their public profile) 

Geographic information is provided in [LONG, LAT] order. For more information on location data in twitter see [this link](https://developer.twitter.com/en/docs/tutorials/filtering-tweets-by-location).

### The tweet location

There are two different categories with respect to geographic information on twitter tweets: 

1. **Point Coordinates**: Tweets from GPS enabled devices will have point coordinates. The only information present here is the point coordinate (latitude/longitude). The JSON code is associated with two attributes - the *geo* attribute (showing [latitude, LONGITUDE]) and the *coordinates* attribute (showing [LONGITUDE, latitude]). 
2. **Twitter places**: A twitter place is a 4 lon-lat coordinate polygon. Twitter places contain information on the polygon coordinates, a display name, the type of the twitter place as well as the country. The JSON code consists of two attributes: The *place* atrribute (here you can find the place id, type, country code and country) as well as the *bounding box* (here you can find the coordinates of the underlying polygon). 

**There are several possibilities to filter for tweet locations:**

* The *has:geo* operator: With this operator you can filter for tweets containing *point coordinates* or *twitter places*. 
* The *place* operator: This operator filters for tweets with a specific location or Twitter place ID. 
* The *place_country* operator: Here you can filter for tweets which match the ISO alpha-2 characteristics, which you can find [here](http://en.wikipedia.org/wiki/ISO_3166-1_alpha-2). 
* The *point radius* operator: With this operator you can specify a circular geographic area and filter for tweets coming from this area. To do this, we have to define a circle: The maximum radius is 25 miles and we can then define its central point. More concretely speakig define: *point_radius:[lon lat radius]* in your query.
* The *bounding_box* operator: Similar to the above, here you can define a specific area and filter on tweets coming from this area. But instead of a circle the shape is a box. We can define a central point and the length of the box (the maximum length being 25 miles). More concretely speakig define: *bounding_box:[west_long south_lat east_long north_lat]* in your query. 

### The user location 

Twitter users might not have their GPS on while tweeting but might indicate some form of geographic information in their profile. In case that users publish information about their location in their profile section, you can access the information in the *user.location* attribute within the *user object* in JSON. 

In a second step, Twitter's *Profile Geo Enrichment* adds structural data to this information. This is done via geocoding and normalizing location strings where possible. From this, you can retrieve the following information: the user's location as well as the related country, country code, region, sub region, full name as well as coordiantes (in Longitude/Latitude). For a concrete example on how this looks like see [here](https://developer.twitter.com/en/docs/twitter-api/enterprise/enrichments/overview/profile-geo). As indicated on [this page](https://developer.twitter.com/en/docs/twitter-api/enterprise/enrichments/overview/profile-geo) there are several limitations with the Profile Geo enrichment. 

**There are several possibilities to filter for profile locations:**

* The *has:profile_geo* operator: Here you can filter for twitter tweets from users who specify their home location at least at the country level. 
* The *profile_country* operator: This information contains the underlying country code of the user's profile location.
* The *profile_region* operator: Through this operator it is possible to match a region in string format to tweets from twitter users. 
* The *profile_locality* operator: This filters for tweets based on a specific user location. 
* The *profile_subregion* operator: This information is based on the location data of Twitter Users. Here you can filter for counties or metro area.  
* The *bio_location* operator: Here you can filter for all locations by twitter users. This filter does not depend on the Profile Geo enrichment. The downside is that this information is user-generated and might not actually reflect a real location (e.g. a user might be a world inhabitant). 
* The *bio* operator: It might be that somebody describes their current location or home in their profile description (similar of what many instagram users do). You can access this information through the *bio* operator. 

You can find an overview of how to filter for locations [here](https://developer.twitter.com/en/docs/tutorials/filtering-tweets-by-location) or [here](https://developer.twitter.com/en/docs/twitter-api/enterprise/rules-and-filtering/enterprise-operators#listofoperators).

## Let's put it to practice and filter for some locations: 

### Let's filter for a place using the twitter tweet place operator


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

### Let's filter on a specific location and extract the location together with the text

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

### Let's filter for a twitter tweet's location using the place_country operator

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


### Another way to filter for a twitter tweet's location is using coordinates 

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


### Let's create a pandas dataframe from our tweets


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














