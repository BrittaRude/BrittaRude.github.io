---
layout: post
title: "Reading in a PDF Table using Python"
date: 2022-01-14
myexcerpt: Often we have to process data in annoying formats. One exampel are PDF Tables. But luckily Python can help! Here we will read in a table from a pdf file using Python.  
image: /images/read_pdf.jpg
---

Often we have to process data in annoying formats. One exampel are PDF Tables. But luckily Python can help! Here we will read in a table from a pdf file using Python. For more information [see this link](https://colab.research.google.com/github/chezou/tabula-py/blob/master/examples/tabula_example.ipynb#scrollTo=q6FGPenCluQz). We are reading in the tables from the annex of [this document](https://www.oecd-ilibrary.org/docserver/5f65ff7e-en.pdf?expires=1642059287&id=id&accname=guest&checksum=7AE1164B91B4404E41ACD029B6BA19A2). *[Picture by Markus Winkler]*


```python
!pip install -q tabula-py
```


```python
import tabula
import pandas as pd
```

    C:\Users\Rude\AppData\Local\Continuum\anaconda3\lib\site-packages\pandas\compat\_optional.py:138: UserWarning: Pandas requires version '2.7.0' or newer of 'numexpr' (version '2.6.8' currently installed).
      warnings.warn(msg, UserWarning)
    


```python
pdf_path = r"F:\Working with Github\Horizon2020_migration\raw_data_supplements\Patent_Data\OECD_AIPatents.pdf"
```

## Let's read in our keyword table on page 66 and 67


```python
dfs = tabula.read_pdf(pdf_path, pages=[66,67], columns=None, pandas_options={'header': None})
```

The command gives us two pandas dataframes, one for each page. 


```python
print(len(dfs))
dfs[0]
```

    2
    




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
      <th>0</th>
      <th>1</th>
      <th>2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>action recognition</td>
      <td>activity recognition</td>
      <td>adaboost</td>
    </tr>
    <tr>
      <th>1</th>
      <td>human action recognition</td>
      <td>human activity recognition</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>adaptive boosting</td>
      <td>adversarial network</td>
      <td>ambient intelligence</td>
    </tr>
    <tr>
      <th>3</th>
      <td>NaN</td>
      <td>generative adversarial network</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>ant colony</td>
      <td>artificial intelligence</td>
      <td>association rule</td>
    </tr>
    <tr>
      <th>5</th>
      <td>ant colony optimisation</td>
      <td>human aware artificial intelligence</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>6</th>
      <td>autoencoder</td>
      <td>autonomic computing</td>
      <td>autonomous vehicle</td>
    </tr>
    <tr>
      <th>7</th>
      <td>autonomous weapon</td>
      <td>backpropagation</td>
      <td>Bayesian learning</td>
    </tr>
    <tr>
      <th>8</th>
      <td>bayesian network</td>
      <td>bee colony</td>
      <td>blind signal separation</td>
    </tr>
    <tr>
      <th>9</th>
      <td>NaN</td>
      <td>artificial bee colony algorithm</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>10</th>
      <td>bootstrap aggregation</td>
      <td>brain computer interface</td>
      <td>brownboost</td>
    </tr>
    <tr>
      <th>11</th>
      <td>chatbot</td>
      <td>classification tree</td>
      <td>cluster analysis</td>
    </tr>
    <tr>
      <th>12</th>
      <td>cognitive automation</td>
      <td>cognitive computing</td>
      <td>cognitive insight system</td>
    </tr>
    <tr>
      <th>13</th>
      <td>cognitive modelling</td>
      <td>collaborative filtering</td>
      <td>collision avoidance</td>
    </tr>
    <tr>
      <th>14</th>
      <td>community detection</td>
      <td>computational intelligence</td>
      <td>computational pathology</td>
    </tr>
    <tr>
      <th>15</th>
      <td>computer vision</td>
      <td>cyber physical system</td>
      <td>data mining</td>
    </tr>
    <tr>
      <th>16</th>
      <td>decision tree</td>
      <td>deep belief network</td>
      <td>deep learning</td>
    </tr>
    <tr>
      <th>17</th>
      <td>dictionary learning</td>
      <td>dimensionality reduction</td>
      <td>dynamic time warping</td>
    </tr>
    <tr>
      <th>18</th>
      <td>emotion recognition</td>
      <td>ensemble learning</td>
      <td>evolutionary algorithm</td>
    </tr>
    <tr>
      <th>19</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>differential evolution algorithm</td>
    </tr>
    <tr>
      <th>20</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>multi-objective evolutionary algorithm</td>
    </tr>
    <tr>
      <th>21</th>
      <td>evolutionary computation</td>
      <td>face recognition</td>
      <td>facial expression recognition</td>
    </tr>
    <tr>
      <th>22</th>
      <td>factorisation machine</td>
      <td>feature engineering</td>
      <td>feature extraction</td>
    </tr>
    <tr>
      <th>23</th>
      <td>feature learning</td>
      <td>feature selection</td>
      <td>firefly algorithm</td>
    </tr>
    <tr>
      <th>24</th>
      <td>fuzzy c</td>
      <td>gaussian mixture model</td>
      <td>gaussian process</td>
    </tr>
    <tr>
      <th>25</th>
      <td>fuzzy environment</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>26</th>
      <td>fuzzy logic</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>27</th>
      <td>fuzzy number</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>28</th>
      <td>fuzzy set</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>29</th>
      <td>intuitionistic fuzzy set</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>30</th>
      <td>fuzzy system</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>31</th>
      <td>t s fuzzy system</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>32</th>
      <td>Takagi-Sugeno fuzzy systems</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>33</th>
      <td>genetic algorithm</td>
      <td>genetic programming</td>
      <td>gesture recognition</td>
    </tr>
    <tr>
      <th>34</th>
      <td>gradient boosting</td>
      <td>graphical model</td>
      <td>gravitational search algorithm</td>
    </tr>
    <tr>
      <th>35</th>
      <td>gradient tree boosting</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>36</th>
      <td>hebbian learning</td>
      <td>hierarchical clustering</td>
      <td>high-dimensional data</td>
    </tr>
    <tr>
      <th>37</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>high-dimensional feature</td>
    </tr>
    <tr>
      <th>38</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>high-dimensional input</td>
    </tr>
    <tr>
      <th>39</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>high-dimensional model</td>
    </tr>
    <tr>
      <th>40</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>high-dimensional space</td>
    </tr>
    <tr>
      <th>41</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>high-dimensional system</td>
    </tr>
    <tr>
      <th>42</th>
      <td>image classification</td>
      <td>image processing</td>
      <td>image recognition</td>
    </tr>
    <tr>
      <th>43</th>
      <td>image retrieval</td>
      <td>image segmentation</td>
      <td>independent component analysis</td>
    </tr>
    <tr>
      <th>44</th>
      <td>inductive monitoring</td>
      <td>instance-based learning</td>
      <td>intelligence augmentation</td>
    </tr>
    <tr>
      <th>45</th>
      <td>intelligent agent</td>
      <td>intelligent classifier</td>
      <td>intelligent geometric computing</td>
    </tr>
    <tr>
      <th>46</th>
      <td>intelligent software agent</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>47</th>
      <td>intelligent infrastructure</td>
      <td>Kernel learning</td>
      <td>K-means</td>
    </tr>
    <tr>
      <th>48</th>
      <td>latent dirichlet allocation</td>
      <td>latent semantic analysis</td>
      <td>latent variable</td>
    </tr>
  </tbody>
</table>
</div>




```python
dfs[1]
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
      <th>0</th>
      <th>1</th>
      <th>2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>layered control system</td>
      <td>learning automata</td>
      <td>link prediction</td>
    </tr>
    <tr>
      <th>1</th>
      <td>logitboost</td>
      <td>long short term memory (LSTM)</td>
      <td>lpboost</td>
    </tr>
    <tr>
      <th>2</th>
      <td>machine intelligence</td>
      <td>machine learning\rextreme machine learning</td>
      <td>machine translation</td>
    </tr>
    <tr>
      <th>3</th>
      <td>machine vision</td>
      <td>madaboost</td>
      <td>MapReduce</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Markovian\rhidden Markov model</td>
      <td>memetic algorithm</td>
      <td>meta learning</td>
    </tr>
    <tr>
      <th>5</th>
      <td>motion planning</td>
      <td>multi task learning</td>
      <td>multi-agent system</td>
    </tr>
    <tr>
      <th>6</th>
      <td>multi-label classification</td>
      <td>multi-layer perceptron</td>
      <td>multinomial naïve Bayes</td>
    </tr>
    <tr>
      <th>7</th>
      <td>multi-objective optimisation</td>
      <td>naïve Bayes classifier</td>
      <td>natural gradient</td>
    </tr>
    <tr>
      <th>8</th>
      <td>natural language generation</td>
      <td>natural language processing</td>
      <td>natural language understanding</td>
    </tr>
    <tr>
      <th>9</th>
      <td>nearest neighbour algorithm</td>
      <td>neural network\rartificial neural network\rcon...</td>
      <td>neural turing\rneural turing machine</td>
    </tr>
    <tr>
      <th>10</th>
      <td>neuromorphic computing</td>
      <td>non negative matrix factorisation</td>
      <td>object detection</td>
    </tr>
    <tr>
      <th>11</th>
      <td>object recognition</td>
      <td>obstacle avoidance</td>
      <td>pattern recognition</td>
    </tr>
    <tr>
      <th>12</th>
      <td>pedestrian detection</td>
      <td>policy gradient methods</td>
      <td>Q-learning</td>
    </tr>
    <tr>
      <th>13</th>
      <td>random field</td>
      <td>random forest</td>
      <td>rankboost</td>
    </tr>
    <tr>
      <th>14</th>
      <td>recommender system</td>
      <td>regression tree</td>
      <td>reinforcement learning</td>
    </tr>
    <tr>
      <th>15</th>
      <td>relational learning\rstatistical relational le...</td>
      <td>robot\rbiped robot\rhumanoid robot\rhuman-robo...</td>
      <td>rough set</td>
    </tr>
    <tr>
      <th>16</th>
      <td>rule learning\rrule-based learning</td>
      <td>self-organising map</td>
      <td>self-organising structure</td>
    </tr>
    <tr>
      <th>17</th>
      <td>semantic web</td>
      <td>semi-supervised learning</td>
      <td>sensor fusion\rsensor data fusion\rmulti-senso...</td>
    </tr>
    <tr>
      <th>18</th>
      <td>sentiment analysis</td>
      <td>similarity learning</td>
      <td>simultaneous localisation mapping</td>
    </tr>
    <tr>
      <th>19</th>
      <td>single-linkage clustering</td>
      <td>sparse representation</td>
      <td>spectral clustering</td>
    </tr>
    <tr>
      <th>20</th>
      <td>speech recognition</td>
      <td>speech to text</td>
      <td>stacked generalisation</td>
    </tr>
    <tr>
      <th>21</th>
      <td>stochastic gradient</td>
      <td>supervised learning</td>
      <td>support vector regression</td>
    </tr>
    <tr>
      <th>22</th>
      <td>swarm intelligence</td>
      <td>swarm optimisation\rparticle swarm optimisation</td>
      <td>temporal difference learning</td>
    </tr>
    <tr>
      <th>23</th>
      <td>text mining</td>
      <td>text to speech</td>
      <td>topic model</td>
    </tr>
    <tr>
      <th>24</th>
      <td>totalboost</td>
      <td>trajectory planning</td>
      <td>trajectory tracking</td>
    </tr>
    <tr>
      <th>25</th>
      <td>transfer learning</td>
      <td>trust region policy optimisation</td>
      <td>unmanned aerial vehicle</td>
    </tr>
    <tr>
      <th>26</th>
      <td>unsupervised learning</td>
      <td>variational inference</td>
      <td>vector machine\rsupport vector machine</td>
    </tr>
    <tr>
      <th>27</th>
      <td>virtual assistant</td>
      <td>visual servoing</td>
      <td>xgboost</td>
    </tr>
  </tbody>
</table>
</div>



Let's append both dataframes


```python
df = dfs[0].append(dfs[1])
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
      <th>0</th>
      <th>1</th>
      <th>2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>action recognition</td>
      <td>activity recognition</td>
      <td>adaboost</td>
    </tr>
    <tr>
      <th>1</th>
      <td>human action recognition</td>
      <td>human activity recognition</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>adaptive boosting</td>
      <td>adversarial network</td>
      <td>ambient intelligence</td>
    </tr>
    <tr>
      <th>3</th>
      <td>NaN</td>
      <td>generative adversarial network</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>ant colony</td>
      <td>artificial intelligence</td>
      <td>association rule</td>
    </tr>
  </tbody>
</table>
</div>




```python
df.tail()
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
      <th>0</th>
      <th>1</th>
      <th>2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>23</th>
      <td>text mining</td>
      <td>text to speech</td>
      <td>topic model</td>
    </tr>
    <tr>
      <th>24</th>
      <td>totalboost</td>
      <td>trajectory planning</td>
      <td>trajectory tracking</td>
    </tr>
    <tr>
      <th>25</th>
      <td>transfer learning</td>
      <td>trust region policy optimisation</td>
      <td>unmanned aerial vehicle</td>
    </tr>
    <tr>
      <th>26</th>
      <td>unsupervised learning</td>
      <td>variational inference</td>
      <td>vector machine\rsupport vector machine</td>
    </tr>
    <tr>
      <th>27</th>
      <td>virtual assistant</td>
      <td>visual servoing</td>
      <td>xgboost</td>
    </tr>
  </tbody>
</table>
</div>




```python
df.to_excel(r"F:\Working with Github\Horizon2020_migration\raw_data_supplements\Patent_Data\AI_Keywords.xlsx", header=False, index=False)
```

## Let's read in our IPC Codes on page 67


```python
dfs = tabula.read_pdf(pdf_path, pages=[68], columns=None, pandas_options={'header': None})
```

Again, we read in the 3 tables as 3 different data frames. 


```python
print(len(dfs))
dfs[0]
```

    3
    




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
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>G06N3</td>
      <td>G06N5</td>
      <td>G06N20</td>
      <td>G06F15/18</td>
    </tr>
    <tr>
      <th>1</th>
      <td>G06T1/40</td>
      <td>G16C20/70</td>
      <td>G16B40/20</td>
      <td>G16B40/30</td>
    </tr>
  </tbody>
</table>
</div>




```python
dfs[0].to_excel(r"F:\Working with Github\Horizon2020_migration\raw_data_supplements\Patent_Data\IPC_Codes_withoutKeywords.xlsx", header=False, index=False)
```


```python
dfs[1]
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
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>G01R31/367</td>
      <td>G06F17/(20-28, 30)</td>
      <td>G06F19/24</td>
      <td>G06K9/00</td>
    </tr>
    <tr>
      <th>1</th>
      <td>G06K9/(46-52, 60-82)</td>
      <td>G06N7</td>
      <td>G06N10</td>
      <td>G06N99</td>
    </tr>
    <tr>
      <th>2</th>
      <td>G06Q</td>
      <td>G06T7/00-20</td>
      <td>G10L15</td>
      <td>G10L21</td>
    </tr>
    <tr>
      <th>3</th>
      <td>G16B40/(00-10)</td>
      <td>G16H50/20-70</td>
      <td>H01M8/04992</td>
      <td>H04N21/466</td>
    </tr>
  </tbody>
</table>
</div>




```python
dfs[1].to_excel(r"F:\Working with Github\Horizon2020_migration\raw_data_supplements\Patent_Data\IPC_Codes_withKeywords.xlsx", header=False, index=False)
```

That's it! Good luck with making your life easier using Python's tabula and pandas. :-) 


```python

```
