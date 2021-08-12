---
layout: post
title: "Counting words on twitter"
date: 2021-08-05
myexcerpt: We might be interested in analyzing the kind of words that are published within a certain subarea. How can we systemize and clean the text published in twitter posts to extract more useful information? This blogposts shows you how!  
image: /images/words.webp
---


## Twitter Word Count Analysis

<img src="/images/words.webp" alt="words" style="float:left;margin: 2px 2px 2px 2px;max-width:30%;"/>

We might be interested in analyzing the kind of words that are published within a certain subarea. How can we systemize and clean the text published in twitter posts to extract more useful information? In this notebook we will learn how to count and clean twitter data. We will follow this tutorial here: https://www.earthdatascience.org/courses/use-data-open-source-python/intro-to-apis/calculate-tweet-word-frequencies-in-python/


```python
import os
import tweepy as tw
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

import re

import itertools
import collections

import nltk
from nltk.corpus import stopwords
```

# Authentification 


```python
consumer_key= 'XX'
consumer_secret= 'XX'
access_token= 'XX'
access_token_secret= 'XX'
```


```python
auth = tw.OAuthHandler(consumer_key, consumer_secret)
auth.set_access_token(access_token, access_token_secret)
api = tw.API(auth, wait_on_rate_limit=True)
```

# Let's create a python list from 1,000 random tweets in twitter


```python
search_term = "#edtech -filter:retweets"

tweets = tw.Cursor(api.search,
                   q=search_term,
                   lang="en",
                   since='2020-11-01').items(1000)

all_tweets = [tweet.text for tweet in tweets]

all_tweets[:5]

```




    ['Energy &amp; carbon footprinting are almost standard practice now, but #H20 footprinting is rather new. On @Water_Day t… https://t.co/AJtcEHaiMi',
     'Keyboard short cuts for @WhiteboardChat 👇🏾 #EdChat #EdTech https://t.co/2iiMP8s1dS',
     'Discover the benefits of being an #FETChampion: #FETC #edtech https://t.co/ifLeDa8kDk',
     'Experienced educators such as Charlotte Speilman with @utschools will lead highly interactive workshops designed by… https://t.co/76VsZw64Va',
     "We're excited to announce we now have a US team! 🇺🇸 We know how important it is to have regional experts who unders… https://t.co/6275cBf1m3"]



# Let's clean our text data

Twitter data is messy, but there are several methods we can apply to clean it. Next we will do the follow: 
    
    1. Remove URLS
    2. Address case issues
    3. Remove Stopwords
    4. Remove Collection Words
    
We will then calculate and plot the word frequencies of clean tweets. 

## Remove URLs

We can remove urls through the *re* package. The *re* package allows us to access **regular expressions**, which can help us to identify patterns in a string. It searches for the specified characters and replaces them by blanks. 


```python
def remove_url(txt):
    """Replace URLs found in a text string with nothing 
    (i.e. it will remove the URL from the string).

    Parameters
    ----------
    txt : string
        A text string that you want to parse and remove urls.

    Returns
    -------
    The same txt string with url's removed.
    """

    return " ".join(re.sub("([^0-9A-Za-z \t])|(\w+:\/\/\S+)", "", txt).split())

```


```python
#Let's create a list of the clean tweets
all_tweets_no_urls = [remove_url(tweet) for tweet in all_tweets]
all_tweets_no_urls[:5]
```




    ['Energy amp carbon footprinting are almost standard practice now but H20 footprinting is rather new On WaterDay t',
     'Keyboard short cuts for WhiteboardChat EdChat EdTech',
     'Discover the benefits of being an FETChampion FETC edtech',
     'Experienced educators such as Charlotte Speilman with utschools will lead highly interactive workshops designed by',
     'Were excited to announce we now have a US team We know how important it is to have regional experts who unders']



## Address case issues

Python is case-sensitive. To create a list of unique words we therefore have to address lower- and upper-cases as Python will treat them as different words. We can use the string method *.lower()* to do this. 


```python
# Split the words from one tweet into unique elements
all_tweets_no_urls[0].split()
```




    ['Energy',
     'amp',
     'carbon',
     'footprinting',
     'are',
     'almost',
     'standard',
     'practice',
     'now',
     'but',
     'H20',
     'footprinting',
     'is',
     'rather',
     'new',
     'On',
     'WaterDay',
     't']



