
# LDA Topic Modeling - The example of CEDEFOP Skill Descriptions

Often we would like to identify topics in a large set of data. This could be the case if we analyze a large set of newspaper articles, for example. Going through all newspapers of the last year would be an insane task, but luckily Machine Learning can help! Hello AI! - Let's have a look at our first AI application example in Python. :-) 

In this post, we will have a look at **LDA Topic Modeling**. Topic Modeling is nothing else than a *Classification Problem* and as it does not require any prelabeling or pretraining, belongs to *Unsupervised Learning*. It is also a category of *Natural Language Processing*. 

In this application, we will follow [this video](https://www.youtube.com/watch?v=TKjjlp5_r7o) as well as [this one](https://www.youtube.com/watch?v=UEn3xHNBXJU&list=PL2VXyKi-KpYttggRATQVmgFcQst3z6OlX&index=11). As an example, we will use the Skill Descriptions of ESCO Skills developed by CEDEFOP. You can download the data [here](https://ec.europa.eu/esco/portal/download). Just choose the skill bundle. 

## Data Preparation and Set-up

Let's load our packages. 


```python
import nltk
nltk.download("stopwords")
```


```python
from nltk.corpus import stopwords
from nltk.tokenize import word_tokenize

import numpy as np
import pandas as pd

import gensim
import gensim.corpora as corpora
from gensim.utils import simple_preprocess
from gensim.models import CoherenceModel

import spacy

import pyLDAvis
import pyLDAvis.gensim_models
```


```python
import en_core_web_sm
nlp = en_core_web_sm.load()
```

Let's load our dataset. 


```python
data = pd.read_csv(r"C:\Users\Rude\Documents\Horizon2020\skills_en.csv")
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
      <th>conceptType</th>
      <th>conceptUri</th>
      <th>skillType</th>
      <th>reuseLevel</th>
      <th>preferredLabel</th>
      <th>altLabels</th>
      <th>hiddenLabels</th>
      <th>status</th>
      <th>modifiedDate</th>
      <th>scopeNote</th>
      <th>definition</th>
      <th>inScheme</th>
      <th>description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>KnowledgeSkillCompetence</td>
      <td>http://data.europa.eu/esco/skill/0005c151-5b5a...</td>
      <td>skill/competence</td>
      <td>sector-specific</td>
      <td>manage musical staff</td>
      <td>manage staff of music\ncoordinate duties of mu...</td>
      <td>NaN</td>
      <td>released</td>
      <td>2016-12-20T17:43:43Z</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>http://data.europa.eu/esco/concept-scheme/skil...</td>
      <td>Assign and manage staff tasks in areas such as...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>KnowledgeSkillCompetence</td>
      <td>http://data.europa.eu/esco/skill/00064735-8fad...</td>
      <td>skill/competence</td>
      <td>occupation-specific</td>
      <td>supervise correctional procedures</td>
      <td>oversee prison procedures\nmanage correctional...</td>
      <td>NaN</td>
      <td>released</td>
      <td>2016-12-20T20:17:49Z</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>http://data.europa.eu/esco/concept-scheme/memb...</td>
      <td>Supervise the operations of a correctional fac...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>KnowledgeSkillCompetence</td>
      <td>http://data.europa.eu/esco/skill/000709ed-2be5...</td>
      <td>skill/competence</td>
      <td>sector-specific</td>
      <td>apply anti-oppressive practices</td>
      <td>apply non-oppressive practices\napply an anti-...</td>
      <td>NaN</td>
      <td>released</td>
      <td>2016-12-20T19:18:19Z</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>http://data.europa.eu/esco/concept-scheme/skil...</td>
      <td>Identify oppression in societies, economies, c...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>KnowledgeSkillCompetence</td>
      <td>http://data.europa.eu/esco/skill/0007bdc2-dd15...</td>
      <td>skill/competence</td>
      <td>sector-specific</td>
      <td>control compliance of railway vehicles regulat...</td>
      <td>monitoring of compliance with railway vehicles...</td>
      <td>NaN</td>
      <td>released</td>
      <td>2016-12-20T20:02:19Z</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>http://data.europa.eu/esco/concept-scheme/skil...</td>
      <td>Inspect rolling stock, components and systems ...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>KnowledgeSkillCompetence</td>
      <td>http://data.europa.eu/esco/skill/00090cc1-1f27...</td>
      <td>skill/competence</td>
      <td>cross-sector</td>
      <td>identify available services</td>
      <td>establish available services\ndetermine rehabi...</td>
      <td>NaN</td>
      <td>released</td>
      <td>2016-12-20T20:15:17Z</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>http://data.europa.eu/esco/concept-scheme/memb...</td>
      <td>Identify the different services available for ...</td>
    </tr>
  </tbody>
</table>
</div>



## Data preparation

We want to remove stopwords as they do not contain so much valuable information and only increase the dimensionality of our dataset.


```python
stopwords = stopwords.words('english')
stopwords[:10]
```




    ['i', 'me', 'my', 'myself', 'we', 'our', 'ours', 'ourselves', 'you', "you're"]



### Prepare pandas column


```python
# remove anything but characters and spaces
text = data['description'].str.replace('[^A-z ]','').str.replace(' +',' ').str.strip()
text.head()
```

    C:\ProgramData\Anaconda3\lib\site-packages\ipykernel_launcher.py:2: FutureWarning: The default value of regex will change from True to False in a future version.
      
    




    0    Assign and manage staff tasks in areas such as...
    1    Supervise the operations of a correctional fac...
    2    Identify oppression in societies economies cul...
    3    Inspect rolling stock components and systems t...
    4    Identify the different services available for ...
    Name: description, dtype: object




```python
#text = text.apply(word_tokenize)
#text.head()
```


```python
print(text[0][0:90])
```

    Assign and manage staff tasks in areas such as scoring arranging copying music and vocal c
    

### Lemmatization

Let's further reduce the dimensionality of our dataset through accounting for the morphological analysis of the words. What this means is that the words "see" and its past tense "saw" will get the same value. This is why lemmatization is in many cases much better than stemmatization. For a detailed explanation see [here](https://blog.bitext.com/what-is-the-difference-between-stemming-and-lemmatization/). 


```python
def lemmatization(texts, allowed_postags=["NOUN", "ADJ", "VERB", "ADV"]):
    nlp = spacy.load("en_core_web_sm", disable=["parser", "ner"])
    texts_out = []
    for text in texts:
        doc = nlp(text)
        new_text = []
        for token in doc:
            if token.pos_ in allowed_postags:
                new_text.append(token.lemma_)
        final = " ".join(new_text)
        texts_out.append(final)
    return (texts_out)
```


```python
lemmatized_texts = lemmatization(text)
print (lemmatized_texts[0][0:90])
```

    assign manage staff task area such score arrange copy music vocal coaching
    

### Remove stopwords


```python
def gen_words(texts):
    final = []
    for text in texts:
        new = gensim.utils.simple_preprocess(text, deacc=True)
        final.append(new)
    return (final)
```


```python
data_words = gen_words(lemmatized_texts)

print (data_words[0][0:20])
```

    ['assign', 'manage', 'staff', 'task', 'area', 'such', 'score', 'arrange', 'copy', 'music', 'vocal', 'coaching']
    

## Bigrams and Trigrams

Let's have a look at word associations. Here we can take advantage of bigrams (which 2 words do often appear together) and trigrams (which 3 words to often appear together). For mor details [see here](https://towardsdatascience.com/text-analysis-basics-in-python-443282942ec5). 


```python
bigram_phrases = gensim.models.Phrases(data_words, min_count=5, threshold=100)
trigram_phrases = gensim.models.Phrases(bigram_phrases[data_words], threshold=100)

bigram = gensim.models.phrases.Phraser(bigram_phrases)
trigram = gensim.models.phrases.Phraser(trigram_phrases)

def make_bigrams(texts):
    return([bigram[doc] for doc in texts])

def make_trigrams(texts):
    return ([trigram[bigram[doc]] for doc in texts])

data_bigrams = make_bigrams(data_words)
data_bigrams_trigrams = make_trigrams(data_bigrams)

print (data_bigrams_trigrams[0][0:20])
```

    ['assign', 'manage', 'staff', 'task', 'area', 'such', 'score', 'arrange', 'copy', 'music', 'vocal', 'coaching']
    


```python
print (data_bigrams_trigrams)
```

## TF-IDF


```python
#TF-IDF REMOVAL
from gensim.models import TfidfModel

id2word = corpora.Dictionary(data_bigrams_trigrams)

texts = data_bigrams_trigrams

corpus = [id2word.doc2bow(text) for text in texts]
# print (corpus[0][0:20])

tfidf = TfidfModel(corpus, id2word=id2word)

low_value = 0.03
words  = []
words_missing_in_tfidf = []
for i in range(0, len(corpus)):
    bow = corpus[i]
    low_value_words = [] #reinitialize to be safe. You can skip this.
    tfidf_ids = [id for id, value in tfidf[bow]]
    bow_ids = [id for id, value in bow]
    low_value_words = [id for id, value in tfidf[bow] if value < low_value]
    drops = low_value_words+words_missing_in_tfidf
    for item in drops:
        words.append(id2word[item])
    words_missing_in_tfidf = [id for id in bow_ids if id not in tfidf_ids] # The words with tf-idf socre 0 will be missing

    new_bow = [b for b in bow if b[0] not in low_value_words and b[0] not in words_missing_in_tfidf]
    corpus[i] = new_bow
```

## Dictionary and Bag of Words

NOTE: We do not use this anymore as we use TF-IDF.


```python
'''
id2word = corpora.Dictionary(data_words)

corpus = []
for text in data_words:
    new = id2word.doc2bow(text)
    corpus.append(new)

print (corpus[0][0:20])

word = id2word[[0][:1][0]]
print (word)
'''
```

    [(0, 1), (1, 1), (2, 1), (3, 1), (4, 1), (5, 1), (6, 1), (7, 1), (8, 1), (9, 1), (10, 1), (11, 1)]
    

## Create Topic Model

Let's run our topic model. We have to choose the number of topics we want to display in our data beforehand (in this case 20). 


```python
lda_model = gensim.models.ldamodel.LdaModel(corpus=corpus,
                                           id2word=id2word,
                                           num_topics=20,
                                           random_state=100,
                                           update_every=1,
                                           chunksize=100,
                                           passes=10,
                                           alpha="auto")
```

## Visualize Data

Let's visualize our data. 


```python
pyLDAvis.enable_notebook()
vis = pyLDAvis.gensim_models.prepare(lda_model, corpus, id2word, mds="mmds", R=30)
vis
```

    C:\ProgramData\Anaconda3\lib\site-packages\pyLDAvis\_prepare.py:247: FutureWarning: In a future version of pandas all arguments of DataFrame.drop except for the argument 'labels' will be keyword-only
      by='saliency', ascending=False).head(R).drop('saliency', 1)
    





<link rel="stylesheet" type="text/css" href="https://cdn.jsdelivr.net/gh/bmabey/pyLDAvis@3.3.1/pyLDAvis/js/ldavis.v1.0.0.css">


<div id="ldavis_el606025368979881925708230102"></div>
<script type="text/javascript">

var ldavis_el606025368979881925708230102_data = {"mdsDat": {"x": [-0.20185936282439046, -0.0016515444470206954, 0.1088943341311281, -0.34083798436619034, -0.2422305548955563, 0.014037263990327854, 0.45252207334243516, -0.39760659175636803, 0.18492077848169342, -0.11109082518705662, -0.42233165375688086, 0.0685902226954882, -0.3908945647566629, 0.26419507463347885, 0.3961801780992818, 0.3877321863951847, 0.28370239939230757, 0.18724961818508912, -0.1884681365961096, -0.0510529107601789], "y": [0.4618918488024911, -0.48975728927360374, 0.45946459303622983, -0.31729612332077506, 0.271508101751653, 0.32197820135471084, -0.013968338011247429, 0.023521303525889405, 0.1339416733855469, 0.06604413613617331, -0.13931727924822077, -0.1259097753185756, 0.1905854014636262, -0.1813364197441383, -0.22228180559089394, 0.15975203481167852, 0.31294883379674004, -0.3743122694911443, -0.24312972032617974, -0.2943271077399603], "topics": [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20], "cluster": [1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1], "Freq": [11.926306271556033, 6.734429238065588, 6.317584238602765, 5.493279862633722, 5.45248284562024, 5.227827229233845, 5.128843713130596, 5.0554362317691295, 5.0402251480023965, 4.831897826378116, 4.741568659745822, 4.731785991601465, 4.524519219443915, 4.2230290230755, 4.076962701048746, 3.923358444892553, 3.749197867537742, 3.3493352784338417, 2.9730386015819716, 2.498891607646011]}, "tinfo": {"Term": ["use", "such", "other", "order", "ensure", "system", "product", "material", "equipment", "different", "process", "provide", "work", "design", "accord", "information", "service", "plan", "procedure", "technique", "activity", "type", "apply", "customer", "operate", "appropriate", "perform", "identify", "make", "machine", "use", "such", "equipment", "process", "type", "various", "water", "repair", "clean", "term", "science", "knowledge", "medicine", "natural", "verify", "brush", "fish", "hold", "cast", "aquaculture", "minor", "welding", "back", "satisfaction", "top", "perform_routine_maintenance", "wear_apparel", "bond", "front", "dead", "order", "method", "specific", "risk", "relate", "technical", "study", "help", "improve", "result", "potential", "cost", "interpret", "aspect", "increase", "exist", "review", "examination", "clinical", "reduce", "achieve", "context", "state", "investigation", "get", "hygiene", "diagnosis", "transmit", "railway", "performer", "plan", "procedure", "technique", "include", "health", "follow", "require", "client", "make_sure", "schedule", "project", "show", "kind", "glass", "manual", "fit", "electric", "step", "cleaning", "tank", "steel", "sketch", "wear", "protective", "guest", "series", "landscape", "accommodation", "copper", "pulp", "information", "tool", "develop", "new", "practice", "research", "characteristic", "security", "behaviour", "hand", "write", "energy", "current", "concept", "amount", "variety", "community", "gather", "interaction", "tree", "code", "package", "optimise", "drilling", "collaborate", "computer_program", "saw", "insulation", "dynamic", "less", "activity", "control", "requirement", "vehicle", "emergency", "store", "medical", "carry", "request", "field", "storage", "transport", "supply", "ship", "movement", "also", "user", "assist", "receive", "raw", "demand", "truck", "flow", "coordinate", "accordance", "inventory", "port", "moisture", "container", "release", "ensure", "make", "operation", "set", "good", "present", "task", "legal", "supervise", "facility", "efficiency", "life", "skill", "staff", "aim", "child", "circumstance", "supplier", "teach", "return", "exchange", "always", "recruit", "motion_picture", "decision", "time", "safe", "meet", "distribution", "correct", "communication", "understand", "language", "animal", "device", "service", "monitor", "safety", "regulation", "area", "check", "electrical", "public", "manufacture", "train", "oversee", "solution", "well", "pressure", "ingredient", "room", "add", "usually", "comply", "engine", "cool", "especially", "consist", "continuous", "start", "defective", "implication", "wiring", "consideration", "daytoday", "product", "machine", "production", "physical", "team", "sale", "organise", "vessel", "tend", "programme", "special", "aid", "sheet", "packaging", "number", "space", "member", "cargo", "fill", "daily", "label", "write_text", "plastic", "box", "crane", "weight", "sort", "arrange", "stability", "glue", "work", "customer", "perform", "identify", "need", "damage", "maintenance", "professional", "handle", "manner", "clear", "general", "address", "replace", "desire", "debris", "recommend", "fix", "anticipate", "roof", "broken", "flexible", "problem", "item", "prevent", "report", "expert", "meet", "religious", "resource", "time", "animal", "care", "understand", "component", "determine", "social", "necessary", "property", "remove", "chemical", "involve", "electronic", "body", "impact", "power", "heat", "mechanical", "call", "cause", "install", "drive", "detect", "therapy", "pump", "connect", "concentration", "substance", "speed", "switch", "operating", "fluid", "electricity", "respect", "operate", "take", "patient", "treatment", "action", "as_well", "environment", "function", "collect", "assessment", "plant", "person", "available", "attach", "safely", "finish", "basic", "concern", "advise", "further", "secure", "exposure", "anatomy", "regulate", "rehabilitation", "origin", "rope", "restore", "discover", "belt", "provide", "create", "test", "prepare", "analyse", "individual", "base", "performance", "structure", "advice", "artistic", "target", "direct", "live", "observation", "counselling", "wide_range", "discuss", "artist", "street", "rehearsal", "safe_working", "master", "report", "animal", "approach", "market", "model", "bank", "instrument", "content", "purpose", "financial", "understand", "relevant", "time", "maintain", "keep", "support", "condition", "place", "record", "oil", "issue", "select", "computer", "adjust", "transfer", "right", "position", "dry", "prevention", "usage", "stock", "mix", "worker", "basis", "convert", "injury", "teacher", "ratio", "period", "already", "compound", "closely", "far", "accord", "datum", "measure", "company", "management", "specification", "policy", "level", "implement", "element", "waste", "site", "medium", "avoid", "database", "option", "credit", "file", "blood", "culture", "fermentation", "legislation", "market", "business", "online", "implementation", "insurance", "ground", "environmental", "resource", "relevant", "financial", "analysis", "time", "animal", "model", "prevent", "understand", "report", "system", "design", "software", "organisation", "application", "possible", "source", "form", "methodology", "deliver", "most", "search", "find", "mining", "data", "infrastructure", "potentially_unauthorised_access", "test_security_weakness", "demonstrate", "try", "network", "engineering", "specialised", "parameter", "hardware", "viability", "testing", "model", "failure", "sensor", "business", "device", "examine", "understand", "relevant", "animal", "language", "time", "surface", "other", "apply", "part", "evaluate", "facilitate", "protect", "range", "pattern", "air", "participate", "government", "occur", "international", "compose", "country", "incident", "home", "creation", "covering", "hoist", "compromise", "insect", "garment", "representative", "cosmetic", "negotiation", "rust", "surface", "wallpaper", "underlayment", "light", "prevent", "animal", "change", "cut", "case", "way", "strategy", "group", "training", "size", "establish", "lead", "program", "stone", "able", "student", "wood", "enable", "paper", "contact", "steam", "wall", "school", "act", "lay", "own", "interest", "block", "insert", "frame", "window", "door", "manage", "development", "assess", "food", "principle", "people", "give", "human", "instruction", "regard", "assemble", "music", "consumption", "diet", "analysis_algorithm_code_testing", "compile_programming_paradigm", "nutritional", "assign", "description", "topic", "healthy", "vocal", "copy", "score", "animal", "expert", "engineering", "understand", "theory", "welfare", "communication", "market", "report", "time", "device", "quality", "metal", "standard", "conduct", "document", "processing", "construction", "inspect", "compliance", "inspection", "consider", "sport", "mould", "detail", "format", "high_level", "attend", "roll", "final", "practise", "rinsing", "professionalism", "ask", "high", "question", "subject", "introduce", "footwear", "reinforce", "sample", "workpiece", "purpose", "pipe", "time", "press", "model", "concrete", "surface", "produce", "prevent", "understand", "animal", "report", "material", "different", "appropriate", "build", "choose", "bridge", "wind", "organ", "leather", "mosaic", "textile", "tract", "leadpipe", "mouthpiece", "bell", "pedal", "advantage", "bellow", "chest", "arrow", "console", "inlay", "cablochon", "cameo", "faceted", "intaglio", "keyboard", "carve", "aluminium", "sphere", "pipe", "musical_instrument", "synthetic", "piece", "shape", "content", "know", "point", "purpose", "understand", "sensor", "mechanism", "produce", "sample", "composition", "transportation", "route", "surface", "account", "correct", "factor", "animal", "style", "care", "time", "language", "device", "prevent"], "Freq": [5188.0, 4819.0, 2125.0, 2350.0, 1893.0, 1726.0, 1808.0, 1351.0, 2110.0, 1175.0, 1892.0, 1255.0, 1254.0, 1135.0, 1147.0, 1250.0, 1211.0, 1192.0, 1171.0, 1159.0, 1099.0, 1510.0, 977.0, 959.0, 923.0, 761.0, 921.0, 915.0, 911.0, 900.0, 5187.383667140883, 4818.294781654222, 2109.3605662827476, 1890.8832092787088, 1508.4793721927372, 1016.206086203259, 553.0775089252433, 520.6933508677672, 365.415893197454, 225.79631800143474, 207.9913527616008, 162.75543233458637, 134.97215065312938, 131.25613627897476, 124.43942339678294, 122.16377949006264, 116.820716294775, 85.6787298740085, 81.49765152129038, 65.19476318253997, 62.40874077337206, 53.53988221013778, 51.37631039758954, 47.67689677827263, 43.59191144324441, 35.62671579526741, 31.07880350102286, 29.262749889220018, 25.659451143472694, 6.634328601743792, 2348.8915827597184, 992.4943886990771, 802.9008629929992, 578.6765292237222, 453.54976425355204, 421.7597295256841, 391.07082960182623, 361.8420500495086, 337.7116905076762, 328.04777796716724, 327.23151473196384, 280.55487815506405, 279.8464729476066, 262.710320357562, 236.21465357463805, 234.7325710619721, 193.672735622806, 163.58237535027496, 154.76719754233795, 144.7734360211267, 143.7027651861007, 134.97696078663054, 132.15045955200083, 117.96660233858785, 104.71246113936, 101.06537444994471, 90.01391423750418, 84.50804824069614, 80.14512958581038, 73.28832314231369, 1191.1453854318002, 1170.3461296898838, 1157.7571779735788, 938.9436759671448, 722.3584804079482, 639.1351534459768, 516.5237149724132, 492.8320489645705, 485.983213339091, 339.62173786562477, 298.97208431724715, 244.53329242033863, 215.2203311034621, 194.93781094922355, 175.48588366683774, 170.41529479258844, 159.57390183415637, 157.16359767983545, 121.2497848340354, 114.21301354970598, 92.71327502983904, 84.30903050160732, 70.96121986975298, 68.49585845035445, 60.385197783456086, 41.06899695056412, 36.925796251158864, 33.082354059637716, 32.84917882372594, 16.478331991779125, 1248.6077106817254, 915.5598078913088, 835.3784884274351, 477.80810672597147, 436.6974100308328, 413.50851723723173, 400.44561563472143, 338.44237997471254, 317.6818108422279, 313.59335894789285, 290.0602063637073, 274.58820209275507, 247.33594849581496, 242.27649643523188, 241.12316528819395, 225.53839428088943, 220.94935543487858, 188.07129266918142, 187.39855792557213, 177.73540966981577, 116.89707533539614, 113.00752671162441, 97.42423217722089, 78.59684033310896, 62.04411158856445, 61.80645761559096, 46.87880653242781, 45.183269794547655, 29.885187320090182, 24.08519685354064, 1098.010915194508, 713.420661868038, 590.0669543467352, 480.2433099982248, 357.9638106361004, 349.94146238237636, 303.7883127508733, 303.58431409860435, 280.9114555289443, 275.6691484698158, 266.94687380572094, 265.81068799957575, 263.9946843231475, 236.1894761706342, 223.49203445631863, 209.61202584206876, 200.82251546752124, 189.68879696468065, 172.35614135911726, 160.48087603121874, 159.5282605901508, 148.12697593441922, 144.38938098237375, 140.35014904714717, 137.48175995137964, 102.46432724211628, 93.35585136773071, 77.3247707577891, 67.97167946707916, 65.4205239076704, 1892.0075451067855, 909.644994765515, 867.4868144829803, 708.7509427337453, 589.6979148217655, 346.1959743587496, 336.16733276247226, 294.44557580983417, 275.2751224004664, 249.99702463629248, 244.62166333912373, 233.63060474696508, 221.61998740620098, 197.36683044543452, 183.8929754712771, 129.70568980846065, 120.2533879351137, 103.0761001753116, 97.92631680480592, 54.24138297789545, 42.39849499058206, 16.239127267633986, 16.137460082105388, 8.929349983785583, 0.0884561075039895, 0.08848602961567877, 0.08846720147202548, 0.08845989142861946, 0.0884551178621632, 0.08846486458670456, 0.0884650641783334, 0.08847742222668532, 0.08846511407624061, 0.08847717273714928, 0.08846631994233146, 1210.0456880505337, 854.4440171995394, 817.6478469360067, 773.0197421012302, 555.5530076830686, 471.213788384106, 434.3970322148398, 357.3469613167911, 286.9771505039682, 252.1384291843961, 244.8862585508427, 183.69171291440722, 158.78946742186733, 148.64001435905683, 135.68897325183963, 119.0308232426983, 112.58158647944583, 110.78340260082493, 104.4263930345659, 100.15825136404827, 97.72892233009873, 88.47217406173944, 65.6731372551972, 62.701406365716515, 62.29525663829185, 44.205705275106816, 42.05677983741531, 32.67924393425824, 26.459307342387493, 23.02500764211437, 1807.1102688141602, 899.1108859173435, 722.9384882663569, 473.5967724680277, 310.025382964685, 293.0227511627284, 270.12267362253453, 238.01876096568614, 211.25635946635717, 192.2457322778576, 164.33537544459935, 163.56060153301715, 154.97331127310815, 140.25364457461944, 139.58141402677336, 129.6348724462067, 122.29179750888036, 111.90418440874204, 110.46632090126901, 109.36080923074284, 104.98244147581909, 103.34910159306465, 101.76355818341688, 98.55738987283354, 94.68678141129573, 89.21245469239366, 79.58407588551357, 71.45977895968522, 62.585055171333906, 60.3093493013332, 1252.5026563400565, 958.027540718183, 919.3967670773824, 913.9415719653638, 846.2081921859884, 496.1710598187621, 361.1365568524958, 300.8799484506736, 290.88261078584094, 272.9479852514242, 175.88521405012807, 158.9018190333388, 157.70349091642154, 157.48948088109833, 133.00641592201575, 100.83450718407573, 75.7714656695097, 74.39022818533508, 74.19069293295803, 62.04561111289498, 30.617731259844618, 9.68737369400638, 0.08748158136530128, 0.08747034830923807, 0.08748145307915066, 0.08748324908525927, 0.08745533882961613, 0.08746846410640091, 0.08745878651991391, 0.0874765220802364, 0.08748955916029263, 0.08749307901155012, 0.08747776485232049, 0.0874811163280053, 699.5514115279506, 527.2923324293334, 523.4235365582961, 520.6612083724505, 461.4166076665946, 399.1347619803593, 379.6351895424209, 331.7613199898644, 320.0668406233045, 290.48901117879143, 264.93893945776665, 263.9821931212241, 224.63634832077037, 212.8411994110907, 201.04052508942752, 200.8609413290037, 188.3056783365906, 161.76274832719892, 153.56236004123394, 122.30939412178249, 106.29002839586487, 93.85565543572511, 93.13365252795535, 91.3917388517794, 83.31572743214582, 76.68032157452342, 75.95047919334104, 72.22983835046185, 63.92467581717565, 58.86197773242842, 921.9812123124789, 809.3299288586297, 783.4861679558645, 490.22098181283184, 439.32836413627206, 435.04764057555747, 330.47546745346483, 328.5260383761678, 240.95121661038255, 217.06584441013896, 216.37016040671514, 215.2945882937721, 213.30593768467327, 207.25729469560642, 191.74365727647793, 181.6534972725612, 165.3918238329643, 162.67080109991022, 101.40323091833186, 100.5540978853086, 97.82748311030407, 91.97939669759899, 76.28088977006648, 72.23042857762178, 68.16986463535497, 57.41657650760177, 55.96924946217518, 45.60722662321337, 33.493303547895565, 10.597568296588245, 1254.0528459137079, 767.5651727688296, 640.0877635705646, 626.589663684427, 570.0431812563644, 562.0990708757312, 483.9837223083065, 478.92430116994876, 449.8346105493711, 331.2978788616738, 285.2302583364827, 160.23321386414366, 154.00691229492764, 136.6782276350718, 85.6913922959167, 72.86175824172223, 55.979147826422434, 52.21312424968714, 51.09264910613018, 41.52831937384618, 25.37442728294601, 18.394183354334285, 1.2714765937758907, 0.08556533366353514, 0.08558315813440708, 0.08554537898774059, 0.08555830323456792, 0.08554765220995483, 0.08553142351096839, 0.08554193151828986, 0.08554530371548184, 0.08555636121029217, 0.08555185240199306, 0.08556198404802078, 0.08555447940382344, 0.08555776880153079, 773.3776006566411, 568.2074566977749, 499.01177154409567, 484.66313204519446, 474.60294649549417, 381.6790641853195, 355.7513484350593, 353.76353291632444, 333.4230566563372, 331.0763394198175, 290.7565586547783, 253.67971758565255, 229.71266535132787, 222.5271280806339, 204.7983443027266, 167.0071785562617, 152.7289102090241, 140.0644171682333, 119.78692706892022, 109.77275399301922, 83.32727814594651, 78.89864940270608, 71.2777735464117, 47.418463318852204, 44.762892050489384, 33.39096405935837, 31.687209049823526, 29.568116471951274, 25.811234468978295, 18.561865301366595, 1146.153932867075, 644.3675960566954, 605.4853452129485, 505.31786495006895, 450.9116886884215, 427.26311876882045, 395.5462031941457, 393.35396022700274, 366.07821466484046, 264.826087316749, 247.01099696023732, 235.22065365601566, 153.4562785116205, 148.96348746476184, 125.74965140530747, 118.66288962428186, 67.93637681064084, 66.38398289302366, 63.159787020185206, 51.835952303941454, 0.08001066310533632, 0.08002247990192696, 0.08003145502429483, 0.08002013535274553, 0.08000866116935905, 0.08001139535708639, 0.08001375334207975, 0.08001347790793523, 0.0800227150286357, 0.08002675920802602, 0.08003006441776028, 0.08002534844777358, 0.0800168032713874, 0.08003323526937528, 0.08003574776620581, 0.0800178647005297, 0.0800216132920576, 0.08002640315900993, 0.08002170062483513, 1724.8871704932938, 1133.8179403293877, 727.9080183231937, 514.1459125067745, 336.839610647345, 270.2283230726934, 233.02073529325313, 196.73418895551237, 147.09472666787698, 144.6726678020166, 143.12732701009045, 135.26882179833638, 112.04384197329142, 110.0057573588677, 101.83400003282607, 43.528369655490394, 31.043236045138574, 30.551368861020535, 27.062381316466126, 19.993479899979857, 0.08340586250652109, 0.08340357959407438, 0.08339617309971602, 0.08339675031337442, 0.08338770946124172, 0.0833856470573836, 0.08339675679892114, 0.08340114102850629, 0.08338609456010754, 0.0834009788898382, 0.08339912402347525, 0.08341288635362275, 0.08339870894848495, 0.08341112877046065, 0.0834035601374342, 0.0834081194767809, 0.0834009205199177, 0.08340315803353734, 0.08340124479725387, 2123.7506997700584, 976.3625389124248, 566.6942134983688, 413.64607448233386, 228.1791783040743, 205.4217498685599, 199.4295239802056, 168.56455587482202, 137.66942232020597, 115.71927754681978, 107.5859182975228, 99.19559671405446, 98.54681587108173, 74.29075897032067, 70.02709001480532, 69.76311037909643, 57.76253414721546, 52.453897601673816, 32.06872610889067, 25.780568855285598, 20.428273893415746, 19.872894100825967, 15.455574681521629, 8.514804390559084, 5.542059284186434, 5.514868838474691, 1.0465482603649587, 0.08486722552227595, 0.08483542662714898, 0.08483300504298621, 0.08486182064628381, 0.08485909948469884, 0.08486731289902408, 451.99560354506366, 406.79097170126704, 385.67202975305986, 333.6853558656689, 333.6397710919906, 299.54714849188815, 284.4503802136077, 278.6574007273091, 271.463458968286, 218.95212045908949, 199.9090690404045, 189.9800530752522, 172.6025437209975, 170.48032332905254, 149.16614370065403, 143.39169371641063, 134.02740616129134, 116.71871369205901, 102.12614452425017, 99.08906508521733, 95.0390558199264, 83.7539478034074, 82.47351890331355, 80.52875788553403, 70.88583767640539, 69.36610092906997, 64.67018522430713, 61.66776462646192, 50.50608788920678, 47.59980511489549, 672.4173328904473, 630.6000642230395, 510.02603024442317, 509.65258305296703, 446.5361230779014, 388.14986325239994, 349.8158102165216, 328.5870876431562, 222.7863033136472, 187.298388395683, 124.885109928092, 95.5669629374862, 84.460886361387, 56.09664248058427, 54.22713140235304, 49.92517230445695, 40.72453680424172, 38.17856461387469, 30.73709682577914, 29.26012595043605, 25.320775847908113, 20.96649442407023, 8.82684117549656, 0.7029205607859047, 0.08394065056219831, 0.0838894692919342, 0.08390001883546146, 0.08392761814630553, 0.08389473873564554, 0.08389006070573798, 0.08390459030432328, 0.08390641782625755, 0.08390680677407446, 0.0839095560490543, 0.08390492064356504, 713.0917589872384, 555.9903939058232, 457.0656550200522, 333.23061959594264, 305.22842614314465, 299.97860571115336, 282.15655375006224, 215.89378258524457, 182.07035822425226, 168.5185714136092, 132.75509835712987, 116.80808327479401, 114.11739605530828, 109.16621154904317, 69.68213895411102, 68.32550641820592, 44.05771492323542, 44.02302484254042, 30.28386655667059, 9.503585032224622, 0.08347531464270067, 0.08347064194864152, 0.08347120948233293, 0.08349091236031911, 0.08347509708811897, 0.08347446334216357, 0.08347144595470433, 0.08347497412248583, 0.08347413228084359, 0.08348725176800963, 0.08348012922018265, 0.0834937878643555, 0.08347774557867879, 0.08349897133873688, 0.08348162372556998, 0.08348291959416533, 0.08347867255037474, 0.08348936110156263, 0.08348589914604514, 0.08348543566019717, 0.08349013673094087, 0.08349246361907559, 0.08348473570197779, 1350.2516769122446, 1173.7723106065034, 759.4227167686222, 77.00143073176861, 72.59066286604741, 25.020042760063294, 0.08026566600846632, 0.0802614920649107, 0.08026080833320445, 0.08025700010660798, 0.0802664769460714, 0.08025528282697368, 0.08025271485789089, 0.08025271485789089, 0.0802528182126837, 0.08025211858024009, 0.08025367685250084, 0.08025184826770505, 0.08025185621807374, 0.08025281026231501, 0.08025191982102314, 0.08025191187065447, 0.08025191187065447, 0.08025191187065447, 0.08025191187065447, 0.08025191187065447, 0.08025271485789089, 0.08025192777139183, 0.0802549568618579, 0.08025195957286653, 0.08026119790126963, 0.08025458319453006, 0.0802531998303802, 0.08026572961141573, 0.08026481531901784, 0.08026539569593129, 0.08026087193615386, 0.08026255741431347, 0.08027227276483721, 0.08028022313351459, 0.08026417928952365, 0.08025568034540755, 0.08026879050335653, 0.08026303443643411, 0.08025731812135509, 0.08025825626485901, 0.08025859813071214, 0.08026727993330783, 0.08025909900393881, 0.0802625097121014, 0.08026137280938055, 0.08027148567833815, 0.08025876508845436, 0.08026308213864618, 0.08026418723989233, 0.08026158746933483, 0.08026250176173273, 0.08026144436269864], "Total": [5188.0, 4819.0, 2125.0, 2350.0, 1893.0, 1726.0, 1808.0, 1351.0, 2110.0, 1175.0, 1892.0, 1255.0, 1254.0, 1135.0, 1147.0, 1250.0, 1211.0, 1192.0, 1171.0, 1159.0, 1099.0, 1510.0, 977.0, 959.0, 923.0, 761.0, 921.0, 915.0, 911.0, 900.0, 5188.9911661364395, 4819.902280649779, 2110.968065278303, 1892.4907082742643, 1510.0868711882927, 1017.813585198815, 554.6850079207992, 522.3008498633232, 367.0233921930099, 227.40381699699066, 209.59885175715672, 164.3629313301423, 136.5796496486853, 132.86363527453068, 126.04692239233881, 123.77127848561851, 118.42821529033087, 87.28622886956437, 83.10515051684625, 66.80226217809584, 64.01623976892793, 55.147381205693655, 52.983809393145414, 49.2843958051538, 45.19941044504148, 37.23421479082328, 32.68630249657873, 30.870248884775894, 27.266950186970085, 8.241827597299672, 2350.505781125644, 994.1085870650027, 804.5150613589248, 580.2907275896478, 455.16396261947756, 423.37392789160964, 392.68502796775175, 363.4562484154341, 339.32588887360174, 329.66197633309275, 328.84571309788936, 282.16907652098956, 281.46067131353215, 264.32451872348753, 237.82885194056357, 236.3467694278976, 195.2869339887315, 165.19657371620048, 156.38139590826347, 146.38763438705223, 145.3169635520262, 136.59115915255606, 133.76465791792634, 119.58080070451334, 106.32665950528549, 102.67957282592009, 91.62811260342967, 86.12224661381914, 81.75932795173587, 74.90252150823918, 1192.7575798682014, 1171.958324126285, 1159.36937240998, 940.5558704035465, 723.9706748443498, 640.7473478823784, 518.1359094088149, 494.44424340097214, 487.5954077754926, 341.2339323020264, 300.5842787536488, 246.14548685674023, 216.8325255398637, 196.55000538562516, 177.09807810323935, 172.02748922899005, 161.18609628626234, 158.77579211623706, 122.86197927043698, 115.82520798610756, 94.32546947343813, 85.92122494449444, 72.57341432041365, 70.10805288675603, 61.997392219857666, 42.6811913869657, 38.537990687560445, 34.694548496039296, 34.46137326012752, 18.090526428180713, 1250.224483690995, 917.1765809005785, 836.9952614367048, 479.4248797352411, 438.3141830401025, 415.1252902465014, 402.0623886439911, 340.0591529839822, 319.29858385149754, 315.2101319571625, 291.676979372977, 276.2049751020247, 248.95272150508464, 243.89326944450156, 242.73993829746362, 227.1551672901591, 222.56612844414826, 189.6880656784511, 189.0153309348418, 179.35218267908544, 118.5138483446658, 114.62429972089407, 99.04100518649055, 80.21361334237862, 63.6608845978341, 63.42323062486061, 48.49557954169746, 46.80004282768053, 31.501960329359846, 25.70196997277456, 1099.6276888720968, 715.0374355456269, 591.6837280243241, 481.86008367581366, 359.58058431368926, 351.5582360599652, 305.40508642846214, 305.2010877761932, 282.5282292065332, 277.2859221474047, 268.5636474833098, 267.4274616771646, 265.6114580007364, 237.80624984822305, 225.10880813390747, 211.22879952562175, 202.43928914511008, 191.3055706422695, 173.9729150367061, 162.09764970880758, 161.14503426773965, 149.74374961200806, 146.0061546599626, 141.966922724736, 139.09853362896848, 104.08110091970514, 94.97262504531956, 78.94154443537795, 69.58845315186552, 67.03729758525925, 1893.6186730976412, 911.2561227563708, 869.0979424738362, 710.3620707246012, 591.3090428126213, 347.8071023496055, 337.77846075332815, 296.05670380069006, 276.8862503913223, 251.6081526271483, 246.23279132997956, 235.2417327378209, 223.2311153970568, 198.97795843629035, 185.50410346213295, 131.31681779931648, 121.86451592596951, 104.68722816616742, 99.53744479566174, 55.85251096875127, 44.009622990176474, 17.850255460721716, 17.74858839508407, 10.540478001920146, 1.6998475812386697, 1.700424560712273, 1.7000841838977667, 1.6999594204659045, 1.6998719125539181, 1.7000594908279174, 1.7001182659224094, 1.700456570001056, 1.7001524086625714, 1.7006104635919996, 1.7002781489315024, 1211.659137109545, 856.0574662585507, 819.2612959950179, 774.6331911602415, 557.1664567420798, 472.8272374431174, 436.01048127385116, 358.9604103758025, 288.59059956297955, 253.75187824340745, 246.49970760985406, 185.30516197341856, 160.40291648087867, 150.25346341806818, 137.30242231085097, 120.64427230170963, 114.19503553845716, 112.39685165983626, 106.03984209357724, 101.7717004230596, 99.34237138911006, 90.08562312075077, 67.28658632069407, 64.31485542472784, 63.908705697303176, 45.8191543584271, 43.67022891529007, 34.29269299326956, 28.07275641595634, 24.638456701125705, 1808.7212219022915, 900.7218390054746, 724.5494413544881, 475.20772555615895, 311.6363360528162, 294.63370425085964, 271.73362671066576, 239.6297140538174, 212.86731255448842, 193.85668536598885, 165.9463285327306, 165.1715546211484, 156.5842643612394, 141.8645976627507, 141.1923671149046, 131.24582553433797, 123.90275059701159, 113.51513749687327, 112.07727398940024, 110.97176231887407, 106.59339456395031, 104.96005468119587, 103.3745112715481, 100.16834296096476, 96.29773449942695, 90.82340778052489, 81.1950289736448, 73.07073204781645, 64.19600825946513, 61.920302389464425, 1254.1147717581616, 959.639656136288, 921.0088824954873, 915.5536873834687, 847.8203076040934, 497.7831752368671, 362.7486722706008, 302.49206386877864, 292.49472620394596, 274.5601006695292, 177.49732946823315, 160.51393445144387, 159.31560633452662, 159.1015962992034, 134.61853134012082, 102.44662260218077, 77.38358108761473, 76.00234360344011, 75.80280835106306, 63.657726531000016, 32.22984667794966, 11.299489118829328, 1.7000443657628537, 1.6999299693003858, 1.7001581984069711, 1.7002103194047518, 1.6996711950882848, 1.6999594204659045, 1.6997747375934933, 1.7001273367403973, 1.700424560712273, 1.7006104635919996, 1.7002386051066396, 1.700456570001056, 701.1636445352826, 528.9045654366655, 525.0357695656281, 522.2734413797825, 463.02884067392665, 400.7469949876914, 381.24742254975297, 333.37355299719644, 321.67907363063654, 292.1012441861235, 266.5511724650987, 265.59442612855617, 226.24858132810246, 214.4534324184228, 202.6527580967596, 202.4731743363358, 189.9179113439227, 163.37498133453101, 155.17459304856604, 123.92162712911457, 107.90226140319695, 95.46788844305719, 94.74588558927296, 93.00397185911147, 84.9279604394779, 78.29255459052919, 77.56271220067312, 73.84207135779393, 65.53690882450772, 60.474210739760494, 923.598930829191, 810.9476473753418, 785.1038864725766, 491.83870032954405, 440.9460826529843, 436.6653590922697, 332.09318597017705, 330.14375689288, 242.56893512709473, 218.68356292685115, 217.98787892342733, 216.9123068104843, 214.92365620138546, 208.8750132200051, 193.36137579319012, 183.27121578927338, 167.00954234967648, 164.2885196166224, 103.02094943504406, 102.1718164020208, 99.44520162701627, 93.59711521431119, 77.89860828677868, 73.84814709433398, 69.78758316211704, 59.03429502431396, 57.58696797888737, 47.2249451929024, 35.11102206460776, 12.21528681330044, 1255.6668818909918, 769.1792087461134, 641.7017995478484, 628.2036996617109, 571.6572172336482, 563.713106853015, 485.59775828559043, 480.5383371472327, 451.44864652665507, 332.9119148389577, 286.8442943137666, 161.84724984142758, 155.62094827221156, 138.29226361235573, 87.30542828995712, 74.47579424757419, 57.59318380370635, 53.82716022697106, 52.7066850834141, 43.1423553511301, 26.988463260229935, 20.00821933161821, 2.8855125710598175, 1.7002103194047518, 1.7006104635919996, 1.6998815960276181, 1.700174604710301, 1.6999673888336135, 1.699658439566357, 1.6998794441062883, 1.6999528707775688, 1.7002066031428598, 1.7001084838424787, 1.700456570001056, 1.7002054781980576, 1.700424560712273, 774.9904396386255, 569.8202956797593, 500.62461052608006, 486.27597102717885, 476.21578547747856, 383.2919031673039, 357.3641874170437, 355.37637189830883, 335.0358956383216, 332.6891784018019, 292.3693976367627, 255.29255656763695, 231.32550433331227, 224.1399670626183, 206.411183284711, 168.6200175382461, 154.3417491910085, 141.6772561502177, 121.39976605090465, 111.38559297500365, 84.94011712793093, 80.51148838469051, 72.890612536414, 49.03130230083661, 46.37573105366094, 35.00380304134277, 33.30004803180793, 31.180955453935677, 27.424073450962698, 20.174704290893786, 1147.7734937779958, 645.9871569676162, 607.1049061238692, 506.9374258609898, 452.53124959934235, 428.8826796797413, 397.1657641050665, 394.97352113792357, 367.6977755757613, 266.44564822766984, 248.63055787115812, 236.84021456693645, 155.0758394225413, 150.58304837568264, 127.36921231622831, 120.28245055027271, 69.55593772156168, 68.0035438039445, 64.77934793110605, 53.45551321486228, 1.6996751091899347, 1.6999769674584067, 1.700174604710301, 1.6999349086907007, 1.6997097145784301, 1.6997689707276276, 1.6998284300538973, 1.6998261424083545, 1.700025082729722, 1.7001273367403973, 1.7002054781980576, 1.7001084838424787, 1.6999211498102906, 1.700424560712273, 1.7006104635919996, 1.6999673888336135, 1.7001581984069711, 1.700456570001056, 1.7002103194047518, 1726.503351693412, 1135.434121529506, 729.524199523312, 515.7620937068928, 338.4557918474633, 271.84450427281166, 234.63691649337136, 198.3503701556306, 148.71090786799522, 146.28884900213484, 144.7435082102087, 136.8850029984546, 113.6600231734097, 111.62193855898597, 103.45018123294435, 45.144550855608664, 32.65941724525684, 32.1675500611388, 28.678562516584392, 21.609661100098123, 1.6998835470214577, 1.69989581986819, 1.699788589942749, 1.6998341646512294, 1.6996900533028179, 1.6996504277706852, 1.6998770733151929, 1.6999673888336135, 1.6996616840372174, 1.699966354062023, 1.6999349086907007, 1.7002781489315024, 1.6999562333433174, 1.700456570001056, 1.7002054781980576, 1.7006104635919996, 1.7001524086625714, 1.700424560712273, 1.7002287125144626, 2125.365433265551, 977.9772724079176, 568.3089469938615, 415.2608079778266, 229.79391179956698, 207.0364833640526, 201.0442574756983, 170.1792893703147, 139.28415581569865, 117.33401104231248, 109.2006517930155, 100.81033020954716, 100.16154937190248, 75.90549246581337, 71.64182351029802, 71.37784387458913, 59.37726764270816, 54.06863109716652, 33.68345960438337, 27.395302350778298, 22.043007427846536, 21.487627649503676, 17.07030817701433, 10.129537886051786, 7.156792779679133, 7.129614442470395, 2.66128175585766, 1.7002287125144626, 1.6996445456996214, 1.6996069981795705, 1.7002049447865692, 1.7001581984069711, 1.7006104635919996, 453.61366641682446, 408.40903457302784, 387.29009262482066, 335.3034187374297, 335.2578339637514, 301.16521136364895, 286.0684430853685, 280.2754635990699, 273.0815218400468, 220.5701833308503, 201.52713191216532, 191.598115947013, 174.22060659275832, 172.09838620081337, 150.78420657241486, 145.00975658817146, 135.64546903305217, 118.33677656381983, 103.74420740416984, 100.70712795697814, 96.65711869168722, 85.37201068348453, 84.09158178131555, 82.14682075729485, 72.5039005481662, 70.98416380083079, 66.28824809606795, 63.28582749822273, 52.124150760967595, 49.2178679866563, 674.0330107493762, 632.2157420819684, 511.6417081033522, 511.26826091189605, 448.1518009368304, 389.76554111132896, 351.4314880754506, 330.20276550208524, 224.40198117257623, 188.914066254612, 126.50078778702103, 97.18264079641521, 86.07656422031602, 57.71232033951329, 55.842809261282056, 51.54085016338597, 42.34021466317074, 39.79424247280371, 32.352774692235386, 30.87580380936507, 26.936453706837135, 22.582172290526476, 10.442519040911128, 2.3185984197149248, 1.7006104635919996, 1.6996711950882848, 1.69989581986819, 1.700456570001056, 1.6998049695172888, 1.6997120852253125, 1.7001182659224094, 1.700174604710301, 1.7002103194047518, 1.700424560712273, 1.7002781489315024, 714.7078512532404, 557.6064861718252, 458.6817472860542, 334.84671186194464, 306.84451840914664, 301.59469797715536, 283.77264601606424, 217.5098748512466, 183.6864504902543, 170.13466367961124, 134.3711906231319, 118.42417554079603, 115.73348832131029, 110.78230381504518, 71.29823122011304, 69.94159868420793, 45.673807195201576, 45.63911710854243, 31.899958822672602, 11.119677441863962, 1.6996514642652665, 1.6995835580145595, 1.6995985924205188, 1.7000006920869537, 1.699681409278928, 1.6996868572871473, 1.6996315904618364, 1.6997054581964175, 1.6996887725577239, 1.6999672869861646, 1.6998406896747473, 1.7002066031428598, 1.6997929892911137, 1.700424560712273, 1.6999181668536936, 1.6999673888336135, 1.6998264181850813, 1.7002287125144626, 1.7001701473630955, 1.7001581984069711, 1.700456570001056, 1.7006104635919996, 1.7002103194047518, 1351.8709869755678, 1175.3916206698266, 761.0420268319453, 78.6207407950918, 74.20997292937061, 26.639352836732424, 1.6997351557554334, 1.6997000072549604, 1.6997228463666438, 1.6996501833480715, 1.699857232401751, 1.6996275270914925, 1.6995767756208333, 1.6995767756208333, 1.6995839879893613, 1.699576726495354, 1.699612039676235, 1.6995754287771438, 1.6995760870070757, 1.6995965125247434, 1.6995790645326183, 1.6995909739880721, 1.6995909739880721, 1.6995909739880721, 1.6995909739880721, 1.6995909739880721, 1.6996081822857776, 1.6995917388576958, 1.6996574647963656, 1.699594411626024, 1.6997929892911137, 1.6996515929474587, 1.699621959263361, 1.6999285523556227, 1.6999075318578172, 1.6999528707775688, 1.6998339324248808, 1.6998822931843363, 1.7002066031428598, 1.700456570001056, 1.699966354062023, 1.6997021845945257, 1.7001701473630955, 1.6999672869861646, 1.6997622917388655, 1.6998057726181055, 1.6998372406486364, 1.7002287125144626, 1.6998831088864526, 1.7000594908279174, 1.7000049833538557, 1.7006104635919996, 1.6998760416154979, 1.7002386051066396, 1.700424560712273, 1.7001524086625714, 1.7002781489315024, 1.7001581984069711], "Category": ["Default", "Default", "Default", "Default", "Default", "Default", "Default", "Default", "Default", "Default", "Default", "Default", "Default", "Default", "Default", "Default", "Default", "Default", "Default", "Default", "Default", "Default", "Default", "Default", "Default", "Default", "Default", "Default", "Default", "Default", "Topic1", "Topic1", "Topic1", "Topic1", "Topic1", "Topic1", "Topic1", "Topic1", "Topic1", "Topic1", "Topic1", "Topic1", "Topic1", "Topic1", "Topic1", "Topic1", "Topic1", "Topic1", "Topic1", "Topic1", "Topic1", "Topic1", "Topic1", "Topic1", "Topic1", "Topic1", "Topic1", "Topic1", "Topic1", "Topic1", "Topic2", "Topic2", "Topic2", "Topic2", "Topic2", "Topic2", "Topic2", "Topic2", "Topic2", "Topic2", "Topic2", "Topic2", "Topic2", "Topic2", "Topic2", "Topic2", "Topic2", "Topic2", "Topic2", "Topic2", "Topic2", "Topic2", "Topic2", "Topic2", "Topic2", "Topic2", "Topic2", "Topic2", "Topic2", "Topic2", "Topic3", "Topic3", "Topic3", "Topic3", "Topic3", "Topic3", "Topic3", "Topic3", "Topic3", "Topic3", "Topic3", "Topic3", "Topic3", "Topic3", "Topic3", "Topic3", "Topic3", "Topic3", "Topic3", "Topic3", "Topic3", "Topic3", "Topic3", "Topic3", "Topic3", "Topic3", "Topic3", "Topic3", "Topic3", "Topic3", "Topic4", "Topic4", "Topic4", "Topic4", "Topic4", "Topic4", "Topic4", "Topic4", "Topic4", "Topic4", "Topic4", "Topic4", "Topic4", "Topic4", "Topic4", "Topic4", "Topic4", "Topic4", "Topic4", "Topic4", "Topic4", "Topic4", "Topic4", "Topic4", "Topic4", "Topic4", "Topic4", "Topic4", "Topic4", "Topic4", "Topic5", "Topic5", "Topic5", "Topic5", "Topic5", "Topic5", "Topic5", "Topic5", "Topic5", "Topic5", "Topic5", "Topic5", "Topic5", "Topic5", "Topic5", "Topic5", "Topic5", "Topic5", "Topic5", "Topic5", "Topic5", "Topic5", "Topic5", "Topic5", "Topic5", "Topic5", "Topic5", "Topic5", "Topic5", "Topic5", "Topic6", "Topic6", "Topic6", "Topic6", "Topic6", "Topic6", "Topic6", "Topic6", "Topic6", "Topic6", "Topic6", "Topic6", "Topic6", "Topic6", "Topic6", "Topic6", "Topic6", "Topic6", "Topic6", "Topic6", "Topic6", "Topic6", "Topic6", "Topic6", "Topic6", "Topic6", "Topic6", "Topic6", "Topic6", "Topic6", "Topic6", "Topic6", "Topic6", "Topic6", "Topic6", "Topic7", "Topic7", "Topic7", "Topic7", "Topic7", "Topic7", "Topic7", "Topic7", "Topic7", "Topic7", "Topic7", "Topic7", "Topic7", "Topic7", "Topic7", "Topic7", "Topic7", "Topic7", "Topic7", "Topic7", "Topic7", "Topic7", "Topic7", "Topic7", "Topic7", "Topic7", "Topic7", "Topic7", "Topic7", "Topic7", "Topic8", "Topic8", "Topic8", "Topic8", "Topic8", "Topic8", "Topic8", "Topic8", "Topic8", "Topic8", "Topic8", "Topic8", "Topic8", "Topic8", "Topic8", "Topic8", "Topic8", "Topic8", "Topic8", "Topic8", "Topic8", "Topic8", "Topic8", "Topic8", "Topic8", "Topic8", "Topic8", "Topic8", "Topic8", "Topic8", "Topic9", "Topic9", "Topic9", "Topic9", "Topic9", "Topic9", "Topic9", "Topic9", "Topic9", "Topic9", "Topic9", "Topic9", "Topic9", "Topic9", "Topic9", "Topic9", "Topic9", "Topic9", "Topic9", "Topic9", "Topic9", "Topic9", "Topic9", "Topic9", "Topic9", "Topic9", "Topic9", "Topic9", "Topic9", "Topic9", "Topic9", "Topic9", "Topic9", "Topic9", "Topic10", "Topic10", "Topic10", "Topic10", "Topic10", "Topic10", "Topic10", "Topic10", "Topic10", "Topic10", "Topic10", "Topic10", "Topic10", "Topic10", "Topic10", "Topic10", "Topic10", "Topic10", "Topic10", "Topic10", "Topic10", "Topic10", "Topic10", "Topic10", "Topic10", "Topic10", "Topic10", "Topic10", "Topic10", "Topic10", "Topic11", "Topic11", "Topic11", "Topic11", "Topic11", "Topic11", "Topic11", "Topic11", "Topic11", "Topic11", "Topic11", "Topic11", "Topic11", "Topic11", "Topic11", "Topic11", "Topic11", "Topic11", "Topic11", "Topic11", "Topic11", "Topic11", "Topic11", "Topic11", "Topic11", "Topic11", "Topic11", "Topic11", "Topic11", "Topic11", "Topic12", "Topic12", "Topic12", "Topic12", "Topic12", "Topic12", "Topic12", "Topic12", "Topic12", "Topic12", "Topic12", "Topic12", "Topic12", "Topic12", "Topic12", "Topic12", "Topic12", "Topic12", "Topic12", "Topic12", "Topic12", "Topic12", "Topic12", "Topic12", "Topic12", "Topic12", "Topic12", "Topic12", "Topic12", "Topic12", "Topic12", "Topic12", "Topic12", "Topic12", "Topic12", "Topic12", "Topic13", "Topic13", "Topic13", "Topic13", "Topic13", "Topic13", "Topic13", "Topic13", "Topic13", "Topic13", "Topic13", "Topic13", "Topic13", "Topic13", "Topic13", "Topic13", "Topic13", "Topic13", "Topic13", "Topic13", "Topic13", "Topic13", "Topic13", "Topic13", "Topic13", "Topic13", "Topic13", "Topic13", "Topic13", "Topic13", "Topic14", "Topic14", "Topic14", "Topic14", "Topic14", "Topic14", "Topic14", "Topic14", "Topic14", "Topic14", "Topic14", "Topic14", "Topic14", "Topic14", "Topic14", "Topic14", "Topic14", "Topic14", "Topic14", "Topic14", "Topic14", "Topic14", "Topic14", "Topic14", "Topic14", "Topic14", "Topic14", "Topic14", "Topic14", "Topic14", "Topic14", "Topic14", "Topic14", "Topic14", "Topic14", "Topic14", "Topic14", "Topic14", "Topic14", "Topic15", "Topic15", "Topic15", "Topic15", "Topic15", "Topic15", "Topic15", "Topic15", "Topic15", "Topic15", "Topic15", "Topic15", "Topic15", "Topic15", "Topic15", "Topic15", "Topic15", "Topic15", "Topic15", "Topic15", "Topic15", "Topic15", "Topic15", "Topic15", "Topic15", "Topic15", "Topic15", "Topic15", "Topic15", "Topic15", "Topic15", "Topic15", "Topic15", "Topic15", "Topic15", "Topic15", "Topic15", "Topic15", "Topic15", "Topic16", "Topic16", "Topic16", "Topic16", "Topic16", "Topic16", "Topic16", "Topic16", "Topic16", "Topic16", "Topic16", "Topic16", "Topic16", "Topic16", "Topic16", "Topic16", "Topic16", "Topic16", "Topic16", "Topic16", "Topic16", "Topic16", "Topic16", "Topic16", "Topic16", "Topic16", "Topic16", "Topic16", "Topic16", "Topic16", "Topic16", "Topic16", "Topic16", "Topic17", "Topic17", "Topic17", "Topic17", "Topic17", "Topic17", "Topic17", "Topic17", "Topic17", "Topic17", "Topic17", "Topic17", "Topic17", "Topic17", "Topic17", "Topic17", "Topic17", "Topic17", "Topic17", "Topic17", "Topic17", "Topic17", "Topic17", "Topic17", "Topic17", "Topic17", "Topic17", "Topic17", "Topic17", "Topic17", "Topic18", "Topic18", "Topic18", "Topic18", "Topic18", "Topic18", "Topic18", "Topic18", "Topic18", "Topic18", "Topic18", "Topic18", "Topic18", "Topic18", "Topic18", "Topic18", "Topic18", "Topic18", "Topic18", "Topic18", "Topic18", "Topic18", "Topic18", "Topic18", "Topic18", "Topic18", "Topic18", "Topic18", "Topic18", "Topic18", "Topic18", "Topic18", "Topic18", "Topic18", "Topic18", "Topic19", "Topic19", "Topic19", "Topic19", "Topic19", "Topic19", "Topic19", "Topic19", "Topic19", "Topic19", "Topic19", "Topic19", "Topic19", "Topic19", "Topic19", "Topic19", "Topic19", "Topic19", "Topic19", "Topic19", "Topic19", "Topic19", "Topic19", "Topic19", "Topic19", "Topic19", "Topic19", "Topic19", "Topic19", "Topic19", "Topic19", "Topic19", "Topic19", "Topic19", "Topic19", "Topic19", "Topic19", "Topic19", "Topic19", "Topic19", "Topic19", "Topic19", "Topic19", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20", "Topic20"], "logprob": [30.0, 29.0, 28.0, 27.0, 26.0, 25.0, 24.0, 23.0, 22.0, 21.0, 20.0, 19.0, 18.0, 17.0, 16.0, 15.0, 14.0, 13.0, 12.0, 11.0, 10.0, 9.0, 8.0, 7.0, 6.0, 5.0, 4.0, 3.0, 2.0, 1.0, -1.3916, -1.4654, -2.2915, -2.4008, -2.6267, -3.0218, -3.6301, -3.6904, -4.0446, -4.526, -4.6081, -4.8533, -5.0405, -5.0684, -5.1218, -5.1402, -5.185, -5.495, -5.545, -5.7682, -5.8119, -5.9652, -6.0064, -6.0811, -6.1707, -6.3725, -6.5091, -6.5693, -6.7007, -8.0533, -1.6124, -2.4739, -2.6858, -3.0133, -3.257, -3.3296, -3.4052, -3.4829, -3.5519, -3.5809, -3.5834, -3.7373, -3.7398, -3.803, -3.9093, -3.9156, -4.1079, -4.2768, -4.3322, -4.3989, -4.4063, -4.469, -4.4901, -4.6037, -4.7229, -4.7583, -4.8741, -4.9372, -4.9902, -5.0797, -2.2275, -2.2451, -2.2559, -2.4654, -2.7277, -2.8501, -3.0631, -3.11, -3.124, -3.4823, -3.6098, -3.8108, -3.9385, -4.0375, -4.1426, -4.1719, -4.2377, -4.2529, -4.5123, -4.5721, -4.7807, -4.8757, -5.048, -5.0834, -5.2094, -5.5949, -5.7013, -5.8112, -5.8183, -6.5081, -2.0406, -2.3508, -2.4425, -3.0012, -3.0911, -3.1457, -3.1778, -3.346, -3.4093, -3.4223, -3.5003, -3.5551, -3.6596, -3.6803, -3.6851, -3.7519, -3.7724, -3.9335, -3.9371, -3.9901, -4.4091, -4.4429, -4.5913, -4.806, -5.0425, -5.0464, -5.3228, -5.3596, -5.773, -5.9888, -2.1617, -2.5928, -2.7827, -2.9886, -3.2825, -3.3052, -3.4466, -3.4473, -3.5249, -3.5437, -3.5759, -3.5801, -3.587, -3.6983, -3.7535, -3.8177, -3.8605, -3.9175, -4.0134, -4.0847, -4.0907, -4.1648, -4.1904, -4.2188, -4.2394, -4.5334, -4.6265, -4.8149, -4.9438, -4.9821, -1.5754, -2.3078, -2.3552, -2.5573, -2.7412, -3.2738, -3.3032, -3.4357, -3.5031, -3.5994, -3.6211, -3.6671, -3.7199, -3.8358, -3.9065, -4.2556, -4.3312, -4.4854, -4.5366, -5.1274, -5.3737, -6.3334, -6.3397, -6.9315, -11.5461, -11.5458, -11.546, -11.546, -11.5461, -11.546, -11.546, -11.5458, -11.546, -11.5459, -11.546, -2.0033, -2.3513, -2.3953, -2.4514, -2.7818, -2.9464, -3.0278, -3.223, -3.4423, -3.5717, -3.6009, -3.8885, -4.0341, -4.1002, -4.1914, -4.3223, -4.378, -4.3941, -4.4532, -4.495, -4.5195, -4.619, -4.917, -4.9633, -4.9698, -5.3129, -5.3627, -5.615, -5.8261, -5.9651, -1.5878, -2.2859, -2.504, -2.927, -3.3507, -3.4071, -3.4884, -3.615, -3.7342, -3.8285, -3.9854, -3.9901, -4.0441, -4.1439, -4.1487, -4.2226, -4.2809, -4.3697, -4.3826, -4.3927, -4.4335, -4.4492, -4.4647, -4.4967, -4.5367, -4.5963, -4.7105, -4.8182, -4.9508, -4.9878, -1.9514, -2.2194, -2.2606, -2.2665, -2.3435, -2.8774, -3.195, -3.3776, -3.4114, -3.475, -3.9145, -4.016, -4.0236, -4.0249, -4.1939, -4.4708, -4.7566, -4.775, -4.7777, -4.9564, -5.6627, -6.8135, -11.5206, -11.5208, -11.5206, -11.5206, -11.5209, -11.5208, -11.5209, -11.5207, -11.5205, -11.5205, -11.5207, -11.5206, -2.4916, -2.7743, -2.7817, -2.787, -2.9078, -3.0528, -3.1029, -3.2377, -3.2736, -3.3705, -3.4626, -3.4662, -3.6276, -3.6815, -3.7386, -3.7395, -3.804, -3.956, -4.008, -4.2355, -4.3759, -4.5003, -4.508, -4.5269, -4.6194, -4.7024, -4.712, -4.7622, -4.8844, -4.9669, -2.1967, -2.327, -2.3595, -2.8284, -2.938, -2.9478, -3.2227, -3.2286, -3.5386, -3.643, -3.6462, -3.6512, -3.6605, -3.6893, -3.7671, -3.8211, -3.9149, -3.9315, -4.4041, -4.4125, -4.44, -4.5016, -4.6888, -4.7434, -4.8012, -4.9729, -4.9984, -5.2031, -5.5119, -6.6626, -1.887, -2.3779, -2.5595, -2.5809, -2.6754, -2.6895, -2.8391, -2.8496, -2.9123, -3.2181, -3.3679, -3.9445, -3.9842, -4.1035, -4.5704, -4.7326, -4.9962, -5.0658, -5.0875, -5.2948, -5.7874, -6.1091, -8.781, -11.4796, -11.4794, -11.4799, -11.4797, -11.4798, -11.48, -11.4799, -11.4799, -11.4797, -11.4798, -11.4797, -11.4797, -11.4797, -2.3256, -2.6339, -2.7637, -2.7929, -2.8139, -3.0318, -3.1021, -3.1077, -3.1669, -3.174, -3.3039, -3.4403, -3.5395, -3.5713, -3.6543, -3.8583, -3.9477, -4.0343, -4.1906, -4.2779, -4.5536, -4.6082, -4.7098, -5.1173, -5.175, -5.4681, -5.5204, -5.5897, -5.7255, -6.0552, -1.8632, -2.4391, -2.5014, -2.6822, -2.7961, -2.85, -2.9271, -2.9327, -3.0046, -3.3283, -3.398, -3.4469, -3.874, -3.9037, -4.0731, -4.1311, -4.6888, -4.7119, -4.7617, -4.9593, -11.433, -11.4328, -11.4327, -11.4329, -11.433, -11.433, -11.433, -11.433, -11.4328, -11.4328, -11.4328, -11.4328, -11.4329, -11.4327, -11.4327, -11.4329, -11.4329, -11.4328, -11.4329, -1.4193, -1.8389, -2.282, -2.6297, -3.0526, -3.2729, -3.4211, -3.5903, -3.8811, -3.8977, -3.9085, -3.9649, -4.1533, -4.1717, -4.2489, -5.0988, -5.4368, -5.4528, -5.5741, -5.8768, -11.3562, -11.3563, -11.3564, -11.3563, -11.3565, -11.3565, -11.3563, -11.3563, -11.3565, -11.3563, -11.3563, -11.3561, -11.3563, -11.3562, -11.3563, -11.3562, -11.3563, -11.3563, -11.3563, -1.1729, -1.95, -2.494, -2.8088, -3.4037, -3.5087, -3.5383, -3.7065, -3.9089, -4.0826, -4.1555, -4.2367, -4.2433, -4.5258, -4.5849, -4.5887, -4.7775, -4.8739, -5.3659, -5.5842, -5.8169, -5.8444, -6.0958, -6.692, -7.1214, -7.1263, -8.7883, -11.3005, -11.3008, -11.3009, -11.3005, -11.3006, -11.3005, -2.6747, -2.7801, -2.8334, -2.9782, -2.9783, -3.0861, -3.1378, -3.1584, -3.1846, -3.3995, -3.4905, -3.5415, -3.6374, -3.6498, -3.7833, -3.8228, -3.8903, -4.0286, -4.1622, -4.1924, -4.2341, -4.3605, -4.3759, -4.3998, -4.5273, -4.549, -4.6191, -4.6666, -4.8663, -4.9256, -2.1647, -2.2289, -2.4411, -2.4419, -2.5741, -2.7142, -2.8182, -2.8808, -3.2694, -3.4429, -3.8482, -4.1158, -4.2393, -4.6485, -4.6824, -4.7651, -4.9688, -5.0333, -5.2501, -5.2994, -5.444, -5.6327, -6.4978, -9.0281, -11.1533, -11.1539, -11.1537, -11.1534, -11.1538, -11.1539, -11.1537, -11.1537, -11.1537, -11.1536, -11.1537, -1.9868, -2.2357, -2.4316, -2.7476, -2.8354, -2.8527, -2.914, -3.1816, -3.352, -3.4294, -3.6679, -3.7959, -3.8192, -3.8636, -4.3125, -4.3321, -4.7709, -4.7717, -5.1458, -6.3048, -11.0396, -11.0397, -11.0397, -11.0394, -11.0396, -11.0396, -11.0397, -11.0396, -11.0396, -11.0395, -11.0396, -11.0394, -11.0396, -11.0394, -11.0396, -11.0395, -11.0396, -11.0395, -11.0395, -11.0395, -11.0395, -11.0394, -11.0395, -1.1746, -1.3147, -1.7501, -4.0389, -4.0979, -5.163, -10.9051, -10.9052, -10.9052, -10.9052, -10.9051, -10.9052, -10.9053, -10.9053, -10.9053, -10.9053, -10.9053, -10.9053, -10.9053, -10.9053, -10.9053, -10.9053, -10.9053, -10.9053, -10.9053, -10.9053, -10.9053, -10.9053, -10.9052, -10.9053, -10.9052, -10.9052, -10.9053, -10.9051, -10.9051, -10.9051, -10.9052, -10.9051, -10.905, -10.9049, -10.9051, -10.9052, -10.9051, -10.9051, -10.9052, -10.9052, -10.9052, -10.9051, -10.9052, -10.9051, -10.9052, -10.905, -10.9052, -10.9051, -10.9051, -10.9052, -10.9051, -10.9052], "loglift": [30.0, 29.0, 28.0, 27.0, 26.0, 25.0, 24.0, 23.0, 22.0, 21.0, 20.0, 19.0, 18.0, 17.0, 16.0, 15.0, 14.0, 13.0, 12.0, 11.0, 10.0, 9.0, 8.0, 7.0, 6.0, 5.0, 4.0, 3.0, 2.0, 1.0, 2.1261, 2.1261, 2.1257, 2.1256, 2.1254, 2.1248, 2.1235, 2.1233, 2.122, 2.1193, 2.1187, 2.1166, 2.1146, 2.1143, 2.1136, 2.1134, 2.1128, 2.1078, 2.1069, 2.1021, 2.101, 2.0968, 2.0956, 2.0933, 2.0902, 2.0823, 2.076, 2.0729, 2.0657, 1.9095, 2.6973, 2.6963, 2.6959, 2.6952, 2.6944, 2.6941, 2.6938, 2.6935, 2.6932, 2.693, 2.693, 2.6922, 2.6922, 2.6918, 2.6911, 2.6911, 2.6896, 2.6881, 2.6876, 2.6868, 2.6868, 2.686, 2.6858, 2.6843, 2.6826, 2.6821, 2.6802, 2.679, 2.678, 2.6762, 2.7605, 2.7605, 2.7604, 2.7601, 2.7596, 2.7593, 2.7587, 2.7586, 2.7585, 2.7571, 2.7565, 2.7553, 2.7544, 2.7536, 2.7527, 2.7524, 2.7518, 2.7516, 2.7486, 2.7478, 2.7446, 2.7429, 2.7394, 2.7386, 2.7355, 2.7233, 2.7191, 2.7143, 2.7139, 2.6685, 2.9004, 2.8999, 2.8997, 2.8983, 2.8979, 2.8977, 2.8976, 2.8969, 2.8966, 2.8965, 2.8961, 2.8958, 2.8951, 2.895, 2.895, 2.8945, 2.8944, 2.8931, 2.8931, 2.8926, 2.8879, 2.8874, 2.8852, 2.8813, 2.8759, 2.8758, 2.8677, 2.8665, 2.849, 2.8367, 2.9076, 2.9068, 2.9064, 2.9057, 2.9046, 2.9045, 2.9038, 2.9038, 2.9034, 2.9033, 2.9031, 2.903, 2.903, 2.9023, 2.9019, 2.9014, 2.9011, 2.9006, 2.8998, 2.8991, 2.899, 2.8982, 2.898, 2.8976, 2.8974, 2.8934, 2.8919, 2.8884, 2.8856, 2.8847, 2.9503, 2.9494, 2.9493, 2.9489, 2.9484, 2.9465, 2.9464, 2.9457, 2.9453, 2.9448, 2.9446, 2.9443, 2.9439, 2.943, 2.9425, 2.9388, 2.9379, 2.9357, 2.9349, 2.9219, 2.9139, 2.8566, 2.856, 2.7853, -0.0046, -0.0046, -0.0046, -0.0046, -0.0046, -0.0046, -0.0047, -0.0047, -0.0047, -0.0048, -0.0048, 2.969, 2.9684, 2.9683, 2.9682, 2.9674, 2.9669, 2.9666, 2.9658, 2.9647, 2.9639, 2.9637, 2.9615, 2.9602, 2.9595, 2.9585, 2.9568, 2.9561, 2.9558, 2.955, 2.9543, 2.9539, 2.9522, 2.946, 2.9449, 2.9447, 2.9344, 2.9326, 2.9221, 2.9111, 2.9026, 2.9838, 2.9829, 2.9825, 2.9813, 2.9795, 2.9792, 2.9788, 2.978, 2.9771, 2.9764, 2.975, 2.9749, 2.9744, 2.9733, 2.9732, 2.9724, 2.9716, 2.9704, 2.9702, 2.9701, 2.9695, 2.9692, 2.969, 2.9685, 2.9678, 2.9668, 2.9647, 2.9624, 2.9593, 2.9583, 2.9864, 2.986, 2.986, 2.986, 2.9858, 2.9845, 2.9833, 2.9824, 2.9822, 2.9818, 2.9786, 2.9776, 2.9775, 2.9775, 2.9757, 2.9719, 2.9667, 2.9663, 2.9662, 2.9621, 2.9364, 2.8338, 0.0207, 0.0207, 0.0207, 0.0207, 0.0207, 0.0206, 0.0206, 0.0206, 0.0206, 0.0205, 0.0206, 0.0205, 3.0276, 3.0269, 3.0269, 3.0268, 3.0264, 3.0259, 3.0257, 3.0251, 3.0249, 3.0244, 3.0239, 3.0238, 3.0228, 3.0224, 3.0219, 3.0219, 3.0214, 3.02, 3.0195, 3.0168, 3.0149, 3.0129, 3.0128, 3.0124, 3.0108, 3.0091, 3.0089, 3.0079, 3.005, 3.0029, 3.047, 3.0468, 3.0467, 3.0455, 3.0451, 3.0451, 3.0439, 3.0439, 3.0421, 3.0414, 3.0414, 3.0413, 3.0412, 3.041, 3.0404, 3.0399, 3.0391, 3.0389, 3.033, 3.0328, 3.0324, 3.0314, 3.0278, 3.0267, 3.0253, 3.021, 3.0203, 3.0139, 3.0016, 2.9067, 3.0496, 3.0488, 3.0483, 3.0483, 3.048, 3.048, 3.0475, 3.0475, 3.0473, 3.046, 3.0452, 3.0408, 3.0404, 3.0391, 3.0322, 3.029, 3.0224, 3.0204, 3.0198, 3.0127, 2.9892, 2.9668, 2.2313, 0.0616, 0.0616, 0.0616, 0.0616, 0.0616, 0.0616, 0.0616, 0.0616, 0.0615, 0.0615, 0.0615, 0.0615, 0.0614, 3.0936, 3.0928, 3.0924, 3.0923, 3.0923, 3.0914, 3.0911, 3.0911, 3.0908, 3.0908, 3.0901, 3.0893, 3.0887, 3.0884, 3.0878, 3.086, 3.0852, 3.0842, 3.0823, 3.0811, 3.0765, 3.0754, 3.0733, 3.0622, 3.0603, 3.0485, 3.046, 3.0425, 3.035, 3.0123, 3.1632, 3.1621, 3.1619, 3.1614, 3.161, 3.1608, 3.1605, 3.1605, 3.1602, 3.1585, 3.1581, 3.1578, 3.1541, 3.1538, 3.1518, 3.1511, 3.1411, 3.1405, 3.1393, 3.1339, 0.1086, 0.1086, 0.1086, 0.1086, 0.1085, 0.1085, 0.1085, 0.1085, 0.1085, 0.1085, 0.1085, 0.1085, 0.1085, 0.1084, 0.1083, 0.1085, 0.1084, 0.1083, 0.1084, 3.1989, 3.1984, 3.1976, 3.1967, 3.195, 3.1939, 3.1929, 3.1916, 3.1889, 3.1887, 3.1886, 3.1879, 3.1855, 3.1852, 3.1841, 3.1634, 3.1491, 3.1483, 3.1418, 3.1221, 0.1852, 0.1852, 0.1852, 0.1851, 0.1851, 0.1851, 0.1851, 0.1851, 0.1851, 0.1851, 0.1851, 0.1851, 0.1851, 0.1849, 0.185, 0.1848, 0.185, 0.1849, 0.185, 3.2375, 3.2366, 3.2354, 3.2343, 3.2312, 3.2304, 3.2302, 3.2287, 3.2266, 3.2244, 3.2233, 3.2221, 3.222, 3.2167, 3.2154, 3.2153, 3.2107, 3.2079, 3.1891, 3.1775, 3.1621, 3.1601, 3.1389, 3.0646, 2.9825, 2.9814, 2.3049, 0.2408, 0.2408, 0.2408, 0.2407, 0.2407, 0.2406, 3.2801, 3.2797, 3.2794, 3.2788, 3.2788, 3.2782, 3.278, 3.2778, 3.2777, 3.2763, 3.2756, 3.2751, 3.2743, 3.2742, 3.2728, 3.2724, 3.2716, 3.2699, 3.2679, 3.2674, 3.2667, 3.2645, 3.2642, 3.2637, 3.2611, 3.2606, 3.2589, 3.2577, 3.2521, 3.2502, 3.394, 3.3938, 3.3932, 3.3932, 3.3928, 3.3923, 3.3918, 3.3915, 3.3892, 3.3878, 3.3836, 3.3796, 3.3775, 3.368, 3.367, 3.3646, 3.3575, 3.355, 3.3452, 3.3427, 3.3346, 3.3222, 3.2283, 2.2029, 0.3878, 0.3877, 0.3877, 0.3877, 0.3877, 0.3877, 0.3876, 0.3876, 0.3876, 0.3875, 0.3875, 3.5133, 3.5127, 3.5121, 3.5107, 3.5103, 3.5102, 3.5099, 3.5081, 3.5067, 3.506, 3.5035, 3.5018, 3.5015, 3.5009, 3.4927, 3.4922, 3.4796, 3.4795, 3.4636, 3.3585, 0.502, 0.5019, 0.5019, 0.5019, 0.5019, 0.5019, 0.5019, 0.5019, 0.5019, 0.5019, 0.5019, 0.5019, 0.5019, 0.5018, 0.5019, 0.5019, 0.5019, 0.5018, 0.5018, 0.5018, 0.5017, 0.5016, 0.5017, 3.6881, 3.6879, 3.6872, 3.6685, 3.6673, 3.6266, 0.6364, 0.6364, 0.6364, 0.6364, 0.6364, 0.6364, 0.6364, 0.6364, 0.6364, 0.6364, 0.6364, 0.6364, 0.6364, 0.6364, 0.6364, 0.6364, 0.6364, 0.6364, 0.6364, 0.6364, 0.6364, 0.6364, 0.6363, 0.6363, 0.6363, 0.6363, 0.6363, 0.6363, 0.6363, 0.6363, 0.6363, 0.6363, 0.6362, 0.6362, 0.6363, 0.6363, 0.6362, 0.6363, 0.6363, 0.6363, 0.6363, 0.6362, 0.6363, 0.6362, 0.6362, 0.636, 0.6363, 0.6361, 0.636, 0.6361, 0.6361, 0.6361]}, "token.table": {"Topic": [17, 3, 14, 5, 2, 17, 11, 5, 7, 9, 13, 12, 11, 8, 6, 16, 13, 5, 6, 4, 12, 18, 11, 9, 15, 16, 20, 1, 7, 8, 12, 12, 11, 2, 18, 18, 11, 18, 5, 11, 19, 11, 14, 1, 12, 11, 13, 4, 11, 17, 14, 10, 1, 8, 20, 9, 1, 20, 10, 8, 5, 17, 1, 10, 17, 4, 7, 10, 6, 20, 6, 1, 3, 9, 3, 2, 13, 4, 4, 11, 4, 14, 18, 19, 7, 10, 16, 13, 16, 13, 4, 10, 4, 11, 13, 19, 10, 19, 7, 7, 19, 18, 17, 5, 2, 7, 5, 13, 7, 5, 3, 18, 16, 2, 12, 16, 16, 8, 12, 16, 14, 14, 4, 9, 17, 8, 9, 15, 14, 14, 7, 1, 9, 7, 15, 5, 15, 18, 15, 9, 19, 10, 10, 4, 18, 2, 18, 20, 12, 11, 12, 19, 17, 4, 10, 13, 4, 6, 3, 7, 10, 10, 14, 5, 17, 4, 7, 6, 11, 1, 7, 17, 16, 2, 6, 2, 11, 16, 6, 13, 5, 14, 8, 19, 15, 11, 1, 3, 9, 9, 5, 10, 3, 18, 15, 19, 17, 1, 11, 11, 16, 4, 9, 2, 18, 3, 8, 6, 16, 17, 3, 4, 9, 3, 18, 10, 2, 19, 16, 1, 16, 18, 2, 9, 10, 14, 7, 2, 16, 3, 2, 12, 4, 15, 7, 13, 16, 17, 19, 19, 10, 18, 4, 4, 17, 16, 2, 5, 2, 10, 13, 13, 3, 1, 8, 3, 17, 17, 6, 4, 14, 6, 12, 8, 13, 9, 6, 3, 18, 14, 9, 3, 7, 12, 20, 14, 10, 5, 1, 14, 8, 19, 2, 15, 15, 1, 13, 5, 7, 15, 6, 19, 5, 18, 1, 10, 9, 16, 4, 8, 18, 12, 16, 13, 11, 10, 6, 4, 14, 2, 15, 8, 11, 16, 7, 17, 4, 8, 17, 16, 16, 11, 16, 18, 9, 1, 12, 2, 13, 11, 8, 13, 3, 11, 8, 14, 5, 13, 15, 2, 15, 10, 4, 19, 12, 6, 7, 13, 18, 3, 1, 19, 8, 8, 9, 17, 8, 3, 10, 16, 3, 12, 7, 3, 10, 19, 2, 16, 13, 5, 5, 9, 13, 6, 2, 18, 11, 7, 11, 12, 2, 5, 10, 1, 9, 16, 5, 3, 5, 4, 10, 11, 2, 6, 2, 13, 2, 19, 9, 7, 11, 16, 12, 11, 7, 8, 1, 4, 3, 17, 1, 18, 15, 11, 4, 13, 3, 7, 6, 8, 5, 3, 14, 17, 3, 6, 10, 15, 7, 8, 15, 8, 8, 2, 14, 10, 19, 8, 6, 19, 7, 2, 17, 3, 3, 13, 17, 5, 5, 17, 12, 12, 17, 2, 10, 1, 6, 6, 5, 13, 10, 15, 11, 3, 12, 6, 6, 13, 8, 2, 3, 8, 1, 12, 15, 10, 4, 1, 18, 7, 17, 13, 2, 5, 11, 4, 5, 15, 1, 13, 1, 5, 7, 4, 1, 5, 1, 8, 18, 17, 14, 1, 17, 3, 1, 8, 1, 7, 12, 17, 7, 17, 9, 13, 4, 8], "Freq": [0.9929939022907234, 0.9511580761388855, 0.9984548399247676, 0.9849133303262124, 0.9909373033964152, 0.9839290339714354, 0.9955865745733004, 0.9985197818420103, 0.9895351358067163, 0.9917421377302852, 0.9953162073464883, 0.9942569948573856, 0.9803831216259729, 0.9929070436865743, 0.9918918049031731, 0.9907803166255474, 0.9609595748761042, 0.9941826136948115, 0.8963457153432298, 0.9928320889027688, 0.9971010298065197, 0.967000061679208, 0.9756271860494724, 0.976217129809838, 0.995698723784525, 0.9979782020874072, 0.9973168014906537, 0.9730209409182733, 0.9979064483728965, 0.9716612658750785, 0.9676191913660841, 0.993570399166632, 0.9961861891318066, 0.9949890432795108, 0.9881361388077062, 0.996791293443535, 0.9923013741667713, 0.9549120083381425, 0.9931754698104907, 0.9910232765944572, 0.9633530178894869, 0.9910495836736419, 0.9894872072736025, 0.9625581962515126, 0.9967097082753608, 0.9879674998122623, 0.9771590010288206, 0.9959330109271594, 0.9005109882498058, 0.972047796373315, 0.9725321728616594, 0.9928064524614465, 0.9394158145029329, 0.9883362055672613, 0.9384612363979069, 0.9618413736112784, 0.9856890992216395, 0.9793853278574934, 0.9918443838994262, 0.9866525511021382, 0.9960646019155934, 0.9966689242782402, 0.9746688321511491, 0.9927241011498705, 0.9964426415333314, 0.9948704760697792, 0.9961355072245871, 0.9967280498805466, 0.9899722075101691, 0.9836952786585409, 0.9847000916402756, 0.9944870211652727, 0.9848449513715021, 0.991564214105536, 0.9970790571024993, 0.9911664945805073, 0.9480721398478931, 0.9872264012534369, 0.9739104379663206, 0.9935320030725588, 0.9929633118251358, 0.9961781755259059, 0.9701042928375953, 0.9908188628733736, 0.9807634370883245, 0.998340409483076, 0.974896514021412, 0.9621257451305387, 0.907317210025269, 0.9949226530002674, 0.9775597898303413, 0.9815729666949187, 0.9922373034368118, 0.9921569710432034, 0.9973760352079837, 0.9944849036991409, 0.9846242703489485, 0.9897955014257659, 0.9261648416263747, 0.9808790073765656, 0.9937532879192172, 0.9758753821190983, 0.9887036253425503, 0.9771736102769956, 0.9883509360164451, 0.979555960811158, 0.9971505889840968, 0.9812264260043425, 0.9864874235400303, 0.9861452041997858, 0.957593876219136, 0.8618610092775789, 0.8383643602251962, 0.9958568226702809, 0.9801842429143043, 0.9770828905539789, 0.9500211788172644, 0.9865237276227441, 0.9984669258701938, 0.9617406423060908, 0.9776304112556109, 0.9727715042410705, 0.9921562556405122, 0.9982913835148398, 0.9965499426953155, 0.9822318554047266, 0.9964177671613377, 0.9859818396095519, 0.9892500527299417, 0.9969238444662827, 0.9335000271729339, 0.8493261861353979, 0.985879255309389, 0.960297076977971, 0.9911896975680216, 0.9928943868922626, 0.9414697819804008, 0.9581867488923586, 0.9987369399049113, 0.9879769053784169, 0.9839116559805364, 0.9924305066603369, 0.9963990376314996, 0.9976161616097086, 0.9980770139035691, 0.9822312982645817, 0.9703300728607002, 0.9988160365912482, 0.989583996947659, 0.9398758013730484, 0.9660550506609202, 0.993988752288261, 0.9752555720823486, 0.984870232223569, 0.9915838929357514, 0.9931632421157893, 0.952321686851976, 0.9949933909154793, 0.9926414479065498, 0.9953889152664924, 0.976548957647313, 0.9947802833063225, 0.9945743222406298, 0.9956043669134527, 0.9861405422955148, 0.995637388133289, 0.9825914235912858, 0.9991451958513942, 0.9936969921136382, 0.9990676953807713, 0.9768484354273135, 0.9923776540205969, 0.9969638165856146, 0.992756667470258, 0.9543367369762507, 0.9943017227138006, 0.9829362773558326, 0.9921933884778824, 0.9936085034989649, 0.9417734072353164, 0.9953624686841437, 0.9705376559533316, 0.9814656984823106, 0.9404400854172194, 0.9853948369262866, 0.9930637455325497, 0.9879402447564581, 0.9882141555510863, 0.973654186061843, 0.8849957635107701, 0.9862597938789993, 0.9750539045841717, 0.9972729533908282, 0.9975193826629606, 0.9931919957871969, 0.9817915367899503, 0.9796822203476941, 0.953535317361033, 0.9965355792166286, 0.9885309232693879, 0.8787187580009798, 0.9911008335057166, 0.9905682054544501, 0.9875227952099864, 0.9959266937539095, 0.9921139387272766, 0.9689875159622742, 0.9977861951740249, 0.98900508583693, 0.9961309895044883, 0.9677826413605521, 0.9961608722738425, 0.9948897328052891, 0.9972779631650502, 0.9281102951445455, 0.9944813738907304, 0.9959933322874955, 0.9722397154092172, 0.94906782436958, 0.9852642405769823, 0.976804799254226, 0.9963574941589105, 0.9836425807032954, 0.9983030078903303, 0.9941805828473637, 0.9953826873902002, 0.9617536029286698, 0.9960925796790718, 0.9806964766684463, 0.9983457969350839, 0.9923102183539084, 0.9969610306516047, 0.9990205889366525, 0.9746469765693445, 0.9905142073320287, 0.9740623316141087, 0.9307681762840843, 0.9805659655660086, 0.9930583618225187, 0.993330790709717, 0.9899013666991654, 0.9937523672239862, 0.9615375816148641, 0.9893377382412618, 0.979257660114891, 0.988403240772668, 0.9948103892927015, 0.9800050066600406, 0.9867804806858625, 0.9958798381429855, 0.9961270022231453, 0.9968054916724443, 0.9915486593383482, 0.9917077937275001, 0.9850516575584394, 0.9600915704186703, 0.9751273345439639, 0.9928812530001162, 0.993053007162862, 0.933780563335129, 0.9950034090076777, 0.9947214606720958, 0.9906555610661053, 0.9980883787525616, 0.997431659105945, 0.9951793834015845, 0.9986215480752313, 0.9967280090213089, 0.996983811301592, 0.9966162566658147, 0.9943178172439301, 0.9881530159688335, 0.9944883874755857, 0.34655887831835397, 0.9986160018273981, 0.9965328790747084, 0.9932226199318321, 0.9953992697210979, 0.9884342238924428, 0.9866140371687097, 0.9846431932475801, 0.9971189607515969, 0.9978789167577478, 0.9884950748232004, 0.9854693568314183, 0.9685042455444787, 0.9884697796673043, 0.9754052894548154, 0.9975965792722503, 0.9879544980512934, 0.8538512198745143, 0.9850217223514631, 0.9906320496679408, 0.9878307402770348, 0.9859733231694291, 0.9975617343734381, 0.9978529558825531, 0.8415602342054578, 0.9970279395261505, 0.9915550171778461, 0.9683465312154755, 0.9850475701737404, 0.9820422152592482, 0.9961826409442318, 0.9982688039409536, 0.9798522749355385, 0.997586068990263, 0.9793923215677445, 0.9893380077941071, 0.9993593799522915, 0.996583514514942, 0.993620124488635, 0.9655404536722915, 0.999357553649749, 0.993915986252496, 0.9860393774619329, 0.9858293597007861, 0.9868564977205706, 0.9878693402383295, 0.9976967686312431, 0.9886306533761007, 0.9973202444812377, 0.9930703120533748, 0.9954702483285338, 0.9978188239726372, 0.9668526703797319, 0.9967987212916971, 0.9746000338849754, 0.9427547047109113, 0.9911839635168553, 0.9974585313091333, 0.9974469861887095, 0.9985264567604797, 0.990880782302003, 0.9867035765911629, 0.9970647920580634, 0.9792295406768187, 0.994914039305182, 0.9932148553904161, 0.9943872976767681, 0.9491902371436882, 0.9939967635925295, 0.9970017327958967, 0.8993066617518473, 0.9980839023037288, 0.994804297159553, 0.9916576737097865, 0.9903924957315424, 0.9974298866267577, 0.998329015558002, 0.9992123035173982, 0.9947124469102034, 0.9990483763437678, 0.9978615105249525, 0.995067428051845, 0.992422201925491, 0.9904223815522093, 0.994729335944588, 0.9956183276381365, 0.9901636497540791, 0.9699313730740587, 0.9986725126583879, 0.9945386445994139, 0.8844408184316752, 0.9823705140331694, 0.9976104204672641, 0.978481624105638, 0.9898318036965299, 0.970335107988506, 0.9870593453231691, 0.9886596425869519, 0.9821204825601413, 0.9966294535401653, 0.9014801427493587, 0.9905208223846059, 0.9898680585699092, 0.9749736836054512, 0.9978916586858416, 0.9743853694150079, 0.9263217308426699, 0.9974427619164336, 0.9696094911542611, 0.9956406535556305, 0.9975093858957657, 0.9867908534666667, 0.8884906795593172, 0.994590879605818, 0.9978076998945876, 0.9971543445516979, 0.9972892756164454, 0.9756224889630312, 0.9740614798408171, 0.9949585440469074, 0.9668320915815634, 0.9934100353646509, 0.9942699602573767, 0.9977757225330671, 0.9640852581647416, 0.9739587537705617, 0.9863709045581741, 0.9724422376349248, 0.37575878532926205, 0.8996302820189152, 0.9929594222858334, 0.9984604472331553, 0.9944551345372603, 0.9739390980822477, 0.9691604975993424, 0.9963839109032854, 0.9828556994651059, 0.9923718486826008, 0.4312950407871629, 0.9862292949763395, 0.9854673568621569, 0.9939447211877306, 0.9939233507071152, 0.9606104859697259, 0.9986306898873366, 0.9980825683398161, 0.9898823526891277, 0.9924045316328908, 0.9953463016065498, 0.9922301431355258, 0.9954492498819146, 0.9776396932686241, 0.9944850188341036, 0.9961226078609609, 0.9979106936763608, 0.992956688526541, 0.9852819933836997, 0.9930236191395841, 0.9905076940217651, 0.9882713371851025, 0.9981168017459292, 0.9956102688009059, 0.9772988727210538, 0.9879739459084905, 0.9813694294724502, 0.990059409334408, 0.9963335203635095, 0.9701338702375923, 0.9868077417055178, 0.9831874236855007, 0.9859479154375014, 0.9888157250386317, 0.9881614297467808, 0.9916590205539653, 0.9941777396235022, 0.9955676303379237, 0.9962481593677318, 0.9735212567363878, 0.996791115583576, 0.987807054748527, 0.9957089579491426, 0.9784528357332177, 0.9996053279633043, 0.9931876343131649, 0.9838831517872522, 0.993933025281116, 0.9967548328789253, 0.9834907086977904, 0.9991292506371694, 0.9975983068923803, 0.9842417033576449, 0.9885864613502086, 0.9947348307841721, 0.9845541062580225, 0.9585713165770441, 0.9947492129013515, 0.9967548122331203, 0.9988188644252922, 0.9912278097934344, 0.993826765902486, 0.9973479900647816, 0.9637041037032752, 0.9844932061204101, 0.9987171707988627, 0.9734640245695271, 0.939246802416975, 0.9930960974337026, 0.9927694118824871, 0.9949369594436471, 0.9869691437701177, 0.9946622472194429, 0.9962615785860037, 0.9924607403217116, 0.9883551092013778, 0.9255119692695776, 0.9986180456051177, 0.9913066348020458, 0.9996162710490945, 0.992890267738105, 0.9875721460235936, 0.9949146334466451, 0.9982181558340463, 0.9961397846826734, 0.9837606317275445, 0.9931990318468962, 0.9299371083449657, 0.9830485885993351, 0.9934418444574175, 0.9969622255933771, 0.9961127186166557, 0.9783196872415706, 0.9484095058853709, 0.9799235921104044, 0.9791942757641736, 0.9912537969280258, 0.9723372854479383, 0.9784332071687315, 0.9623041271934148, 0.9881671521641892, 0.9991111086614515, 0.9875603932430059, 0.9942505597233556, 0.9813257082692143], "Term": ["able", "accommodation", "accord", "accordance", "achieve", "act", "action", "activity", "add", "address", "adjust", "advice", "advise", "aid", "aim", "air", "already", "also", "always", "amount", "analyse", "analysis_algorithm_code_testing", "anatomy", "anticipate", "application", "apply", "appropriate", "aquaculture", "area", "arrange", "artist", "artistic", "as_well", "aspect", "assemble", "assess", "assessment", "assign", "assist", "attach", "attend", "available", "avoid", "back", "base", "basic", "basis", "behaviour", "belt", "block", "blood", "body", "bond", "box", "bridge", "broken", "brush", "build", "call", "cargo", "carry", "case", "cast", "cause", "change", "characteristic", "check", "chemical", "child", "choose", "circumstance", "clean", "cleaning", "clear", "client", "clinical", "closely", "code", "collaborate", "collect", "community", "company", "compile_programming_paradigm", "compliance", "comply", "component", "compose", "compound", "compromise", "computer", "computer_program", "concentration", "concept", "concern", "condition", "conduct", "connect", "consider", "consideration", "consist", "construction", "consumption", "contact", "container", "context", "continuous", "control", "convert", "cool", "coordinate", "copper", "copy", "cosmetic", "cost", "counselling", "country", "covering", "crane", "create", "creation", "credit", "culture", "current", "customer", "cut", "daily", "damage", "data", "database", "datum", "daytoday", "dead", "debris", "defective", "deliver", "demand", "demonstrate", "description", "design", "desire", "detail", "detect", "determine", "develop", "development", "diagnosis", "diet", "different", "direct", "discover", "discuss", "document", "door", "drilling", "drive", "dry", "dynamic", "efficiency", "electric", "electrical", "electricity", "electronic", "element", "emergency", "enable", "energy", "engine", "ensure", "environment", "equipment", "especially", "establish", "evaluate", "examination", "exchange", "exist", "exposure", "facilitate", "facility", "far", "field", "file", "fill", "final", "find", "finish", "fish", "fit", "fix", "flexible", "flow", "fluid", "follow", "food", "form", "format", "frame", "front", "function", "further", "garment", "gather", "general", "get", "give", "glass", "glue", "good", "government", "group", "guest", "hand", "handle", "health", "healthy", "heat", "help", "high_level", "hoist", "hold", "home", "human", "hygiene", "identify", "impact", "implement", "implication", "improve", "incident", "include", "increase", "individual", "information", "infrastructure", "ingredient", "injury", "insect", "insert", "inspect", "inspection", "install", "instruction", "insulation", "interaction", "interest", "international", "interpret", "inventory", "investigation", "involve", "issue", "keep", "kind", "knowledge", "label", "landscape", "lay", "lead", "legal", "less", "level", "life", "live", "machine", "maintain", "maintenance", "make", "make_sure", "manage", "management", "manner", "manual", "manufacture", "master", "material", "measure", "mechanical", "medical", "medicine", "medium", "member", "metal", "method", "methodology", "mining", "minor", "mix", "moisture", "monitor", "most", "motion_picture", "mould", "movement", "music", "natural", "necessary", "need", "negotiation", "new", "number", "nutritional", "observation", "occur", "oil", "operate", "operating", "operation", "optimise", "option", "order", "organisation", "organise", "origin", "other", "oversee", "own", "package", "packaging", "paper", "part", "participate", "patient", "pattern", "people", "perform", "perform_routine_maintenance", "performance", "performer", "period", "person", "physical", "place", "plan", "plant", "plastic", "policy", "port", "position", "possible", "potential", "potentially_unauthorised_access", "power", "practice", "practise", "prepare", "present", "pressure", "prevention", "principle", "procedure", "process", "processing", "product", "production", "professional", "program", "programme", "project", "property", "protect", "protective", "provide", "public", "pulp", "pump", "quality", "railway", "range", "ratio", "raw", "receive", "recommend", "record", "recruit", "reduce", "regard", "regulate", "regulation", "rehabilitation", "rehearsal", "relate", "release", "remove", "repair", "replace", "representative", "request", "require", "requirement", "research", "respect", "restore", "result", "return", "review", "right", "risk", "roll", "roof", "room", "rope", "rust", "safe_working", "safely", "safety", "sale", "satisfaction", "saw", "schedule", "school", "science", "score", "search", "secure", "security", "select", "series", "service", "set", "sheet", "ship", "show", "site", "size", "sketch", "skill", "social", "software", "solution", "sort", "source", "space", "special", "specific", "specification", "speed", "sport", "stability", "staff", "standard", "start", "state", "steam", "steel", "step", "stock", "stone", "storage", "store", "strategy", "street", "structure", "student", "study", "substance", "such", "supervise", "supplier", "supply", "support", "switch", "system", "take", "tank", "target", "task", "teach", "teacher", "team", "technical", "technique", "tend", "term", "test", "test_security_weakness", "therapy", "tool", "top", "topic", "train", "training", "transfer", "transmit", "transport", "treatment", "tree", "truck", "try", "type", "usage", "use", "user", "usually", "variety", "various", "vehicle", "verify", "vessel", "vocal", "wall", "waste", "water", "way", "wear", "wear_apparel", "weight", "welding", "well", "wide_range", "window", "wiring", "wood", "work", "worker", "write", "write_text"]}, "R": 30, "lambda.step": 0.01, "plot.opts": {"xlab": "PC1", "ylab": "PC2"}, "topic.order": [16, 3, 20, 9, 4, 8, 6, 14, 19, 5, 15, 1, 13, 7, 12, 2, 11, 17, 10, 18]};

function LDAvis_load_lib(url, callback){
  var s = document.createElement('script');
  s.src = url;
  s.async = true;
  s.onreadystatechange = s.onload = callback;
  s.onerror = function(){console.warn("failed to load library " + url);};
  document.getElementsByTagName("head")[0].appendChild(s);
}

if(typeof(LDAvis) !== "undefined"){
   // already loaded: just create the visualization
   !function(LDAvis){
       new LDAvis("#" + "ldavis_el606025368979881925708230102", ldavis_el606025368979881925708230102_data);
   }(LDAvis);
}else if(typeof define === "function" && define.amd){
   // require.js is available: use it to load d3/LDAvis
   require.config({paths: {d3: "https://d3js.org/d3.v5"}});
   require(["d3"], function(d3){
      window.d3 = d3;
      LDAvis_load_lib("https://cdn.jsdelivr.net/gh/bmabey/pyLDAvis@3.3.1/pyLDAvis/js/ldavis.v3.0.0.js", function(){
        new LDAvis("#" + "ldavis_el606025368979881925708230102", ldavis_el606025368979881925708230102_data);
      });
    });
}else{
    // require.js not available: dynamically load d3 & LDAvis
    LDAvis_load_lib("https://d3js.org/d3.v5.js", function(){
         LDAvis_load_lib("https://cdn.jsdelivr.net/gh/bmabey/pyLDAvis@3.3.1/pyLDAvis/js/ldavis.v3.0.0.js", function(){
                 new LDAvis("#" + "ldavis_el606025368979881925708230102", ldavis_el606025368979881925708230102_data);
            })
         });
}
</script>



## Save

Let's save our final dataset containing the topic. 


```python
top_words_per_topic = []
for t in range(lda_model.num_topics):
    top_words_per_topic.extend([(t, ) + x for x in lda_model.show_topic(t, topn = 15)])

df = pd.DataFrame(top_words_per_topic, columns=['Topic', 'Word', 'P'])
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
      <th>Topic</th>
      <th>Word</th>
      <th>P</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>provide</td>
      <td>0.151524</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0</td>
      <td>create</td>
      <td>0.092743</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0</td>
      <td>test</td>
      <td>0.077340</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0</td>
      <td>prepare</td>
      <td>0.075709</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0</td>
      <td>analyse</td>
      <td>0.068877</td>
    </tr>
  </tbody>
</table>
</div>




```python
df.loc[df.Word.str.contains('data', case=False)]
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
      <th>Topic</th>
      <th>Word</th>
      <th>P</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>104</th>
      <td>6</td>
      <td>database</td>
      <td>0.017024</td>
    </tr>
    <tr>
      <th>179</th>
      <td>11</td>
      <td>data</td>
      <td>0.014281</td>
    </tr>
  </tbody>
</table>
</div>




```python
df.query('Topic==6')
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
      <th>Topic</th>
      <th>Word</th>
      <th>P</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>90</th>
      <td>6</td>
      <td>accord</td>
      <td>0.155170</td>
    </tr>
    <tr>
      <th>91</th>
      <td>6</td>
      <td>datum</td>
      <td>0.087237</td>
    </tr>
    <tr>
      <th>92</th>
      <td>6</td>
      <td>measure</td>
      <td>0.081973</td>
    </tr>
    <tr>
      <th>93</th>
      <td>6</td>
      <td>company</td>
      <td>0.068412</td>
    </tr>
    <tr>
      <th>94</th>
      <td>6</td>
      <td>management</td>
      <td>0.061046</td>
    </tr>
    <tr>
      <th>95</th>
      <td>6</td>
      <td>specification</td>
      <td>0.057844</td>
    </tr>
    <tr>
      <th>96</th>
      <td>6</td>
      <td>policy</td>
      <td>0.053550</td>
    </tr>
    <tr>
      <th>97</th>
      <td>6</td>
      <td>level</td>
      <td>0.053254</td>
    </tr>
    <tr>
      <th>98</th>
      <td>6</td>
      <td>implement</td>
      <td>0.049561</td>
    </tr>
    <tr>
      <th>99</th>
      <td>6</td>
      <td>element</td>
      <td>0.035853</td>
    </tr>
    <tr>
      <th>100</th>
      <td>6</td>
      <td>waste</td>
      <td>0.033441</td>
    </tr>
    <tr>
      <th>101</th>
      <td>6</td>
      <td>site</td>
      <td>0.031845</td>
    </tr>
    <tr>
      <th>102</th>
      <td>6</td>
      <td>medium</td>
      <td>0.020775</td>
    </tr>
    <tr>
      <th>103</th>
      <td>6</td>
      <td>avoid</td>
      <td>0.020167</td>
    </tr>
    <tr>
      <th>104</th>
      <td>6</td>
      <td>database</td>
      <td>0.017024</td>
    </tr>
  </tbody>
</table>
</div>




```python

```
