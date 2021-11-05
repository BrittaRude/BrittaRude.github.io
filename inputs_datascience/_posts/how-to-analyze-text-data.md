Text as data – Clustering text data

In a [recent blogpost] I talked about the fact that we have large amounts of text data piling up everywhere around us, sitting ready for our analysis! But how can we actually analyze this data? And what are some questions we can ask when using text data? The problem with text data is that many analytical models cannot read text by itself, something that text data analysts often call *Text Feature Extraction*. This means that we have to find ways to transform text into numeric features. There are several ways to do this. Let’s have a look at some examples. For the code behind these example see [here]( https://sanjayasubedi.com.np/nlp/nlp-feature-extraction/). 

## Binary encoding 

In text analysis, a dataset is often called *corpus*. So, let’s take an example corpus such as: *“Every morning I bike to work, passing a blue car.”*. Our corpus consists of 10 words. Binary transformation works with vectors and the size of the vector is equal to the size of the corpus, in this case 10 words. We first need to create a list out of the above sentence, which in Python, for example, would look like this: 
[Every, morning, I, bike, to, work, passing, a, blue, car]. 
What binary encoding does now is that it compares text to this pre-established corpus. Let’s have a look at the sentence: “I saw a blue car.” The binary transformation of this sentence will look as follows: 
[0, 0, 1, 0, 0, 0, 0, 1, 1, 1]    
What we do is to loop through every word of our corpus and set it to 1 if it exists in our example sentence. You might have noticed that the word “saw” is completely ignored in our example sentence, as it is not included in our pre-defined corpus. It is therefore recommended to establish a sufficiently large corpus, containing as many words as possible. The python library *sklearn* already does that for us, for example. Especially the [CountVectorize]( sklearn.feature_extraction.text.CountVectorizer — scikit-learn 1.0.1 documentation) package can help us with that. Let’s have a look at a more complicated example now, a *document* consisting of 3 sentences: “Yesterday was a sunny day. Today is not a sunny day. Today is a rainy day.” Our corpus, if sorted alphabetically, will look like this: 
[a, day, is, not, rainy, sunny, Today, was, Yesterday]
Now, comparing our *document* of three sentences to this corpus requires us the create a list of these three sentences first, which would look something like this: 
[“Yesterday was a sunny day”, “Today is not a sunny day”, “Today is a rainy day”]
Our feature matrix, when compared to our alphabetically sorted corpus, would then be a *3x9* matrix, looking something like this: 
1 1 0 0 0 1 0 1 0 
1 1 1 1  0 1 1 0 0 
1 1 1 0 1 0 1 0 0 
Now, this is a simple numeric representation of text data, which machine learning models or other analytical models can process. The insight of this feature matrix, on the other hand, might not be very valuable. So, let’s try to find a method that extracts some more value from our text data. 

## Counting words

Counting takes the above approach to the next level through checking how often a word exists instead of simply checking if the word exists. Let’s slightly change the sentences of our example from above to get a better idea of what that means: 
[“Yesterday was a sunny day”, “Today is not a sunny day but a rainy day”]
This will result in the following corpus: 
[a, but, day, is, not, rainy, sunny, Today, was, Yesterday]
When comparing our example to the pre-established corpus, we will encounter the following feature matrix of size *2x10*
1 0 1 0 0 0 1 0 1 0 
2 1 2 1 1  0 1 1 0 0
As the words *a* and *day* are mentioned twice in our second example sentence, the value is 2 in our feature matrix. So this is some kind of weighting mechanism, giving higher weights to the words which come up more often in our document. 
Simply counting the number of occurrences of words might not be very valuable neither, as words like “a” or “as” occurring quite frequently in written document might receive a lot of attention without having a lot of value for our analysis. This is why there are a variety of different counting methods (one example is the *TF-IDF* method, which gives higher weight to words occurring less frequently).  

## Clustering words 

Sometimes it might be useful to group different items together, based on their similarity. Text data analysists call this *text clustering*. We can do this manually or use algorithms to make our lives easier, especially in the case of large text datasets. For example, it might be easy to group the words of the following sentence by hand: “Today is a sunny day and I love the sun as it makes me feel happy.” But it might take us a long time to group the Wikipedia article on the [sun]( https://en.wikipedia.org/wiki/Sun) into different categories. 
There are several algorithms available for text clustering, which are nothing more than unsupervised learning algorithms. Some examples are [KMeans](https://towardsdatascience.com/understanding-k-means-clustering-in-machine-learning-6a6e67336aa1) clustering or [DBSCAN clustering]( https://www.machinecurve.com/index.php/2020/12/09/performing-dbscan-clustering-with-python-and-scikit-learn/). In the end your choice will depend on if you already know the number of clusters you want to compute as well as the necessity to predict clusters for unseen datasets. 
K-means clustering is a simple unsupervised machine learning algorithm. The purpose is to group data points together, based on their similarity and to identify patterns. The main mechanism to do this is to look at a fixed number of clusters (k) in the dataset. Each cluster has a centroid, which is the center of the cluster. The algorithm locates each data point to the nearest cluster, while trying to keep the centroids as small as possible. The centroid refers to the averaging of the data. 
The problem with the K-Means Clustering method is that you need to know the exact amount of clusters before you run the model. You could just determine an arbitrary number of clusters, or use the [elbow method]( https://pythonprogramminglanguage.com/kmeans-elbow-method/). Running the model is quite straightforward and you can also use it to predict the clustering on unseen data. 

## Topic Modeling 
Topic modeling does not cluster your data, but is similar in a sense that it retrieves a number of topics prevalent in your text data. It can help us to summarize documents, find hidden themes in written text, or tag a large amount of different papers. Let’s assume that you have a number of 100 news articles. You do not know these articles and would like to know which topics they are covering. Text processing algorithms can help you with that. One example is the [Non Negative Matrix Factorization (NMF) method]( https://sanjayasubedi.com.np/nlp/nlp-with-python-topic-modeling/) or [Latent Dirichlet Allocation]( https://sanjayasubedi.com.np/nlp/nlp-with-python-topic-modeling/). 
