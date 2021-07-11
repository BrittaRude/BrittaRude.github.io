---
layout: post
title: "Publish a jupyter notebook as a blogpost on your github pages"
date: 2021-07-11
myexcerpt: We more and more work in Jupyter Notebook. How can we show our codebooks as a blogpost on github pages? 
image: /images/github.jpg
---

## Converting a Jupyter Notebook to a blogpost on Github pages

We more and more work in Jupyter Notebook. How can we show our codebooks as a blogpost on github pages? There is an easy command to convert our .ipynb files to .md files, namely nbconvert. We can do this easily in our Anaconda Prompt. But first, we have to change the directory we are working in to the one of .ipynb file. 

To do this, open Anaconda Prompt and type: 

```
cd D:\Newfolder
```

Then we can transform our jupyter notebook through a simple command in the Anaconda Prompt: 

```
jupyter nbconvert --to markdown  notebook.ipynb 
````

This then creates a *notebook.md* file with the markdown text of our blog post. Additionally, it creates a folder called *notebook_folder* with all images of the blogpost. 

Lastly, upload the *notebook.md* file to the *_posts* folder of your github page. You will also need to adjust the source code for your images. Therefore, upload the *notebook_folder* to the *images* folder of your github pages repository.  
