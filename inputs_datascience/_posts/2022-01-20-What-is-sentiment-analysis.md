---
layout: post
usemathjax: true 
title: "What is Sentiment Analysis?"
date: 2022-1-20
image: /images/SentimentAnalysis2.jpg
---


<img src="/images/SentimentAnalysis.jpg" alt="sentimentanalysis" style="float:left;margin: 2px 2px 2px 2px;max-width:40%;"/>

Big data creates new opportunities to analyze open questions. One example is a project by [UNHCR and UN Global Pulse](https://childhub.org/en/child-protection-online-library/what-twitter-data-can-tell-us-about-europe-refugee-emergency?language=el), in which they analyze the sentiments around forced displacement, using social media data. What they do in the project is to use twitter data and classify tweets into three different categories: Xenophobic, neutral, and irrelevant. In a first step, the researchers classify a subset of the overall dataset by hand into these three categories and then train the machine based on this hand-coded dataset in order to categorize the remaining tweets. This is a typical example of a Sentiment Analysis. [Another paper](https://osf.io/preprints/socarxiv/pc3za/) by a group of researchers from Liverpool, Barcelona and Berlin uses Twitter data to analyze how sentiments about migration have shifted during the early stage of the COVID-19 pandemic. They make use of 30.39 million tweets and natural language processing, and find that there has been no overall shift in the average opinion score around the topic, but a higher polarization around the more extreme ends of the sentiment distribution. These are two examples of the cool things you can do with Sentiment Analysis. In this blogpost we will have a look at what a Sentiment Analysis is and how it works. 
## The concept of Sentiment Analysis 
Sentiment Analysis, or Opinion Mining, as it is also called, in a nutshell is the classification of text into positive, negative, or neutral sentiments. It takes advantage of Natural Language Processing and Machine Learning Algorithms. The ultimate goal is to detect the emotional tone behind written conversations. You can program your own Sentiment Analysis Algorithm, or you can just take advantage of already existing packages, floating around. In the UNHCR and UN Global Pulse project they programmed their own algorithm, while in the second paper mentioned above they took advantage of a preexisting package called VADER. In order to implement Sentiment Analysis you should still be familiar with text analysis, as it is crucial to prepare your data before doing Opinion Mining. For a detailed overview on how to do this have a look at my previous blogposts and the Code section. 
The result of running a sentiment analysis will often be a binary code (for example, a positive or negative sentiment), or a score (the degree of positivity or negativity). There are three different approaches towards Sentiment Analysis: The rule-based approach, the automatic approach and the hybrid approach. The hybrid approach is a mix of the first two ones ([Source]( https://monkeylearn.com/sentiment-analysis/)). 
## Rule-based Sentiment Analysis 
Rule-based approaches take a very simple and naïve approach towards opinion mining. As an example, you could just create a list of polarized words (such as horrible, bad, ugly, scary versus beautiful, nice, great, awesome) and tell your algorithm to classify a text as positive as soon as the number of positive words is higher than the number of negative words ([Source]( https://monkeylearn.com/sentiment-analysis/)). While rule-based approaches are rather simple and abstract from the sequence and interrelation of words, in the case of opinion mining, this might not be critical. 
Commonly used packages often used in Python are TextBlob, VADER, and SentiWordNet (for an overview see [here]( https://www.analyticsvidhya.com/blog/2021/06/rule-based-sentiment-analysis-in-python/)). All three packages draw from preexisting Lexicons, which are the basis for the classification of new text. Let’s dig deeper into one of them, the VADER classifier. VADER stands for Valence Aware Dictionary and Sentiment Reasoner and gives you four different scores measuring the sentiment behind your text: The negativity score, the neutrality score, and the positivity score. All of them add up to one. Then there is the compound score, which represents the overall sentiment and ranges from -1 to 1. VADER is especially well suited for social media data, as it also considers smileys (such as ) or signs (such as “???”). What VADER does is to take the piece of text you read in (let’s take as an example the phrase “I really love chocolate! ”) and compares each word to the lexicon it is based on. In the lexicon, words are related with a number indicating the degree of positivity and negativity (e.g. tragedy has a sentiment rating of -3.4, while great has a sentiment rating of 3.1). This is then the basis of the final sentiment ranking ([Source]( https://www.analyticsvidhya.com/blog/2021/06/vader-for-sentiment-analysis/)). 
## Automatic Approaches
Automatic approaches follow the typical machine learning procedure. Instead of defining a rule, which your opinion mining follows along, you base your sentiment analysis on a classification problem. You then take a classifier, feed in your text, resulting in a category (positive, negative, or neutral). Similar to what I have shown you in my previous blogposts about machine learning, you first train your data using pre-classified data (e.g. hand-coded text, which you gave different sentiment scores based on your own impression) and then make a prediction of the rest of your not yet classified data. You can try out several different classifiers, such as the Naïve Bayes Classifier, the Support Vector Machine, or the Logistic Regression Classifier, and then compare their performance to each other. 
For an example of the full workflow, see [here](https://www.analyticsvidhya.com/blog/2021/06/twitter-sentiment-analysis-a-nlp-use-case-for-beginners/). You start of by transforming your text into features. There are several methods to do that, and [this blogpost of mine](https://brittarude.github.io/blog/2021/11/05/how-to-analyze-text-data) gave you one example. The methods range from bag-of-words, or bag-of-ngrams to word embeddings. You then apply a classification model to this numerically transformed text data. This is where you train your machine. The machine learns to make relations between the numerically transformed text data and the classification. It can then apply these learned patterns to new, unseen data. 
## Hybrid Approaches
Hybrid approaches combine the strength from the rule-based approach (stability) and the machine learning approach (accuracy). One example is [this paper]( https://www.degruyter.com/document/doi/10.1515/jisys-2019-0106/html?lang=de), where they combine the SentiWordNet (SWN) Lexicon and combine it with a Support Vector Machine (SVM). In a first step, they transform their text into features using the SentiWordNet Lexicon, and then train the SVM model based on the SWN-based generated feature vectors. Pretty smart. 
## Limitations and Challenges 
There are several challenges involved in sentiment analysis ([Source] https://monkeylearn.com/sentiment-analysis/)). First of all, context might indeed matter in many cases. Let’s imagine somebody asks you what you liked about a movie and you answer “everything”. Then this “everything” has a positive connotation. But what if the question if what you disliked about a movie? Then this “everything” has a quite negative connotation. Machines additionally have problems to detect irony and sarcasm (as have some human beings, by the way…). Moreover humans typically do not coincide on the degree of sentiment of text. Depending on who evaluates the text, the human sentiment might differ. As machines learn mostly from human-made examples, this imposes further challenges. These challenges and limitations should not deter you from conducting sentiment analysis, but it is important to be aware of them and factor them in.