The above shows that there are lower- and upper-case words in our list. Let's remove this. 


```python
# Split the words from one tweet into unique elements
all_tweets_no_urls[0].lower().split()
```




    ['energy',
     'amp',
     'carbon',
     'footprinting',
     'are',
     'almost',
     'standard',
     'practice',
     'now',
     'but',
     'h20',
     'footprinting',
     'is',
     'rather',
     'new',
     'on',
     'waterday',
     't']



Let's repeat this not for one list but all tweets. 


```python
# Create a list of lists containing lowercase words for each tweet
words_in_tweet = [tweet.lower().split() for tweet in all_tweets_no_urls]
words_in_tweet[:2]

```




    [['energy',
      'amp',
      'carbon',
      'footprinting',
      'are',
      'almost',
      'standard',
      'practice',
      'now',
      'but',
      'h20',
      'footprinting',
      'is',
      'rather',
      'new',
      'on',
      'waterday',
      't'],
     ['keyboard', 'short', 'cuts', 'for', 'whiteboardchat', 'edchat', 'edtech']]



## Calculate word frequencies

We can now have a first look at the word frequencies in our list of lists. We can use the pyhton library **collections**. Especially, the function *most_common* is helpful, as it returns the most commonly used words and count them. 

First we have make one list out of the number of lists (which represent the different tweets) in our overall list. We can do this through the *itertools* function. 


```python
# List of all words across tweets
all_words_no_urls = list(itertools.chain(*words_in_tweet))

# Create counter
counts_no_urls = collections.Counter(all_words_no_urls)

counts_no_urls.most_common(15)

```




    [('the', 498),
     ('to', 498),
     ('edtech', 395),
     ('a', 298),
     ('for', 293),
     ('and', 293),
     ('of', 281),
     ('in', 245),
     ('with', 168),
     ('is', 165),
     ('education', 131),
     ('our', 130),
     ('you', 125),
     ('on', 121),
     ('are', 98)]



We can now transform our list into a pandas dataframe. This makes it easier to plot the data. 


```python
clean_tweets_no_urls = pd.DataFrame(counts_no_urls.most_common(15),
                             columns=['words', 'count'])

clean_tweets_no_urls.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>words</th>
      <th>count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>the</td>
      <td>498</td>
    </tr>
    <tr>
      <th>1</th>
      <td>to</td>
      <td>498</td>
    </tr>
    <tr>
      <th>2</th>
      <td>edtech</td>
      <td>395</td>
    </tr>
    <tr>
      <th>3</th>
      <td>a</td>
      <td>298</td>
    </tr>
    <tr>
      <th>4</th>
      <td>for</td>
      <td>293</td>
    </tr>
  </tbody>
</table>
</div>



Next, we will create a horizontal bar graph to visualize the most common words. 


```python
fig, ax = plt.subplots(figsize=(8, 8))

# Plot horizontal bar graph
clean_tweets_no_urls.sort_values(by='count').plot.barh(x='words',
                      y='count',
                      ax=ax,
                      color="purple")

ax.set_title("Common Words Found in Tweets (Including All Words)")

plt.show()
```


    
![png](images/02TwitterWordCounts_22_0.png)
    


## Remove stopwords

As you can see in the graph, there are a lot of stopwords involved. These stopwords do not tell us much for our analysis and we rather remove them. 

To remove them from our list of words we can use the python package **nltk**. 


```python
nltk.download('stopwords')
```

    [nltk_data] Downloading package stopwords to
    [nltk_data]     C:\Users\Rude\AppData\Roaming\nltk_data...
    [nltk_data]   Package stopwords is already up-to-date!
    




    True



Let's have a look at what the package defines as stopwords. 


```python
stop_words = set(stopwords.words('english'))

# View a few words from the set
list(stop_words)[0:10]
```




    ["haven't", 't', 'of', "you're", 'only', "it's", 'being', 'do', 'm', 'is']




```python
words_in_tweet[0]
```




    ['bigbrotherau',
     'bigot',
     'katie',
     'hopkins',
     'causing',
     'trouble',
     'in',
     'peoples',
     'workplace',
     'why',
     'are',
     'you',
     'allowing',
     'a',
     'covid',
     'denier',
     'to',
     'abu']



Let's eliminate all stopwords from our list of tweets. 


```python
# Remove stop words from each tweet list of words
tweets_nsw = [[word for word in tweet_words if not word in stop_words]
              for tweet_words in words_in_tweet]
