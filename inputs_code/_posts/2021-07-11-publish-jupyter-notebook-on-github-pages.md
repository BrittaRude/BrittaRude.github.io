---
layout: post
title: "Publish a jupyter notebook as a blogpost on your github pages"
date: 2021-07-11
myexcerpt: We more and more work in Jupyter Notebook. How can we show our codebooks as a blogpost on github pages? 
image: /images/github.jpg
---

## Converting a Jupyter Notebook to a blogpost on Github pages

We more and more work in Jupyter Notebook. How can we show our codebooks as a blogpost on github pages? There is an easy command to convert our .ipynb files to .md files, namely nbconvert. We can do this easily in our Anaconda Prompt. But first, we have to change the directory we are working in to the one of our notebook.ipynb file. 

<img src="/images/github.jpg" alt="database" style="float:left;margin: 2px 2px 2px 2px;max-width:40%;"/>

To do this, open Anaconda Prompt and type: 

```
cd D:\Newfolder
```

Then we can transform our jupyter notebook through a simple command in the Anaconda Prompt: 

```
jupyter nbconvert --to markdown  notebook.ipynb 
````

This then creates a *notebook.md* file with the markdown text of our blog post. Additionally, it creates a folder called *notebook_folder* with all images of the blogpost. 

Lastly, upload the *notebook.md* file to the *_posts* folder of your github page. Add the date and title of the blogpost to your title. You will also need to adjust the source code for your images. Therefore, upload the *notebook_folder* to the *images* folder of your github pages repository. [*Picture by Roman Synkevych*] 

Sometimes you might encounter the problem that not all of your notebook converts. Then try the following in your conda command prompt: 

```
Jupyter nbconvert --to latex notebook.ipynb
pdflatex notebook.tex
```
