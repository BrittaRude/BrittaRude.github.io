# How to use GeoPy

Sometimes you might want to transform or unify a bunch of addresses. For example, you might have a list of addresses in different formats and might want to match them to official administrative geographic boarders. To do this, you can take advantage of [GeoPy](https://geopy.readthedocs.io/en/stable/). GeoPy is a Python Client for geocoding web services and works via API querys. GeoPy allows you to connect to a large number of geocoding webs. For a full overview see the documentation. One example is the [Nominatim](https://nominatim.org/) web service, which we will make use of in this tutorial. We will closely follow [this tutorial](https://www.youtube.com/watch?v=gJMHbW3MK2w). 


```python
!pip install geopy
```


```python
import pandas as pd
from geopy.geocoders import Nominatim
```

## Import data 


```python
data = pd.read_pickle(r"C:\Users\Rude\Documents\Jupyter Notebooks\finaltweets_2014_P1_M7.pkl")
data.shape
```




    (215956, 49)




```python
data.columns
```




    Index(['tweet_created_at', 'lang', 'attachments', 'possibly_sensitive',
           'source', 'text', 'tweet_id', 'conversation_id', 'author_id',
           'reply_settings', '__twarc', 'referenced_tweets', 'in_reply_to_user_id',
           'in_reply_to_user', 'withheld', 'coordinates', 'place_id', 'geo',
           'name', 'geo_id', 'full_name', 'place_type', 'country', 'country_code',
           'description', 'verified', 'protected', 'created_at', 'username',
           'pinned_tweet_id', 'url', 'name', 'entities', 'location',
           'profile_image_url', 'withheld', 'followers_count', 'following_count',
           'tweet_count', 'listed_count', 'retweet_count', 'reply_count',
           'like_count', 'quote_count', 'hashtags', 'urls', 'mentions',
           'annotations', 'cashtags'],
          dtype='object')




```python
data[['tweet_id', 'username', 'location']].head()
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
      <th>tweet_id</th>
      <th>username</th>
      <th>location</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>489868328748720128</td>
      <td>GbxgbxAde</td>
      <td>Zion</td>
    </tr>
    <tr>
      <th>1</th>
      <td>489868314748133376</td>
      <td>MichalaRudman</td>
      <td>South Wales</td>
    </tr>
    <tr>
      <th>2</th>
      <td>489868314068262912</td>
      <td>dougmcbot</td>
      <td>North Texas</td>
    </tr>
    <tr>
      <th>3</th>
      <td>489868301581836288</td>
      <td>DebiJackson50</td>
      <td>Cincinnati, OH</td>
    </tr>
    <tr>
      <th>4</th>
      <td>489868289221595136</td>
      <td>spasskultur</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>



## Prepare our query 


```python
data['query'] = data["location"]
data = data[['tweet_id', 'username', 'location', 'query']]
data.head()
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
      <th>tweet_id</th>
      <th>username</th>
      <th>location</th>
      <th>query</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>489868328748720128</td>
      <td>GbxgbxAde</td>
      <td>Zion</td>
      <td>Zion</td>
    </tr>
    <tr>
      <th>1</th>
      <td>489868314748133376</td>
      <td>MichalaRudman</td>
      <td>South Wales</td>
      <td>South Wales</td>
    </tr>
    <tr>
      <th>2</th>
      <td>489868314068262912</td>
      <td>dougmcbot</td>
      <td>North Texas</td>
      <td>North Texas</td>
    </tr>
    <tr>
      <th>3</th>
      <td>489868301581836288</td>
      <td>DebiJackson50</td>
      <td>Cincinnati, OH</td>
      <td>Cincinnati, OH</td>
    </tr>
    <tr>
      <th>4</th>
      <td>489868289221595136</td>
      <td>spasskultur</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>



Geopy will give us the specific address, latitude and longitude. 


```python
data["location_lat"]=""
data["location_long"]=""
data["location_address"]=""
```


```python
data.head()
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
      <th>tweet_id</th>
      <th>username</th>
      <th>location</th>
      <th>query</th>
      <th>location_lat</th>
      <th>location_long</th>
      <th>location_address</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>489868328748720128</td>
      <td>GbxgbxAde</td>
      <td>Zion</td>
      <td>GbxgbxAde Zion</td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>1</th>
      <td>489868314748133376</td>
      <td>MichalaRudman</td>
      <td>South Wales</td>
      <td>MichalaRudman South Wales</td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>2</th>
      <td>489868314068262912</td>
      <td>dougmcbot</td>
      <td>North Texas</td>
      <td>dougmcbot North Texas</td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>3</th>
      <td>489868301581836288</td>
      <td>DebiJackson50</td>
      <td>Cincinnati, OH</td>
      <td>DebiJackson50 Cincinnati, OH</td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>4</th>
      <td>489868289221595136</td>
      <td>spasskultur</td>
      <td>NaN</td>
      <td>NaN</td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
  </tbody>
</table>
</div>



## Use GeoPy to fetch the geocode data 


```python
df = data.iloc[:100]
df.shape
```




    (100, 4)




```python
geolocator = Nominatim(user_agent="myApp")

for i in df.index:
    try:
        #tries fetch address from geopy
        location = geolocator.geocode(df['query'][i])
        
        #append lat/long to column using dataframe location
        df.loc[i,'location_lat'] = location.latitude
        df.loc[i,'location_long'] = location.longitude
        df.loc[i,'location_address'] = location.address
    except:
        #catches exception for the case where no value is returned
        #appends null value to column
        df.loc[i,'location_lat'] = ""
        df.loc[i,'location_long'] = ""
        df.loc[i,'location_address'] = ""

#print first rows as sample
df.head()
```


```python
pd.set_option("display.max_colwidth", None)
pd.set_option("display.max_rows", None)
```


```python
df.head()
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
      <th>tweet_id</th>
      <th>username</th>
      <th>location</th>
      <th>query</th>
      <th>location_lat</th>
      <th>location_long</th>
      <th>location_address</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>489868328748720128</td>
      <td>GbxgbxAde</td>
      <td>Zion</td>
      <td>Zion</td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>1</th>
      <td>489868314748133376</td>
      <td>MichalaRudman</td>
      <td>South Wales</td>
      <td>South Wales</td>
      <td>42.708949</td>
      <td>-78.57808</td>
      <td>South Wales, Town of Wales, Erie County, New York, 14139, United States</td>
    </tr>
    <tr>
      <th>2</th>
      <td>489868314068262912</td>
      <td>dougmcbot</td>
      <td>North Texas</td>
      <td>North Texas</td>
      <td>36.197937</td>
      <td>-76.009923</td>
      <td>Texas, Camden County, North Carolina, 2, United States</td>
    </tr>
    <tr>
      <th>3</th>
      <td>489868301581836288</td>
      <td>DebiJackson50</td>
      <td>Cincinnati, OH</td>
      <td>Cincinnati, OH</td>
      <td>39.101454</td>
      <td>-84.51246</td>
      <td>Cincinnati, Hamilton County, Ohio, United States</td>
    </tr>
    <tr>
      <th>4</th>
      <td>489868289221595136</td>
      <td>spasskultur</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>46.314475</td>
      <td>11.048029</td>
      <td>Nanno, Ville d'Anaunia, Comunità della Val di Non, Provincia di Trento, Trentino-Alto Adige/Südtirol, 38012, Italia</td>
    </tr>
  </tbody>
</table>
</div>




```python

```

Now, go ahead and extract the necessary geo information from your dataset! :-)
