---
layout: post
title: "Using Tweepy to analyze Twitter Data with Python"
date: 2021-08-01
myexcerpt: Twitter data contains large amounts of data. Users express feelings, opinions, make statements or political claims. Twitter data has large potentials for sentiment analysis or to analyze attitudes. The downside is that it is quite messy. Python can help us to access the twitter universe more easily. This post gives an overview of how to use tweepy. 
image: /images/tweepy.jpg
---


# How can we use Python to analyze Twitter Data? The power of Tweepy.  

Twitter data contains large amounts of data. Users express feelings, opinions, make statements or political claims. Twitter data has large potentials for sentiment analysis or to analyze attitudes. The downside is that it is quite messy. Python can help us to access the twitter universe more easily. In this notebook we will have a first look on how to analyze twitter data with Python. We will use the tweepy package to do so. For more information on tweepy see: https://docs.tweepy.org/en/stable/auth_tutorial.html 

<img src="/images/tweepy.jpg" alt="database" style="float:left;margin: 2px 2px 2px 2px;max-width:15%;"/>


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

### Accessing the text of the tweets in a cursor object. 

Let's loop over the .Cursos object and extract the text of each tweet. 


```python
# Iterate and print tweets
for tweet in tweets:
    print(tweet.text)    
```

### Collect the text in the tweets as a list

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




    ['RT @Miaibar: @baybarsorsek @factchecknet @crowdtangle Do you have a sexual harasser as a facebook Factchecker??? Surely I must wait to pass…',
     'On the one hand, while stories of rape by the State machineries were prominently put forward by the comrades, stori… https://t.co/MMI6iEvvwi',
     '@HippyResident @jstanotherb @Empath___ @sIumington @Rob_WaIker @DantesUndrowned @beccas1434 @MAKEITGOB00M… https://t.co/PlXTqBzK3q',
     '@baybarsorsek @factchecknet @crowdtangle Do you have a sexual harasser as a facebook Factchecker??? Surely I must w… https://t.co/K9pfpS48bt',
     'RT @jessica_lee25: This is a letter I wrote after I worked for @Dillards Corporate where I was a victim to #sexualharassment, #inequality,…',
     'RT @jessica_lee25: This is a letter I wrote after I worked for @Dillards Corporate where I was a victim to #sexualharassment, #inequality,…',
     'RT @jessica_lee25: This is a letter I wrote after I worked for @Dillards Corporate where I was a victim to #sexualharassment, #inequality,…',
     'RT @jessica_lee25: This is a letter I wrote after I worked for @Dillards Corporate where I was a victim to #sexualharassment, #inequality,…',
     'This is a letter I wrote after I worked for @Dillards Corporate where I was a victim to #sexualharassment,… https://t.co/6A2XSPriSS',
     '#SexualHarassmentTraining \nfor #politicians will run for one hour and be optional \n\nSo #ScottyTheGaslighter\nis goin… https://t.co/GDdQtMCdBx',
     'Sony Music Australia facing a possible class action suit by former employees who were subjected to #bullying,… https://t.co/uWxs8xZmOc',
     "#SexualHarassment News:\n\n$73M payout OK'd to ~6,000 women abused by frmr #UCLA #gynecologist James Heaps\n\nNoem susp… https://t.co/oZ1QNo3y5b",
     'RT @vidyaratha: Prevention of Sexual Harassment and Sexual Assault in the Workplace #diversity #sexualharassment https://t.co/VY0QglKHe4 vi…',
     'Prevention of Sexual Harassment and Sexual Assault in the Workplace #diversity #sexualharassment https://t.co/VY0QglKHe4 via @SlideShare',
     'RT @WeAreSpeakOut: Unfortunately @LittleJanhere sums up the experience of many women and men dealing with #sexualharassment at work. Take a…',
     '"The South Bend Fire Department’s female members have sent the mayor’s office a letter demanding they deal with wha… https://t.co/JLQqicANQP',
     'RT @2Rarely: ‘A very sad day’: Colchester Royal Grammar School (CRGS) receives damning Ofsted report amid ‘rape culture’ claims\n\n#Everyones…',
     'RT @mism_2020: @LittleJanhere @CNplus https://t.co/VDoebkY56E  And yet @NicolaSturgeon government is still funding training.  #sexualharass…',
     'RT @HarassNoMore: State of affairs reflecting badly on our societal values. No one should face dismissal &amp; economic disruption for reportin…',
     "RT @doloreselee: ***TRIGGER WARNING***\n\n🙏🏿💕💜 Please take an hour out of your day to watch this short film -'A Woman's Worth' - a social med…",
     'RT @AblinLaw: Six questions you should ask about workplace bullying\n\nhttps://t.co/YUpc7u5PYU\n\n#workplaceinvestigations #hr #humanresources…',
     'Six questions you should ask about workplace bullying\n\nhttps://t.co/YUpc7u5PYU\n\n#workplaceinvestigations #hr… https://t.co/oh5iRxJm7x',
     'Sexual harassment is a form of illegal discrimination prohibited by the federal Fair Housing Act. Learn more with H… https://t.co/TADBwZlru7',
     "@jessphillips 'Coward' as all abusers &amp; sexual  predators are.\n\nThinking that a 6-week ban for #sexualharassment wi… https://t.co/N5Ur0chZ2t",
     "I think for many it seems fair to conclude that there is no effective 'code of conduct' for Ministers &amp;… https://t.co/8ULJZVXOhr",
     'RT @ItsTimeYEG: How a positive workplace culture is an important line of defense in preventing #SexualHarassment.\n\nhttps://t.co/ypvBfGHsMW',
     'RT @prsjournal: Is the #GenderDiversity Pipeline in #PlasticSurgery Leaky or Plugged?\n\nWhat does a national survey show about #GenderBias &amp;…',
     'RT @tomesper: Has office-based sexual harassment shifted to video calls and online chats due to the growth of remote working?\n\n- Wired @Wir…',
     'Has office-based sexual harassment shifted to video calls and online chats due to the growth of remote working?\n\n-… https://t.co/AIOttjhj70',
     'RT @WeAreSpeakOut: Sometimes it is difficult to know the best way to #support those close to you after they experienced  #sexualharassment…']



