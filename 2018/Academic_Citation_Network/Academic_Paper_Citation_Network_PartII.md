title: Academic Paper Citation Network (Part 2 - Interactive Visualization)
tags:
	- network analysis
	- interactive visualization
	- javascript
	- D3
categories: data science
date: 2018-04-29
---
As a theoretical physicist by training, whenever I want to enter a new field or start a new project, I would like to do a thorough literature search, but always find myself overwhelmed by the number of papers in the field. In my field of condensed matter physics, everyday there are a few dozens of new preprint papers posted on [arXiv](https://arxiv.org/). 

As I began to grow a strong interest in data science and machine learning since last year, I realize that it would be really helpful to build a data product to help researchers to find relevant information in the ocean of scientific papers using data science and machine learning techniques. This is the reason why in the spring of 2017, I started to work on this project on academic paper citation network analysis. 

<!-- more -->

## Tasks
After discussing this idea with my research colleagues and friends (since they will be the targeted users of my product), I set three main goals for my project: 

1. Understand the landscape of the research field by looking at the citation relationship between research papers, understand how do they divide into different subfields, how subfields are interacting with each other; 
2. For each field of interest, identify the most important papers and authors; 
3. A more advanced functionality would be to recommend important articles or newly published articles in a user’s field according to user’s research history and interest.

I am going to write a series of blog posts talking about the steps I took from start to finish in this project. In this first post, I will be focusing on the first task, and describe how I used hierarchical clustering algorithms to identify the community structure in academic citation networks. In the next post, I will talk about the interactive website I built for users (researchers and anyone who is interested in physics research!) to explore the landscape of physics research and identify the most influential research papers and researchers in the field of their interest. (The third task, to recommend articles for users, is still an ongoing process and hopefully I can turn it into something that can put into production someday!)


## Citation Network
The citations between papers give us a direct way to study how papers are connected with each other, and how knowledge and ideas are inherited and evolving in time. The citation network of papers is a directed graph, with papers as nodes and citation as directed edges that only going backward in time. 

{% asset_img directed_graph.png %}