```


```python
tweets_nsw[0]
```




    ['energy',
     'amp',
     'carbon',
     'footprinting',
     'almost',
     'standard',
     'practice',
     'h20',
     'footprinting',
     'rather',
     'new',
     'waterday']



## Calculate word frequencies without stopwords


```python
all_words_nsw = list(itertools.chain(*tweets_nsw))

counts_nsw = collections.Counter(all_words_nsw)

counts_nsw.most_common(15)
```




    [('edtech', 395),
     ('education', 131),
     ('new', 92),
     ('students', 91),
     ('learning', 88),
     ('amp', 67),
     ('school', 64),
     ('edchat', 55),
     ('learn', 47),
     ('great', 44),
     ('year', 43),
     ('via', 42),
     ('time', 41),
     ('classroom', 38),
     ('online', 37)]




```python
clean_tweets_nsw = pd.DataFrame(counts_nsw.most_common(15),
                             columns=['words', 'count'])

fig, ax = plt.subplots(figsize=(8, 8))

# Plot horizontal bar graph
clean_tweets_nsw.sort_values(by='count').plot.barh(x='words',
                      y='count',
                      ax=ax,
                      color="purple")

ax.set_title("Common Words Found in Tweets (Without Stop Words)")

plt.show()
```


    
![png](images/02TwitterWordCounts_33_0.png)
    


## Remove collection words

It is common to also remove the collection words in text analysis. Collection words are the words you used to query your data from twitter. 

We do this, as the term #sexualharassment+workplace will be present in every tweet and skew the word frequency analysis. 

Let's remove the collection words through the method of list comprehension. 


```python
collection_words = ['edtech']
```


```python
tweets_nsw_nc = [[w for w in word if not w in collection_words]
                 for word in tweets_nsw]
```


```python
#Words in first tweet with collection words
tweets_nsw[0]
```




    ['energy',
     'amp',
     'carbon',
     'footprinting',
     'almost',
     'standard',
     'practice',
     'h20',
     'footprinting',
     'rather',
     'new',
     'waterday']




```python
#Words in first tweet without collection words
tweets_nsw_nc[0]

```




    ['energy',
     'amp',
     'carbon',
     'footprinting',
     'almost',
     'standard',
     'practice',
     'h20',
     'footprinting',
     'rather',
     'new',
     'waterday']



## Calculate word frequencies of clean words 


```python
# Flatten list of words in clean tweets
all_words_nsw_nc = list(itertools.chain(*tweets_nsw_nc))

# Create counter of words in clean tweets
counts_nsw_nc = collections.Counter(all_words_nsw_nc)

counts_nsw_nc.most_common(15)
```




    [('education', 131),
     ('new', 92),
     ('students', 91),
     ('learning', 88),
     ('amp', 67),
     ('school', 64),
     ('edchat', 55),
     ('learn', 47),
     ('great', 44),
     ('year', 43),
     ('via', 42),
     ('time', 41),
     ('classroom', 38),
     ('online', 37),
     ('teachers', 36)]




```python
clean_tweets_ncw = pd.DataFrame(counts_nsw_nc.most_common(15),
                             columns=['words', 'count'])
clean_tweets_ncw.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>words</th>
      <th>count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>education</td>
      <td>131</td>
    </tr>
    <tr>
      <th>1</th>
      <td>new</td>
      <td>92</td>
    </tr>
    <tr>
      <th>2</th>
      <td>students</td>
      <td>91</td>
    </tr>
    <tr>
      <th>3</th>
      <td>learning</td>
      <td>88</td>
    </tr>
    <tr>
      <th>4</th>
      <td>amp</td>
      <td>67</td>
    </tr>
  </tbody>
</table>
</div>




```python
fig, ax = plt.subplots(figsize=(8, 8))

# Plot horizontal bar graph
clean_tweets_ncw.sort_values(by='count').plot.barh(x='words',
                      y='count',
                      ax=ax,
                      color="purple")

ax.set_title("Common Words Found in Tweets (Without Stop or Collection Words)")

plt.show()
```


    
![png](images/02TwitterWordCounts_42_0.png)
    



```python

```