### Take care of retweets 

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




    ['Six questions you should ask about workplace bullying\n\nhttps://t.co/YUpc7u5PYU\n\n#workplaceinvestigations #hr… https://t.co/oh5iRxJm7x',
     'Sexual harassment is a form of illegal discrimination prohibited by the federal Fair Housing Act. Learn more with H… https://t.co/TADBwZlru7',
     "@jessphillips 'Coward' as all abusers &amp; sexual  predators are.\n\nThinking that a 6-week ban for #sexualharassment wi… https://t.co/N5Ur0chZ2t",
     "I think for many it seems fair to conclude that there is no effective 'code of conduct' for Ministers &amp;… https://t.co/8ULJZVXOhr",
     'Has office-based sexual harassment shifted to video calls and online chats due to the growth of remote working?\n\n-… https://t.co/AIOttjhj70',
     'tags: #teamhimmat #handbook #awareness #accessibility #aid #sexualharassment #collegecampus #workplace',
     'How a positive workplace culture is an important line of defense in preventing #SexualHarassment.\n\nhttps://t.co/ypvBfGHsMW',
     'The former jail warden in Westmoreland County, Pennsylvania, has filed a lawsuit alleging retaliation and sexual ha… https://t.co/c2Lk9aIG3J',
     'Take a stand - I Pledge! ✊\n\n#thatsnotok #Pledge #NoMore #silencenomore #sexualharassment #sexualassault https://t.co/hXFhcOIVHc',
     '@TTChelps @cityoftoronto The disappointing this about it was tons of people behind him clearly saw and knew what wa… https://t.co/g2c42jWbte',
     'Sexual harassment includes unwelcome sexual advances, requests for sexual favors, and other verbal or physical unwa… https://t.co/Vojot6abYg',
     'Too many men grew up believing it wasn’t “manly” to admit you’ve been sexually harassed. But men experience harassm… https://t.co/U3D0D6Ivas',
     'https://t.co/yJVGgTqYFo The Metropolitan Police force is under pressure to investigate how Sarah Everard’s killer w… https://t.co/8P28hfMf03',
     'Revolting Roundup  https://t.co/Qz8Z1t0qgj  #PeopleAreRevolting #OceanCity #EnvironmentalRacism #USC #SexualHarassment #DetroitPeaceMarch',
     'Free booklet: "Feminism as Fascism" (a critique of the #MeToo campaign - #Feminism, #Fascism, #RadicalFeminism,… https://t.co/lq0jTYOPQP',
     'It’s Time to Act ⌛ Working together, we can eliminate the shadow pandemic which is violence against women.… https://t.co/HqnJH6bJZT',
     "Bullying in our schools includes #sexualharassment and online abuse-it’s never acceptable. That's why I am supporti… https://t.co/zMWibl6NY4",
     '@BorisJohnson insists #MPRobRoberts was appropriately punished for breaching sexual misconduct rules with a 6-week… https://t.co/7kWO1Zf0GM',
     'Federal Government introduces workplace sexual harassment Bill — Respect@Work https://t.co/IpgGjixdQm #workplace… https://t.co/MXZmdp7GCn',
     'Finally \n#sexualharassment #Jamaica https://t.co/52iCr6mgiE',
     "⚠️#SECRETSERVICES-RUN #TORTURE &amp; #SLEEPDEPRIVATION DON'T CEASE❗\nTHE TURDS ARE RUNNING #SEXUALHARASSMENT &amp; INDUCING… https://t.co/uY5Uaw871z",
     'How diverse are the #riverguides that work for your rafting company? A homogeneous staff, where everyone comes form… https://t.co/McnADOifey',
     '@jovanthony @Delroychuckjm Pandering to women after the affectionate touch comment 🙄\n\nTho women disproportionately… https://t.co/ufBPXjzuYP',
     'Ugh...\nMan dem in Parliament talk sometimes like them cyaan keep them hands to demselves... cause what is this? Kmt… https://t.co/JwBLxLs5uN',
     '#SexualHarassment News:\n\nHorrific Details Of Sexual Abuse at #Michigan Largely Ignored Amid Debates Over Legacies… https://t.co/LZaJyxAn62',
     'Only a fraction of workplace retaliation, employment discrimination, and sexual harassment were filed in 2020 (over… https://t.co/Gv3HCuHSeH',
     'More on #sexualharassment https://t.co/BgOWC4amw2',
     'For women serving as active-duty service members, the atrocities of war can be exacerbated by #sexualharassment and… https://t.co/lTwldJF2oh',
     'What is #SexualHarassment? \n1. Gender harassment \n2. Unwanted sexual attention \n3. Sexual coercion… https://t.co/AcI3zV7MQ4',
     'Bjp வழக்கறிஞர் மீது பாலியல் புகார்.. | Sexual complaint against Bjp lawyer | Sexual harassment\n\n#Sexualharassment… https://t.co/GPGk1RXWOi']



