---
layout: post
title: "Web Scraping with Python"
date: 2021-07-11
myexcerpt: The internet has created large amounts of unstructured data. Most of it we can access through websites. How can we create structured data accessing information published on websites? Python is a great way to do that. Have a look at some examples, accessing the html source code underlying websites through Python. 
image: /images/webscraping.jpg
---


## How can we extract information on websites? 

<img src="/images/webscraping.jpg" alt="database" style="float:left;margin: 2px 2px 2px 2px;max-width:15%;"/>

The internet has created large amounts of unstructured data. Most of it we can access through websites. How can we create structured data accessing information published on websites? Python is a great way to do that. Have a look at some examples, accessing the html source code underlying websites through Python.

This tutorial follows this tutorial on Web Scraping with Python by [Piyush Trivedi](https://www.youtube.com/channel/UCpYUJY4zvLQ_wmmCVVJ9Ajw) [https://www.youtube.com/watch?v=I-ITLsE8SgM](https://www.youtube.com/watch?v=I-ITLsE8SgM) as well as this one [https://www.youtube.com/watch?v=ng2o98k983k](https://www.youtube.com/watch?v=ng2o98k983k) developed by [Corey Schafer](https://www.youtube.com/channel/UCCezIgC97PvUuR4_gbFUs5g). [*Foto by Diego PH*]

### First, let's import our packages!

```python
import requests
import bs4
from bs4 import BeautifulSoup

import csv
```


```python
url = 'https://economictimes.indiatimes.com/markets/stocks/news'
```

### Let's get a user agent

We need to find our user agent. Therefore, type "find user agent" in mozilla firefox. 


```python
headers={
    'User-Agent':'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.114 Safari/537.36 Edg/91.0.864.59'
}
```

### Let's create a request

Request will return a response object. We need to get the source text through headers or through requests.get().text in the end.


```python
r= requests.get(url, {'headers':headers})
```

### Creating our BeautifulSoup object

Let's create our BeautifulSoup object. All we need for that is our html file as well as a parser. In our case, we use the html parser. 


```python
soup = bs4.BeautifulSoup(r.text,'html.parser')
```
### Html files and Python

Let's now have a look at our html file. There are different methods to print out our html file. One is print(), and the other one is find_all(). 


```python
#Do not show to save space
#print(soup)
#print(soup.prettify())
```


```python
#Do not show to save space
#soup.find_all()
```

### Accessing information and the find() command

Next we will access some information from out html fle. For this, we can use some helpful options of our BeautifulSoup package. 


```python
#The below will give us the first title in our html file
match = soup.title
print(match)
```

    <title>Stocks in News Today - Latest News on Stocks, Stock in News | The Economic Times</title>
    


```python
#Now we just want to get the text, without the headings
match = soup.title.text
print(match)
```

    Stocks in News Today - Latest News on Stocks, Stock in News | The Economic Times
    


```python
#Another example - using the div class and not the title class
match = soup.div
print(match)
```

    <div data-adsslot="ET_Markets/ET_Markets_Stocks/ET_ROS_OP_MKT_STK_Innov1" data-loc="All" data-oop="1" data-size="" id="div-gpt-ad-1386445009452-0-oop1"></div>
    


```python
#Another way of doing the same is using the find() function
match = soup.find('div')
print(match)
```

    <div data-adsslot="ET_Markets/ET_Markets_Stocks/ET_ROS_OP_MKT_STK_Innov1" data-loc="All" data-oop="1" data-size="" id="div-gpt-ad-1386445009452-0-oop1"></div>
    

### Filtering for information 

What if we do not want the first object (e.g. the first div in our html file), but a specific div? We can then filter through our find function. 

*Remember to always indicate the class_ with an underscore!*


```python
#Finding the first eachStory class in our html file
article = soup.find('div', class_='eachStory')
print(article)
```

    <div class="eachStory" itemprop="itemListElement" itemscope="itemscope" itemtype="http://schema.org/ListItem"><meta content="1" itemprop="position"/><a href="/markets/stocks/news/didi-cybersecurity-probe-blindsides-shareholders-days-after-debut/articleshow/84164026.cms"><span class="imgContainer"><img alt="Didi cybersecurity probe blindsides shareholders days after debut" class="lazy" data-original="https://img.etimg.com/thumb/msid-84164026,width-160,height-120,imgsize-153018/didi-cybersecurity-probe-blindsides-shareholders-days-after-debut.jpg" height="120" src="https://img.etimg.com/thumb/msid-42031747,width-160,height-120/etlogo.jpg" width="160"/></span></a><h3><a data-orefid="p2146843_1" href="/markets/stocks/news/didi-cybersecurity-probe-blindsides-shareholders-days-after-debut/articleshow/84164026.cms" itemprop="url"><meta content="Didi cybersecurity probe blindsides shareholders days after debut" itemprop="name"/>Didi cybersecurity probe blindsides shareholders days after debut</a></h3><time class="date-format" data-time="Jul 6, 2021, 10:42 AM IST">Jul 6, 2021, 10:42 AM IST</time><p>While Didi's initial public offering (IPO) prospectus did mention some of the regulatory risks to its operations, there was no indication that the Cyberspace Administration of China (CAC) would begin investigating the company and ban it from accepting new users during the review.</p></div>
    


```python
#Extract the first eachStory's headline
headline = article.h3.a.text
print(headline)
```

    Didi cybersecurity probe blindsides shareholders days after debut
    


```python
#Extract summary of the first eachStory
summary = article.p.text
print(summary)
```

    While Didi's initial public offering (IPO) prospectus did mention some of the regulatory risks to its operations, there was no indication that the Cyberspace Administration of China (CAC) would begin investigating the company and ban it from accepting new users during the review.
    

### Looping over all elements in our html through find_all()

We can also loop over several articles and extract information from all through a simple loop()


```python
#Loop over different eachStory's and extract headline and summary
for article in soup.find_all('div', class_='eachStory'):
    
    headline = article.h3.a.text
    print(headline)

    summary = article.p.text
    print(summary)
    
    print()
```

    Didi cybersecurity probe blindsides shareholders days after debut
    While Didi's initial public offering (IPO) prospectus did mention some of the regulatory risks to its operations, there was no indication that the Cyberspace Administration of China (CAC) would begin investigating the company and ban it from accepting new users during the review.
    
    AU Small Finance Bank surges 9% after Q1 update
    The numbers gave a relief to the investors who were expecting a worse impact of the Second Covid Wave on the small finance lenders. The restrictions on mobility and business during the second wave were less stringent than those during the nationwide lockdown. The gross advances showed a growth of 31 per cent on year-on-year basis (YoY) to Rs 34,688 crore in the quarter ended on June 30, 2021 from Rs 26,534 crore in the June 2020 quarter. 
    
    Sell these 6 stocks if you want to protect your hard-earned money
    Dalal Street has seen a lot of buying in the last 15 months. Almost all benchmark indices have hit fresh highs and many stocks did the same. But, as valuations have soared, many money managers including S Naren of ICICI Prudential AMC, feel it is time to accumulate cash. Here are some stocks that analysts believe you should sell right now.
    
    Adviser Glass Lewis flags risks from Adani 'unconscionable conduct'
    Adani Enterprises' Australian unit, Bravus Mining & Resources, has been involved in a controversial project to develop the Carmichael mine in Queensland to sell thermal coal into India, despite concerns about global warming.
    
    Gold price today: Yellow metal firms, silver tops Rs 70,000
    Gold futures on MCX were up 0.33 per cent or Rs 156 at Rs 47,455 per 10 grams. Silver futures gained 0.35 per cent or Rs 243 to Rs 70,282 per kg.
    
    Sensex rises 30 points, Nifty nears 15,850; Force Motors climbs 5%, Bajaj Hind 4%
    Domestic equity benchmarks started Tuesday's session on a flat note amid mild buying interest seen in private bank stocks.The S&P BSE Sensex index rose as much as 44 points to touch 52,923.75 in the first few minutes of trade, and the broader NSE Nifty50 benchmark climbed to as high as 15,857.30, up 23 points from its previous close.
    
    Sensex, Nifty gain on firm global cues; banks, auto see buying interest
    Many global market experts are warning of bubbles in many asset classes, most prominently in equities. Equities, globally, are over-valued by all metrics but even bears are still invested, said an analyst.
    
    D-Street’s mega puzzle: Why is analysts’ consensus ‘buy’ for 2021 not performing
    The stock is up just 3 per cent year to date compared with a nearly 11 per cent rise in the Sensex. Its peers have also underperformed. Vodafone Idea is down 13 per cent while Jio-operator Reliance Industries is up 8 per cent for the year.
    
    Buy or Sell: Stock ideas by experts for July 06, 2021
    ET Now spoke to various experts and here's what they have to recommend for today's trading session. Kunal Bothra has a buy/sell recommendation on Maruti and Axis Bank while Nooresh Merani has recommended SBI and NTPC for today's trading session. 
    
    Taper Tantrum nightmares won’t haunt emerging markets in Asia
    Policy makers and investors also see a different story in emerging Asia, where South Korea is the lone central bank expected to hike as early as this year -- and more for growth reasons around normalization, rather than inflation. During the first half of the year, 13 Asia-Pacific central banks held interest rates steady, the only exception being a cut in February by Indonesia.
    
    Asian markets opens higher, but China tech worries weigh on Hong Kong
    MSCI's broadest index of Asia-Pacific shares outside Japan was up 0.05%. In Hong Kong, the Hang Seng Index was down 0.7% while China's CSI300 was off by nearly 0.3%.
    
    Stocks in focus: SH Kelkar & Co Ltd, Maruti Suzuki, NMDC, FMCG stocks, Tech Mahindra, Tata Motors and more
    Nifty futures on the Singapore Exchange traded 37.5 points, or 0.24 per cent higher at 15,826, signaling that Dalal Street was headed for a negative start on Tuesday. Here are a dozen stocks which may buzz the most in today's trade 
    
    SGX Nifty down 20 points; here's what changed for market while you were sleeping
    Asian stocks opened mixed on Tuesday as investors sought fresh cues while keeping an eye on rising virus cases. Investors are waiting for the US market to reopen later Tuesday after the Independence Day holiday. MSCI's broadest index of Asia-Pacific shares outside Japan was down by 0.08 per cent.
    
    Banks can pay less on matured bulk FDs: RBI
    Given the surplus liquidity in the system, short-term interest rates are very low, and banks offer a risk-free return on idle money. For businesses that have a cash stash running into crores, a 3-4% return (which is what most savings deposits provide), is a decent yield for the short term. The move by the RBI is expected to force businesses to make use of their resources more productively or deploy them in longer-term deposits.
    
    Stocks in the news: FMCG stocks, Tech Mahindra, Tata Motors, NTPC and NMDC
    The banking stocks will be on the focus on traders after June quarter update. Similarly, FMCG stocks too, may attract investors due to high growth in sales
    
    D-Mart vs Info Edge in tug of war to become next Nifty50 member
    According to brokerage firm ICICI Securities, there are two candidates to be included in the next reconstitution of the premier stock index in the country in October. On one corner is the crown jewel of Radhakishan Damani’s multi-billion dollar empire, Avenue Supermarts. On the other end, is Info Edge India representing the new technology age. All stocks included in the Nifty50 must also be part of the derivatives segment, and the D-Mart stock is yet to be included  there.
    
    

### Saving the information to a CSV file 

We will next save our informtion in a CSV file. 


```python
#w writes to the csv file 
csv_file = open('cms_scrape.csv', 'w')

#Define function writing to csv file 
csv_writer = csv.writer(csv_file)

#Define columnnames
csv_writer.writerow(['headline', 'summary'])
```

Now we add the csv_writer to our loop to fill in the information into the created csv file 


```python
#Loop over different eachStory's and extract headline and summary
for article in soup.find_all('div', class_='eachStory'):
    
    headline = article.h3.a.text
    print(headline)

    summary = article.p.text
    print(summary)
    
    print()
    
    csv_writer.writerow([headline, summary])
    
csv_file.close()
```

    Didi cybersecurity probe blindsides shareholders days after debut
    While Didi's initial public offering (IPO) prospectus did mention some of the regulatory risks to its operations, there was no indication that the Cyberspace Administration of China (CAC) would begin investigating the company and ban it from accepting new users during the review.
    
    AU Small Finance Bank surges 9% after Q1 update
    The numbers gave a relief to the investors who were expecting a worse impact of the Second Covid Wave on the small finance lenders. The restrictions on mobility and business during the second wave were less stringent than those during the nationwide lockdown. The gross advances showed a growth of 31 per cent on year-on-year basis (YoY) to Rs 34,688 crore in the quarter ended on June 30, 2021 from Rs 26,534 crore in the June 2020 quarter. 
    
    Sell these 6 stocks if you want to protect your hard-earned money
    Dalal Street has seen a lot of buying in the last 15 months. Almost all benchmark indices have hit fresh highs and many stocks did the same. But, as valuations have soared, many money managers including S Naren of ICICI Prudential AMC, feel it is time to accumulate cash. Here are some stocks that analysts believe you should sell right now.
    
    Adviser Glass Lewis flags risks from Adani 'unconscionable conduct'
    Adani Enterprises' Australian unit, Bravus Mining & Resources, has been involved in a controversial project to develop the Carmichael mine in Queensland to sell thermal coal into India, despite concerns about global warming.
    
    Gold price today: Yellow metal firms, silver tops Rs 70,000
    Gold futures on MCX were up 0.33 per cent or Rs 156 at Rs 47,455 per 10 grams. Silver futures gained 0.35 per cent or Rs 243 to Rs 70,282 per kg.
    
    Sensex rises 30 points, Nifty nears 15,850; Force Motors climbs 5%, Bajaj Hind 4%
    Domestic equity benchmarks started Tuesday's session on a flat note amid mild buying interest seen in private bank stocks.The S&P BSE Sensex index rose as much as 44 points to touch 52,923.75 in the first few minutes of trade, and the broader NSE Nifty50 benchmark climbed to as high as 15,857.30, up 23 points from its previous close.
    
    Sensex, Nifty gain on firm global cues; banks, auto see buying interest
    Many global market experts are warning of bubbles in many asset classes, most prominently in equities. Equities, globally, are over-valued by all metrics but even bears are still invested, said an analyst.
    
    D-Street’s mega puzzle: Why is analysts’ consensus ‘buy’ for 2021 not performing
    The stock is up just 3 per cent year to date compared with a nearly 11 per cent rise in the Sensex. Its peers have also underperformed. Vodafone Idea is down 13 per cent while Jio-operator Reliance Industries is up 8 per cent for the year.
    
    Buy or Sell: Stock ideas by experts for July 06, 2021
    ET Now spoke to various experts and here's what they have to recommend for today's trading session. Kunal Bothra has a buy/sell recommendation on Maruti and Axis Bank while Nooresh Merani has recommended SBI and NTPC for today's trading session. 
    
    Taper Tantrum nightmares won’t haunt emerging markets in Asia
    Policy makers and investors also see a different story in emerging Asia, where South Korea is the lone central bank expected to hike as early as this year -- and more for growth reasons around normalization, rather than inflation. During the first half of the year, 13 Asia-Pacific central banks held interest rates steady, the only exception being a cut in February by Indonesia.
    
    Asian markets opens higher, but China tech worries weigh on Hong Kong
    MSCI's broadest index of Asia-Pacific shares outside Japan was up 0.05%. In Hong Kong, the Hang Seng Index was down 0.7% while China's CSI300 was off by nearly 0.3%.
    
    Stocks in focus: SH Kelkar & Co Ltd, Maruti Suzuki, NMDC, FMCG stocks, Tech Mahindra, Tata Motors and more
    Nifty futures on the Singapore Exchange traded 37.5 points, or 0.24 per cent higher at 15,826, signaling that Dalal Street was headed for a negative start on Tuesday. Here are a dozen stocks which may buzz the most in today's trade 
    
    SGX Nifty down 20 points; here's what changed for market while you were sleeping
    Asian stocks opened mixed on Tuesday as investors sought fresh cues while keeping an eye on rising virus cases. Investors are waiting for the US market to reopen later Tuesday after the Independence Day holiday. MSCI's broadest index of Asia-Pacific shares outside Japan was down by 0.08 per cent.
    
    Banks can pay less on matured bulk FDs: RBI
    Given the surplus liquidity in the system, short-term interest rates are very low, and banks offer a risk-free return on idle money. For businesses that have a cash stash running into crores, a 3-4% return (which is what most savings deposits provide), is a decent yield for the short term. The move by the RBI is expected to force businesses to make use of their resources more productively or deploy them in longer-term deposits.
    
    Stocks in the news: FMCG stocks, Tech Mahindra, Tata Motors, NTPC and NMDC
    The banking stocks will be on the focus on traders after June quarter update. Similarly, FMCG stocks too, may attract investors due to high growth in sales
    
    D-Mart vs Info Edge in tug of war to become next Nifty50 member
    According to brokerage firm ICICI Securities, there are two candidates to be included in the next reconstitution of the premier stock index in the country in October. On one corner is the crown jewel of Radhakishan Damani’s multi-billion dollar empire, Avenue Supermarts. On the other end, is Info Edge India representing the new technology age. All stocks included in the Nifty50 must also be part of the derivatives segment, and the D-Mart stock is yet to be included  there.
    
    

### Get information from embedded html code

Html code can be messy and sometimes it might not be so straightforward to extract information. We will therefore now have a look at how to extract information from a youtube link. 


```python
url = 'https://coreyms.com'
```


```python
r= requests.get(url, {'headers':headers})
```


```python
soup = bs4.BeautifulSoup(r.text,'html.parser')
```


```python
#Do not show to save space
#print(soup.prettify)
```


```python
# Get last published article
article = soup.find('article')
print(article)
```

    <article class="post-1670 post type-post status-publish format-standard has-post-thumbnail category-development category-python tag-gzip tag-shutil tag-zip tag-zipfile entry" itemscope="" itemtype="https://schema.org/CreativeWork"><header class="entry-header"><h2 class="entry-title" itemprop="headline"><a class="entry-title-link" href="https://coreyms.com/development/python/python-tutorial-zip-files-creating-and-extracting-zip-archives" rel="bookmark">Python Tutorial: Zip Files – Creating and Extracting Zip Archives</a></h2>
    <p class="entry-meta"><time class="entry-time" datetime="2019-11-19T13:02:37-05:00" itemprop="datePublished">November 19, 2019</time> by <span class="entry-author" itemprop="author" itemscope="" itemtype="https://schema.org/Person"><a class="entry-author-link" href="https://coreyms.com/author/coreymschafer" itemprop="url" rel="author"><span class="entry-author-name" itemprop="name">Corey Schafer</span></a></span> <span class="entry-comments-link"><a href="https://coreyms.com/development/python/python-tutorial-zip-files-creating-and-extracting-zip-archives#respond"><span class="dsq-postid" data-dsqidentifier="1670 http://coreyms.com/?p=1670">Leave a Comment</span></a></span> </p></header><div class="entry-content" itemprop="text">
    <p>In this video, we will be learning how to create and extract zip archives. We will start by using the zipfile module, and then we will see how to do this using the shutil module. We will learn how to do this with single files and directories, as well as learning how to use gzip as well. Let’s get started…<br/></p>
    <span class="embed-youtube" style="text-align:center; display: block;"><iframe allowfullscreen="true" class="youtube-player" height="360" sandbox="allow-scripts allow-same-origin allow-popups allow-presentation" src="https://www.youtube.com/embed/z0gguhEmWiY?version=3&amp;rel=1&amp;showsearch=0&amp;showinfo=1&amp;iv_load_policy=1&amp;fs=1&amp;hl=en-US&amp;autohide=2&amp;wmode=transparent" style="border:0;" width="640"></iframe></span>
    </div><footer class="entry-footer"><p class="entry-meta"><span class="entry-categories">Filed Under: <a href="https://coreyms.com/category/development" rel="category tag">Development</a>, <a href="https://coreyms.com/category/development/python" rel="category tag">Python</a></span> <span class="entry-tags">Tagged With: <a href="https://coreyms.com/tag/gzip" rel="tag">gzip</a>, <a href="https://coreyms.com/tag/shutil" rel="tag">shutil</a>, <a href="https://coreyms.com/tag/zip" rel="tag">zip</a>, <a href="https://coreyms.com/tag/zipfile" rel="tag">zipfile</a></span></p></footer></article>
    


```python
#Get last published header 
headline = article.h2.a.text
print(headline)
```

    Python Tutorial: Zip Files – Creating and Extracting Zip Archives
    


```python
#Get text of first paragraph of summary in first article
summary = article.find('div', class_='entry-content').p.text 
print(summary)
```

    In this video, we will be learning how to create and extract zip archives. We will start by using the zipfile module, and then we will see how to do this using the shutil module. We will learn how to do this with single files and directories, as well as learning how to use gzip as well. Let’s get started…
    

### Getting the link to the video for this code

The source of the video is in an iframe. The source item is the link to the embeded version of the video, not the video iteself, but Youtube videos have a video id. The questionmark identifies where the query parameters start and is not part of hte ID. We need to parse the ID from the URL.

First, we need to grab the URL from the iframe. 



```python
vid_src = article.find('iframe', class_='youtube-player')['src']
print(vid_src)
```

    https://www.youtube.com/embed/z0gguhEmWiY?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent
    

### We want the value from the source attribute of the tag

We can access it like a dictionary and say that we want the source attribute of the tag. We need to parse the URL string to grab the id of the video. 


```python
#Split method on a string
vid_id = vid_src.split('/')
print(vid_id)
```

    ['https:', '', 'www.youtube.com', 'embed', 'z0gguhEmWiY?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent']
    


```python
#Let's get the 4th index
vid_id = vid_src.split('/')[4]
print(vid_id)
```

    z0gguhEmWiY?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent
    


```python
#Let's get the video id 
#vid_id = vid_id.split('?')
#print(vid_id)
```

    ['z0gguhEmWiY', 'version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent']
    


```python
#Let's get the first index
vid_id = vid_id.split('?')[0]
print(vid_id)
```

    z0gguhEmWiY
    

### Let's create our own youtube link using this video id



```python
#Format through f (f-string)
yt_link = f'https://youtube.com/watch?v={vid_id}'
print(yt_link)
```

    https://youtube.com/watch?v=z0gguhEmWiY
    

### Let's loop over all articles


```python
for article in soup.find_all('article'):
    headline = article.h2.a.text
    print(headline)
    
    summary = article.find('div', class_='entry-content').p.text
    print(summary)
    
    vid_src = article.find('iframe', class_='youtube-player')['src']
    
    vid_id = vid_src.split('/')[4]
    vid_id = vid_id.split('?')[0]
    
    yt_link = f'https://youtube.com/watch?v={vid_id}'
    print(yt_link)
    
    print()
```

    Python Tutorial: Zip Files – Creating and Extracting Zip Archives
    In this video, we will be learning how to create and extract zip archives. We will start by using the zipfile module, and then we will see how to do this using the shutil module. We will learn how to do this with single files and directories, as well as learning how to use gzip as well. Let’s get started…
    https://youtube.com/watch?v=z0gguhEmWiY
    
    Python Data Science Tutorial: Analyzing the 2019 Stack Overflow Developer Survey
    In this Python Programming video, we will be learning how to download and analyze real-world data from the 2019 Stack Overflow Developer Survey. This is terrific practice for anyone getting into the data science field. We will learn different ways to analyze this data and also some best practices. Let’s get started…
    https://youtube.com/watch?v=_P7X8tMplsw
    
    Python Multiprocessing Tutorial: Run Code in Parallel Using the Multiprocessing Module
    In this Python Programming video, we will be learning how to run code in parallel using the multiprocessing module. We will also look at how to process multiple high-resolution images at the same time using a ProcessPoolExecutor from the concurrent.futures module. Let’s get started…
    https://youtube.com/watch?v=fKl2JW_qrso
    
    Python Threading Tutorial: Run Code Concurrently Using the Threading Module
    In this Python Programming video, we will be learning how to run threads concurrently using the threading module. We will also look at how to download multiple high-resolution images online using a ThreadPoolExecutor from the concurrent.futures module. Let’s get started…
    https://youtube.com/watch?v=IEEhzQoKtQU
    
    Update (2019-09-03)
    Hey everyone. I wanted to give you an update on my videos. I will be releasing videos on threading and multiprocessing within the next week. Thanks so much for your patience. I currently have a temporary recording studio setup at my Airbnb that will allow me to record and edit the threading/multiprocessing videos. I am going to be moving into my new house in 10 days and once I have my recording studio setup then you can expect much faster video releases. I really appreciate how patient everyone has been while I go through this move, especially those of you who are contributing monthly through YouTube 
    


    ---------------------------------------------------------------------------

    TypeError                                 Traceback (most recent call last)

    <ipython-input-24-af8223fd91e3> in <module>
          6     print(summary)
          7 
    ----> 8     vid_src = article.find('iframe', class_='youtube-player')['src']
          9 
         10     vid_id = vid_src.split('/')[4]
    

    TypeError: 'NoneType' object is not subscriptable


### We have to account for the articles without videos!

We need to use a try and except in Python


```python
for article in soup.find_all('article'):
    headline = article.h2.a.text
    print(headline)
    
    summary = article.find('div', class_='entry-content').p.text
    print(summary)
    
  
    try: 
        vid_src = article.find('iframe', class_='youtube-player')['src']
    
        vid_id = vid_src.split('/')[4]
        vid_id = vid_id.split('?')[0]
    
        yt_link = f'https://youtube.com/watch?v={vid_id}'
    except Exception as e: 
        yt_link=None
    
    print(yt_link) 
    
    print()
```

    Python Tutorial: Zip Files – Creating and Extracting Zip Archives
    In this video, we will be learning how to create and extract zip archives. We will start by using the zipfile module, and then we will see how to do this using the shutil module. We will learn how to do this with single files and directories, as well as learning how to use gzip as well. Let’s get started…
    https://youtube.com/watch?v=z0gguhEmWiY
    
    Python Data Science Tutorial: Analyzing the 2019 Stack Overflow Developer Survey
    In this Python Programming video, we will be learning how to download and analyze real-world data from the 2019 Stack Overflow Developer Survey. This is terrific practice for anyone getting into the data science field. We will learn different ways to analyze this data and also some best practices. Let’s get started…
    https://youtube.com/watch?v=_P7X8tMplsw
    
    Python Multiprocessing Tutorial: Run Code in Parallel Using the Multiprocessing Module
    In this Python Programming video, we will be learning how to run code in parallel using the multiprocessing module. We will also look at how to process multiple high-resolution images at the same time using a ProcessPoolExecutor from the concurrent.futures module. Let’s get started…
    https://youtube.com/watch?v=fKl2JW_qrso
    
    Python Threading Tutorial: Run Code Concurrently Using the Threading Module
    In this Python Programming video, we will be learning how to run threads concurrently using the threading module. We will also look at how to download multiple high-resolution images online using a ThreadPoolExecutor from the concurrent.futures module. Let’s get started…
    https://youtube.com/watch?v=IEEhzQoKtQU
    
    Update (2019-09-03)
    Hey everyone. I wanted to give you an update on my videos. I will be releasing videos on threading and multiprocessing within the next week. Thanks so much for your patience. I currently have a temporary recording studio setup at my Airbnb that will allow me to record and edit the threading/multiprocessing videos. I am going to be moving into my new house in 10 days and once I have my recording studio setup then you can expect much faster video releases. I really appreciate how patient everyone has been while I go through this move, especially those of you who are contributing monthly through YouTube 
    None
    
    Python Quick Tip: The Difference Between “==” and “is” (Equality vs Identity)
    In this Python Programming Tutorial, we will be learning the difference between using “==” and the “is” keyword when doing comparisons. The difference between these is that “==” checks to see if values are equal, and the “is” keyword checks their identity, which means it’s going to check if the values are identical in terms of being the same object in memory. We’ll learn more in the video. Let’s get started…
    https://youtube.com/watch?v=mO_dS3rXDIs
    
    Python Tutorial: Calling External Commands Using the Subprocess Module
    In this Python Programming Tutorial, we will be learning how to run external commands using the subprocess module from the standard library. We will learn how to run commands, capture the output, handle errors, and also how to pipe output into other commands. Let’s get started…
    https://youtube.com/watch?v=2Fp1N6dof0Y
    
    Visual Studio Code (Windows) – Setting up a Python Development Environment and Complete Overview
    In this Python Programming Tutorial, we will be learning how to set up a Python development environment in VSCode on Windows. VSCode is a very nice free editor for writing Python applications and many developers are now switching over to this editor. In this video, we will learn how to install VSCode, get the Python extension installed, how to change Python interpreters, create virtual environments, format/lint our code, how to use Git within VSCode, how to debug our programs, how unit testing works, and more. We have a lot to cover, so let’s go ahead and get started…
    https://youtube.com/watch?v=-nh9rCzPJ20
    
    Visual Studio Code (Mac) – Setting up a Python Development Environment and Complete Overview
    In this Python Programming Tutorial, we will be learning how to set up a Python development environment in VSCode on MacOS. VSCode is a very nice free editor for writing Python applications and many developers are now switching over to this editor. In this video, we will learn how to install VSCode, get the Python extension installed, how to change Python interpreters, create virtual environments, format/lint our code, how to use Git within VSCode, how to debug our programs, how unit testing works, and more. We have a lot to cover, so let’s go ahead and get started…
    https://youtube.com/watch?v=06I63_p-2A4
    
    Clarifying the Issues with Mutable Default Arguments
    In this Python Programming Tutorial, we will be clarifying the issues with mutable default arguments. We discussed this in my last video titled “5 Common Python Mistakes and How to Fix Them”, but I received many comments from people who were still confused. So we will be doing a deeper dive to explain exactly what is going on here. Let’s get started…
    https://youtube.com/watch?v=_JGmemuINww
    
    


```python

```
