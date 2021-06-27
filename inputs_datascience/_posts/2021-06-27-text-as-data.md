---
layout: post
usemathjax: true 
title: "Text as data - How is digital text changing the way we see our world?"
date: 2021-06-27
image: /images/digital_text.jpg
---

## How is text data changing our data environment? 

Digitalization has brought along a large amount of digital text. Text data is a new form of data, lining up next to the masses of binary and numeric data, and recently more researchers have started to ask themselves the question about how to use it. Text data generates a new data source, containing rich information, which can create a mirror of reality in real time. Additionally, accessing it might be much cheaper than relying on traditional survey designs, and small sample problems are normally not an issue. How is text data changing the way we see our world? And how can we make productive use of it? 

<img src="/images/digital_text.jpg" alt="automation" style="float:left;margin: 2px 2px 2px 2px;max-width:28%;"/>

A recent example of how text data can be put into practice is the creation of a real time metric of welfare (see [here](https://arxiv.org/ftp/arxiv/papers/2001/2001.03401.pdf)). Instead of using self-reported information from surveys on wellbeing and happiness, the researchers use unstructured online media text. Unstructured online media text is especially pertinent for this purpose, as a large amount of users communicate their emotions in real-time. Another example is the usage of online job adds to gain a better understanding of labor demand and skill mismatches (see for example this project by [CEDEFOP]( https://www.cedefop.europa.eu/de/data-visualisations/skills-online-vacancies)). [Foto by Jason Leung]

The difference between text data and traditional data sources (such as administrative or survey data) is that text data is unstructured. This has led to a rise in computing power as well as a surge of machine learning algorithms in order to analyze this data. The downsides of text data is that the underlying population might not be representative of the socio-economic composition of the population as a whole. 

## Let’s dig deeper – the use of twitter data to measure welfare

Let’s take a deeper look at the example mentioned above, the creation of a welfare measure based on online media text. The researchers use a 1 percent sample of Twit-ter tweets in the greater area of London (approximately 50,000 tweets per day). Around 20 percent of these tweets contain emojis. They then classify those emojis into positive and negative emotions through machine learning algorithms. Afterwards, a group of humans validated a subsample of these tweets on their perception of positivity and negativity. In all but 18 percent of the tweets the 11 people involved in the validation exercise agreed on the direction of emotion. 

<img src="/images/digital_text2.jpg" alt="automation" style="float:right;margin: 2px 2px 2px 2px;max-width:33%;"/>

Machine learning algorithms traditionally rely on quantitative databases. So how do we transfer text data into quantitative data? A widely used methodology is the GloVe (Global Vector for Word Representation) methodology developed by [Pennington et al.]( https://nlp.stanford.edu/projects/glove/) from Stanford University. The GloVe is an unsupervised learning algorithm developed to obtain a vector representation of words. It is based on a nearest neighbor analysis of words from a corpus, resulting in a definition of relatedness of words. The GloVe model can be interpreted as a log-bilinear model giving out the probability under which two words will be listed together and the closeness of a word with all other words listed in the corpus. This means that each word will have an underlying vector describing its closeness to all other words. 

[Nyman and Ormerod (2020)] (https://arxiv.org/ftp/arxiv/papers/2001/2001.03401.pdf) then take these vectors and create an average vector of each tweet, finally resulting in the positivity or negativity of the resulting tweet through machine learning algorithms which detect patterns behind these average vectors.   

For the classification of tweets into positive and negative tweets there exist a variety of different machine learning methods which one can apply. [Nyman and Ormerod (2020)] (https://arxiv.org/ftp/arxiv/papers/2001/2001.03401.pdf) apply three different methodologies: Random forests (RF) and linear as well as radial basis function kernel support vector machines (SVM). Through a 10-fold cross validation process, they then choose the model with the highest accuracy in predicting the positivity or negativity of tweets. 

The resulting London Feel Good Factor shows that people are happier on Thursday, Friday and Saturday than on Sunday and Monday. There is a low point for the day after the Brexit Referendum and another one for the day president Trump was elected. 

## The emergence of Online Job Adds to create a real-time picture of labor markets

Another area in which digital text has been used is in the analysis of labor markets. More and more job vacancies are migrated to online formats. This means that we can get a better understanding of labor demand in real time and connect it to specific skill requirements.  

[CEDEFOP]( https://www.cedefop.europa.eu/files/5572_en.pdf), for example, has mapped the online job portal landscape in all EU member states. With this it is possible to map the features of the online job market and identify trends in skill demands. Online job vacancy data might help us to increase the match between labor demand and supply. Another example is work by [Acemoglu et al. (2020)]( https://www.nber.org/papers/w28257). The researchers use information from online job vacancies to study the impact of AI on labor markets. They classify establishments as AI-exposed when their workers engage in AI-related tasks. This information is completely based on information extracted from online job vacancies. 

Of course, the probability of posting a job opening varies by sector and occupation, vacancy skill level, company size and geographic location as well as the age of the target group. It is therefore crucial to correct this bias. Additionally, online job adds are unstructured and not classified, which makes a structured analysis more difficult. Also, text data is highly dimensional and required complex analysis tools, such as machine learning (for detailed information about how to decrease the dimensionality of text data see [Gentzkow et al. (2019)]( https://web.stanford.edu/~gentzkow/research/text-as-data.pdf). Additionally, the usage of certain skill requirements may depend on cultural factors. All these are downsides of text data on which we have to work to increase its accuracy and applicability. 

## So how is text data changing the way we see the world? 

Text data is an additional data source with rich information. It contains knowledge about human interactions, communication, and culture. It is already used in a variety of different fields. We can use it for predictions (classify an email as spam or sentiment prediction) but also for causal questions (e.g. on how racial animus influences votes for a black candidate as by [Stephens-Da-vid-owith](https://www.sciencedirect.com/science/article/abs/pii/S0047272714000929)). The potential of text data to increase our understanding about complex questions is enormous. The creation of new indicators is already on the way, and will for sure keep growing in the future ahead.  