### Let's find out who tweets about sexual harassment 

We can access the other items contained in a twitter tweet, such as the user's id or location 


```python
tweets = tw.Cursor(api.search, 
                           q=new_search,
                           lang="en",
                           since=date_since).items(30)

users_locs = [[tweet.user.screen_name, tweet.user.location] for tweet in tweets]
users_locs
```




    [['FeminismInIndia', 'India'],
     ['AemielGotsMail', 'AFK on the Moon'],
     ['Miaibar', 'Lima, Perú'],
     ['jessica_lee25', 'Austin, TX'],
     ['william7424', 'Australia'],
     ['HarassNoMore', 'Victoria, British Columbia'],
     ['ClassActionLaw', 'Seattle, WA'],
     ['vidyaratha', 'Guyana'],
     ['SharaThompson', '@townofaddison '],
     ['AblinLaw', 'Chicago, IL'],
     ['hrc_la', 'Los Angeles'],
     ['Monique14687419', '🇨🇰'],
     ['Monique14687419', '🇨🇰'],
     ['tomesper', 'Greater Detroit/Ann Arbor Area'],
     ['teamhimmat', ''],
     ['ItsTimeYEG', 'Edmonton, Alberta'],
     ['LFarberLaw', 'Montclair, NJ 07042'],
     ['Karah_Levy', 'Iowa, USA'],
     ['xbm34x', 'Ontario, Canada'],
     ['ThatcherLawFirm', 'Greenbelt, MD'],
     ['EnoughAlreadySK', 'Saskatchewan'],
     ['FightUnidays', 'Nottingham, London, NY, Sydney'],
     ['peoplerevolting', 'Earth'],
     ['LoaferTolboll', 'Rold Forest, Denmark'],
     ['GhosthunterDoc', ''],
     ['MariaMillerUK', 'Basingstoke, England'],
     ['Monique14687419', '🇨🇰'],
     ['HopgoodGanim', 'Brisbane | Perth'],
     ['Rolician', ''],
     ['m_t_h_MWarrior', '']]



### Create a pandas dataframe from a list of tweet data


```python
tweet_text = pd.DataFrame(data=users_locs, 
                    columns=['user', "location"])
tweet_text
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
      <th>user</th>
      <th>location</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>FeminismInIndia</td>
      <td>India</td>
    </tr>
    <tr>
      <th>1</th>
      <td>AemielGotsMail</td>
      <td>AFK on the Moon</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Miaibar</td>
      <td>Lima, Perú</td>
    </tr>
    <tr>
      <th>3</th>
      <td>jessica_lee25</td>
      <td>Austin, TX</td>
    </tr>
    <tr>
      <th>4</th>
      <td>william7424</td>
      <td>Australia</td>
    </tr>
    <tr>
      <th>5</th>
      <td>HarassNoMore</td>
      <td>Victoria, British Columbia</td>
    </tr>
    <tr>
      <th>6</th>
      <td>ClassActionLaw</td>
      <td>Seattle, WA</td>
    </tr>
    <tr>
      <th>7</th>
      <td>vidyaratha</td>
      <td>Guyana</td>
    </tr>
    <tr>
      <th>8</th>
      <td>SharaThompson</td>
      <td>@townofaddison</td>
    </tr>
    <tr>
      <th>9</th>
      <td>AblinLaw</td>
      <td>Chicago, IL</td>
    </tr>
    <tr>
      <th>10</th>
      <td>hrc_la</td>
      <td>Los Angeles</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Monique14687419</td>
      <td>🇨🇰</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Monique14687419</td>
      <td>🇨🇰</td>
    </tr>
    <tr>
      <th>13</th>
      <td>tomesper</td>
      <td>Greater Detroit/Ann Arbor Area</td>
    </tr>
    <tr>
      <th>14</th>
      <td>teamhimmat</td>
      <td></td>
    </tr>
    <tr>
      <th>15</th>
      <td>ItsTimeYEG</td>
      <td>Edmonton, Alberta</td>
    </tr>
    <tr>
      <th>16</th>
      <td>LFarberLaw</td>
      <td>Montclair, NJ 07042</td>
    </tr>
    <tr>
      <th>17</th>
      <td>Karah_Levy</td>
      <td>Iowa, USA</td>
    </tr>
    <tr>
      <th>18</th>
      <td>xbm34x</td>
      <td>Ontario, Canada</td>
    </tr>
    <tr>
      <th>19</th>
      <td>ThatcherLawFirm</td>
      <td>Greenbelt, MD</td>
    </tr>
    <tr>
      <th>20</th>
      <td>EnoughAlreadySK</td>
      <td>Saskatchewan</td>
    </tr>
    <tr>
      <th>21</th>
      <td>FightUnidays</td>
      <td>Nottingham, London, NY, Sydney</td>
    </tr>
    <tr>
      <th>22</th>
      <td>peoplerevolting</td>
      <td>Earth</td>
    </tr>
    <tr>
      <th>23</th>
      <td>LoaferTolboll</td>
      <td>Rold Forest, Denmark</td>
    </tr>
    <tr>
      <th>24</th>
      <td>GhosthunterDoc</td>
      <td></td>
    </tr>
    <tr>
      <th>25</th>
      <td>MariaMillerUK</td>
      <td>Basingstoke, England</td>
    </tr>
    <tr>
      <th>26</th>
      <td>Monique14687419</td>
      <td>🇨🇰</td>
    </tr>
    <tr>
      <th>27</th>
      <td>HopgoodGanim</td>
      <td>Brisbane | Perth</td>
    </tr>
    <tr>
      <th>28</th>
      <td>Rolician</td>
      <td></td>
    </tr>
    <tr>
      <th>29</th>
      <td>m_t_h_MWarrior</td>
      <td></td>
    </tr>
  </tbody>
</table>
</div>



### You can customize your twitter queries


```python
new_search = "sexualharassment+workplace -filter:retweets"

tweets = tw.Cursor(api.search,
                   q=new_search,
                   lang="en",
                   since='2020-07-14').items(100)

all_tweets = [tweet.text for tweet in tweets]
all_tweets[:5]
```




    ['Sony Music Australia facing a possible class action suit by former employees who were subjected to #bullying,… https://t.co/uWxs8xZmOc',
     'Prevention of Sexual Harassment and Sexual Assault in the Workplace #diversity #sexualharassment https://t.co/VY0QglKHe4 via @SlideShare',
     'Six questions you should ask about workplace bullying\n\nhttps://t.co/YUpc7u5PYU\n\n#workplaceinvestigations #hr… https://t.co/oh5iRxJm7x',
     'tags: #teamhimmat #handbook #awareness #accessibility #aid #sexualharassment #collegecampus #workplace',
     'How a positive workplace culture is an important line of defense in preventing #SexualHarassment.\n\nhttps://t.co/ypvBfGHsMW']



### Let's filter on a specific location

There are two sources of information for geocoded data in twitter: 

1. The tweet location (Available if users share their location at the time of the tweet)
2. The account location (Available if users share their home location in their public profile) 

Geographic information is provided in [LONG, LAT] order. For more information on location data in twitter see: https://developer.twitter.com/en/docs/tutorials/filtering-tweets-by-location

You can filter for the location using Twitter's operators, which are listed here: https://developer.twitter.com/en/docs/twitter-api/enterprise/rules-and-filtering/enterprise-operators#listofoperators

**There are several possibilities to filter for locations:**
* The *place* operator: This operator filters for tweets with a specific location or Twitter place ID. 
* The *place_country* operator: Here you can filter for tweets which match the ISO alpha-2 characteristics, which you can find here: http://en.wikipedia.org/wiki/ISO_3166-1_alpha-2

You can find an overview of how to filter for locations here: https://developer.twitter.com/en/docs/tutorials/filtering-tweets-by-location

#### Let's filter for a place using the twitter tweet place operator


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




    ['Sony Music Australia facing a possible class action suit by former employees who were subjected to #bullying,… https://t.co/uWxs8xZmOc',
     'Prevention of Sexual Harassment and Sexual Assault in the Workplace #diversity #sexualharassment https://t.co/VY0QglKHe4 via @SlideShare',
     'Six questions you should ask about workplace bullying\n\nhttps://t.co/YUpc7u5PYU\n\n#workplaceinvestigations #hr… https://t.co/oh5iRxJm7x',
     'tags: #teamhimmat #handbook #awareness #accessibility #aid #sexualharassment #collegecampus #workplace',
     'How a positive workplace culture is an important line of defense in preventing #SexualHarassment.\n\nhttps://t.co/ypvBfGHsMW']



#### Let's filter on a specific location and extract the location together with the text

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




    [['Sony Music Australia facing a possible class action suit by former employees who were subjected to #bullying,… https://t.co/uWxs8xZmOc',
      'Victoria, British Columbia'],
     ['Prevention of Sexual Harassment and Sexual Assault in the Workplace #diversity #sexualharassment https://t.co/VY0QglKHe4 via @SlideShare',
      'Guyana'],
     ['Six questions you should ask about workplace bullying\n\nhttps://t.co/YUpc7u5PYU\n\n#workplaceinvestigations #hr… https://t.co/oh5iRxJm7x',
      'Chicago, IL'],
     ['tags: #teamhimmat #handbook #awareness #accessibility #aid #sexualharassment #collegecampus #workplace',
      ''],
     ['How a positive workplace culture is an important line of defense in preventing #SexualHarassment.\n\nhttps://t.co/ypvBfGHsMW',
      'Edmonton, Alberta'],
     ['Federal Government introduces workplace sexual harassment Bill — Respect@Work https://t.co/IpgGjixdQm #workplace… https://t.co/MXZmdp7GCn',
      'Brisbane | Perth'],
     ['@jovanthony @Delroychuckjm Pandering to women after the affectionate touch comment 🙄\n\nTho women disproportionately… https://t.co/ufBPXjzuYP',
      ''],
     ['Only a fraction of workplace retaliation, employment discrimination, and sexual harassment were filed in 2020 (over… https://t.co/Gv3HCuHSeH',
      'New York City, NY'],
     ["This is #BhanwariDevi. She was gangraped due to her work. Her struggle led to India's law against women's… https://t.co/MkY5JwHFJV",
      'Pune, Maharashtra, India '],
     ['Seeking #Israeli companies with *open spaces* - not desks crammed in a room - but outstanding policies on having op… https://t.co/xFBXXcpdag',
      'wherever, whenever'],
     ['#Feminist #KrishnaKumari reminds @DayakarRao2019 sexist comments equates to #SexualHarassment of #women at… https://t.co/0sswfsUNeL',
      'Hyderabad, India'],
     [". @jyothsna_tdp joined #Telangana #feminists to condemn @DayakarRao2019's sexist remarks towards the woman MPDO at… https://t.co/BspyMr5PF1",
      'Hyderabad, India'],
     ['More #girls are going to school, fewer are forced into early marriage, more #women are serving in leadership positi… https://t.co/3PRZktspk4',
      'Pune, Maharashtra, India '],
     ['@taylordbaird the blackhawks don’t seem to be complaint with illinois law as it relates to sexual harassment in the… https://t.co/DIKJKxBwFr',
      ''],
     ['Had a productive engagement while leading stakeholders to research on the prevalence of workplace sexual harassment… https://t.co/wOdYK9su5D',
      'Accra, Ghana'],
     ['"Over 60% of women in the UK have experienced some form of #sexualharassment in the workplace. The seriousness of t… https://t.co/K8jxYkCFLk',
      'UK'],
     ['#Sexualharassment is prevalent yet preventable. In Australia, almost 4/5 cases of #workplace sexual harassment are… https://t.co/A41qr0934S',
      'Australia'],
     ['What will you do if you come across an incident of #sexualharassment in your workplace❓😦Will you just stand and wat… https://t.co/Ky76caeF8f',
      'USA'],
     ['What does it mean to ‘seriously demean’ women in the workplace? It’s not a trick question, explains @Virginia_Hauss… https://t.co/bQ82NK2gHK',
      'Australia'],
     ['What Cosby’s Release Means For Victims Of Workplace Sexual Harassment\n\n #criminaldefense #justice #law #lawyer… https://t.co/xqi0aTygMm',
      ''],
     ['There were over 400 reports of sexual harassment in SK workplaces in the past 2 years (and that’s just the ones who… https://t.co/yRuCYeXlCO',
      'Saskatchewan'],
     ['Quoted 👇🏼\n important article on #Violence and #sexualharassment at work  by @RoopaDhatt @Inaguruguru @TorresOyarzo… https://t.co/bX5ehR2yOL',
      'New Delhi, India'],
     ['Did you know that #SexualHarassment in the #Workplace takes many forms? If you’re not sure that what you experience… https://t.co/uuiSNakbzF',
      'Sarnia, Ontario'],
     ['Shock. PTSD. Rage. Fear. People react in different ways to workplace sexual harassment. No one deserves to feel thi… https://t.co/vJh4m9QfNo',
      'Saskatchewan'],
     ["The International Labour Organisation's Violence and Harassment Convention can help empower working women in myriad… https://t.co/AnanjGmtJi",
      'Chennai, India'],
     ['Adv. @shivangi_prasad  – Corporate Lawyer, External Member &amp; Trainer, Head – Legal &amp; Compliance, Partner… https://t.co/15wrAjQpcQ',
      'India'],
     ['@CollectiveShout So - in line with allegations - Brett Blundy + co made millions by exploiting young women, and hav… https://t.co/s8fFfhyVoD',
      ''],
     ['"As long as companies need no good reason to fire people, Lander says, employees will be vulnerable to #wagetheft,… https://t.co/LDWNPvqqX8',
      'California']]



#### Let's filter for a twitter tweet's location using the place_country operator

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




    ['@MarshtheRandy @baldhalb9 Es tut wirklich nichts rechtfertigen, da sollte man auch nicht rechtfertigen\nFür Sexuelle… https://t.co/LOGLldWVi4',
     'Mädchen angesprochen und sich vor ihnen entblößt #StuttgartSued #SexuelleBelaestigung https://t.co/OMtfuVgbXh',
     'Frau in S-Bahn sexuell belästigt – Zeugen gesucht #Stuttgart #SexuelleBelaestigung https://t.co/Ez8N1tPdCa',
     'Frau in S-Bahn sexuell belästigt – Zeugen gesucht #Stuttgart #SexuelleBelaestigung https://t.co/w1EiI4Ib6Y',
     'Was soll das denn für eine bekloppte Argumentation sein?\n\n#Belästigung #DickPic #sexuelleBelästigung #Misogynie https://t.co/ULAEXtDZDO']




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




    [['@MarshtheRandy @baldhalb9 Es tut wirklich nichts rechtfertigen, da sollte man auch nicht rechtfertigen\nFür Sexuelle… https://t.co/LOGLldWVi4',
      '',
      None],
     ['Mädchen angesprochen und sich vor ihnen entblößt #StuttgartSued #SexuelleBelaestigung https://t.co/OMtfuVgbXh',
      'Stuttgart, Germany',
      None],
     ['Frau in S-Bahn sexuell belästigt – Zeugen gesucht #Stuttgart #SexuelleBelaestigung https://t.co/Ez8N1tPdCa',
      'Stuttgart, Germany',
      None],
     ['Frau in S-Bahn sexuell belästigt – Zeugen gesucht #Stuttgart #SexuelleBelaestigung https://t.co/w1EiI4Ib6Y',
      'Stuttgart, Germany',
      None],
     ['Was soll das denn für eine bekloppte Argumentation sein?\n\n#Belästigung #DickPic #sexuelleBelästigung #Misogynie https://t.co/ULAEXtDZDO',
      'Germany',
      None],
     ['Sexuelle Belästigung in Straubing durch Radfahrer\n.\n#SexuelleBelästigung #Straubing https://t.co/ScbolFvQzn',
      'Straubing',
      None],
     ['Mein lieber Scholli 🤜 🙅 🤛 🙅 👊 https://t.co/6vlYw669IF via @FacebookWatch \n#sexuellebelästigung #frauenrechte… https://t.co/SM4RWEvwZV',
      '',
      None],
     ['Wow das erste Mal seit Monaten  das ich sexuell belästigt worden bin im Internet....Neuer Rekord.… https://t.co/ja3XJw960C',
      '',
      None],
     ['Anpfiff zum #Finale! \nAm Sonntag entscheidet sich, wer die #EURO2020 gewinnt. Leider erleben Frauen auf den Zuschau… https://t.co/aHCoGOARmQ',
      '',
      None],
     ['CW #SexuelleBelästigung #Genitalien \n\nWenn man ungewollt ein #dickpic bekommt, kann man das auf dieser Seite melden… https://t.co/fHAZ3Yzy8c',
      ' 🇬🇧 ze/zir; 🇩🇪 xie|sier|er',
      None],
     ['Vorfall in Linz: Zwei Mädchen in Freibad sexuell belästigt, junges Alter der Täter ist erschreckend… https://t.co/ppMKCg64eD',
      '',
      None],
     ['@wienerlinien @LPDWien! Wer ohne Ticket fährt, bekommt ein saftiges Bußgeld, wer auf den Rücksitzen masturbiert, st… https://t.co/cwWowKgfYo',
      'Vienna, Austria',
      None],
     ['Als ich 12 Jahre alt war, hat mich meine Mutter gefragt, was ich von #Frauenemanzipation halte. #Feminismus sei für… https://t.co/lkErdQXjr5',
      '',
      None],
     ['Frau an Fußgängerampel sexuell belästigt\n\n #Polizeimeldung #Zeugenaufruf #Pressemitteilung #SexuelleBelästigung… https://t.co/ixJKMVPhXj',
      'Aulendorf',
      None],
     ['Rechtsexperte Dr. Nico Herold zu #Whistleblowing: Weit über 80 Prozent der Fälle haben intern angefangen, Missstand… https://t.co/nMVtnFnpz1',
      'dbb forum, Berlin',
      None]]




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




    ['@MarshtheRandy @baldhalb9 Es tut wirklich nichts rechtfertigen, da sollte man auch nicht rechtfertigen\nFür Sexuelle… https://t.co/LOGLldWVi4',
     'Mädchen angesprochen und sich vor ihnen entblößt #StuttgartSued #SexuelleBelaestigung https://t.co/OMtfuVgbXh',
     'Frau in S-Bahn sexuell belästigt – Zeugen gesucht #Stuttgart #SexuelleBelaestigung https://t.co/Ez8N1tPdCa',
     'Frau in S-Bahn sexuell belästigt – Zeugen gesucht #Stuttgart #SexuelleBelaestigung https://t.co/w1EiI4Ib6Y',
     'Was soll das denn für eine bekloppte Argumentation sein?\n\n#Belästigung #DickPic #sexuelleBelästigung #Misogynie https://t.co/ULAEXtDZDO',
     'Sexuelle Belästigung in Straubing durch Radfahrer\n.\n#SexuelleBelästigung #Straubing https://t.co/ScbolFvQzn',
     'Mein lieber Scholli 🤜 🙅 🤛 🙅 👊 https://t.co/6vlYw669IF via @FacebookWatch \n#sexuellebelästigung #frauenrechte… https://t.co/SM4RWEvwZV',
     'Wow das erste Mal seit Monaten  das ich sexuell belästigt worden bin im Internet....Neuer Rekord.… https://t.co/ja3XJw960C',
     'Anpfiff zum #Finale! \nAm Sonntag entscheidet sich, wer die #EURO2020 gewinnt. Leider erleben Frauen auf den Zuschau… https://t.co/aHCoGOARmQ',
     'CW #SexuelleBelästigung #Genitalien \n\nWenn man ungewollt ein #dickpic bekommt, kann man das auf dieser Seite melden… https://t.co/fHAZ3Yzy8c',
     'Vorfall in Linz: Zwei Mädchen in Freibad sexuell belästigt, junges Alter der Täter ist erschreckend… https://t.co/ppMKCg64eD',
     '@wienerlinien @LPDWien! Wer ohne Ticket fährt, bekommt ein saftiges Bußgeld, wer auf den Rücksitzen masturbiert, st… https://t.co/cwWowKgfYo',
     'Als ich 12 Jahre alt war, hat mich meine Mutter gefragt, was ich von #Frauenemanzipation halte. #Feminismus sei für… https://t.co/lkErdQXjr5']



#### Another way to filter for a twitter tweet's location is using coordinates 

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




    [[None, False],
     [None, True],
     [None, True],
     [None, True],
     [None, True],
     [None, True],
     [None, False],
     [None, False],
     [None, False],
     [None, False],
     [None, False],
     [None, False],
     [None, True]]



### Let's create a pandas dataframe from our tweets


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
      <th>text</th>
      <th>user loc</th>
      <th>tweet loc</th>
      <th>tweet coordinates</th>
      <th>tweet date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>#Spanien und seine #Folter und #SexuelleBeläst...</td>
      <td></td>
      <td>None</td>
      <td>None</td>
      <td>2021-07-16 05:08:03</td>
    </tr>
    <tr>
      <th>1</th>
      <td>@MarshtheRandy @baldhalb9 Es tut wirklich nich...</td>
      <td></td>
      <td>None</td>
      <td>None</td>
      <td>2021-07-14 14:08:35</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Mädchen angesprochen und sich vor ihnen entblö...</td>
      <td>Stuttgart, Germany</td>
      <td>None</td>
      <td>None</td>
      <td>2021-07-12 13:38:00</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Frau in S-Bahn sexuell belästigt – Zeugen gesu...</td>
      <td>Stuttgart, Germany</td>
      <td>None</td>
      <td>None</td>
      <td>2021-07-12 13:26:54</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Frau in S-Bahn sexuell belästigt – Zeugen gesu...</td>
      <td>Stuttgart, Germany</td>
      <td>None</td>
      <td>None</td>
      <td>2021-07-12 13:26:53</td>
    </tr>
  </tbody>
</table>
</div>




```python
tweet_df['user loc'].hist();
```  


There are several problems when using location data in Twitter. This blogpost summarizes it quite well: https://towardsdatascience.com/twitter-location-analysis-c488c967a41f
