title: Academic Paper Citation Network (Part I, Network Clustering)
tags:
	- network analysis
	- clustering algorithms
categories: data science
date: 2018-03-31
---
As a theoretical physicist by training, whenever I want to enter a new field or start a new project, I would like to do a thorough literature search, but always find myself overwhelmed by the number of papers in the field. In my field of condensed matter physics, everyday there are a few dozens of new preprint papers posted on [arXiv](https://arxiv.org/). 

As I began to grow a strong interest in data science and machine learning since last year, I realize that it would be really helpful to build a data product to help researchers to find relevant information in the ocean of scientific papers using data science and machine learning techniques. This is the reason why in the spring of 2017, I started to work on this project on academic paper citation network analysis. 

<!-- more -->

## Tasks
After discussing this idea with my research colleges and friends (since they will be the targeted users of my product), I set three main goals for my project: 

1. Understand the landscape of the research field by looking at the citation relationship between research papers, understand how do they divide into different subfields, how subfields are interacting with each other; 
2. For each field of interest, identify the most important papers and authors; 
3. A more advanced functionality would be to recommend important articles or newly published articles in a user’s field according to user’s research history and interest.

I am going to write a series of blog posts talking about the steps I took from start to finish in this project. In this first post, I will be focusing on the first task, and describe how I used hierarchical clustering algorithms to identify the community structure in academic citation networks. In the next post, I will talk about the interactive website I built for users (researchers and anyone who is interested in physics research!) to explore the landscape of physics research and identify the most influential reseach papers and researchers in the field of their interest. (The third task, to recommend articles for users, is still an ongoing process and hopefully I can turn it into something that can put into production someday!)

## Data Collection
To gather the data for physics articles and build their citation network, I made a request on the [American Physical Society](https://journals.aps.org/datasets) website who was kind enough to provide me all data about over 450,000 articles from all the APS journals (Phys. Rev. Lett., Phys. Rev. X, Rev. of Mod. Phys., to name a few) dating from 1893 to 2015. 

The dataset includes two parts:

- **Citing Article Pairs**: a json file contains pairs of APS articles that cite each other in format ”10.1103/PhysRevSeriesI.11.215, 10.1103/Phys-RevSeriesI.1.1” (first string is the doi of the citing article, while the second string is the doi of the cited article); 
- **Article Metadata**: the metadata of all APS journal articles including doi, title, journal, date, volume, page, author, affiliations, and so on (each article is stored in individual json files).
In this first part of the project, I mainly used the citation dataset and performed clustering algorithms on the citation network. Due to the limitation of time and computational resources, I restricted the scale of the project on the articles published in three more general purpose journals (Physical Review Letter, Review of Modern Physics, and Physical Review X) in the past ten years, from 2007 (which happend to be the year I entered the field of physics 😉) to 2015. 

To construct the citation network on this subset of data, I need to use the journal and publish time information from the metadata to filter out the citation pairs that not in the three journals or not in the time period of our interest. To do this, I loaded all the article metadata and citation data (all in `json` format) into a [MongoDB](https://www.mongodb.com/) database, and wrote some MongoDB queries to link the citation and metadata collections and performed the filtering on journal and date of the articles. This resulted in a network of 27695 physics articles and 91346 citation links between them.

## Citation Network
The citations between papers give us a direct way to study how papers are connected with each other, and how knowledge and ideas are inherited and evolving in time. The citation network of papers is a directed graph, with papers as nodes and citation as directed edges that only going backward in time. 

{% asset_img directed_graph.png %}

Our main task is to identify the [community structure](https://en.wikipedia.org/wiki/Community_structure) in the citation network. The community in a graph is a group of nodes that are densely connected internally but with sparse connections with nodes from other community. In the following example I am showing here, the graph can be divided into two communities. 
{% asset_img community_structure.png %}

The task of community detection in networks is a very popular and important topic in network analysis. It is very similar to the unsupervised clustering task, with a major difference which is that the data points in a graph only have topological structure but without a well-defined distance metric. In recent years, a growing number of clustering algorithms have been proposed to study the community structure of the scientific networks [^1,^2,^3,^4,^5,^6,^7].

Before talking about specific models to detect the clusters, I want to first discuss how would we evaluate a clustering result in a graph. The metric mostly commonly used is the [modularity](https://en.wikipedia.org/wiki/Modularity_(networks)) score, which represents how strongly connected the nodes within each cluster compared to nodes between different clusters. To give the definition of modularity, I first need to define several notions. I will use $n$ to represent the number of nodes in the graph, $m$ as the number of edges, $c$ as the number of clusters. Then, the definition of modularity is given by the following equation:

{% asset_img modularity.png %}

## Hierarchical Clustering Algorithms for Networks

With the modularity metric at hand, we can think about algorithms to optimize the score. Unfortunately finding a global optimization for modularity is a [NP-hard](https://en.wikipedia.org/wiki/NP-hardness) problem that is impractical to implement. Instead of trying to find a global optimum, people would normally use greedy algorithms to incrementally increase the modularity and achieve a local optimum. One example is the [Louvain algorithm](https://en.wikipedia.org/wiki/Louvain_Modularity)[^2] which is an [agglomerative hierarchical clustering algorithm](https://en.wikipedia.org/wiki/Hierarchical_clustering) that is very efficient and can give high performance compared to other methods in many cases.

The idea of the Louvain algorithm is to assign a unique cluster label to each node as the initial state. Then iterate through each node, calculate the modularity gain of merging the node with any of its neighbor nodes and select the neighbor with highest modularity gain to merge. After one round, clusters will begin to form in the graph. By merging all the nodes in a cluster into a bigger node, adding the weight of edges together between each of these bigger nodes, one can construct a compressed graph with fewer number of "nodes". On this compressed graph, repeat the iteration of merging process again. Repeat the merging and compressing process until all the nodes fall into a gaint cluster and there’s nothing left to merge.

{% asset_img clustering_process.png %}

The hierarchical clustering process builds up a hierarchical tree of cluster structures. One important question to ask is when should we stop? In other words, how do I determine how many clusters I should keep in the graph? For example, in the example I gave above, 2 clusters or 3 clusters both seem to be reasonable choices. But it is better to use some quantitative metric to help with the selection. One approach is to perform the whole merging process in the algorithm till the top of the tree, keep track of modularity score at each step, and then plot modularity score as a function of cluster number. The optimal choice for the number of cluster can be chosen as the peak position in the curve.
{% asset_img tree.png %}

## Results

Before applying the clustering algorithm on the network of more than 20000 articles, I tested the efficiency and performance of the algorithm on a randomly-selected subset of 2000 articles. The simulation with the Louvain algorithm on a network of 2000 articles takes 5.92 seconds. This figure shows the modularity score with the different number of clusters given by the Louvain algorithm. The modularity function peaks at Q = 0.852, when the number of cluster is 200. I also looked at the distribution of cluster sizes with the optimal number of clusters(N = 200). The histogram result is consistent with a power-law distribution which is also found in previous study[^4]. 
{% asset_img result_test.png %}

To have a better sense of the data, I drew the [force-directed graph](https://bl.ocks.org/mbostock/4062045) for the citation network using a Javascript library [D3.js](https://d3js.org/). In this graph, different colors represent the cluster structures in the citation network of our randomly selected 2000 articles.
{% asset_img d3.png %}

Now it's finally the time to apply the Louvain clustering algorithm on the whole dataset including27695 articles and 91346 citation links. The simulation with the Louvain algorithm on a network of 27695 articles and 91346 citation links takes about 1.5 hours. The modularity function peaks at Q = 0.762, when the number of cluster is 500. This value indicates a strong community structure in the network. We can also see a clear power-law distribution of cluster sizes in the histogram of cluster sizes. 
{% asset_img result_wholedataset.png %}

However, the force-directed graph for more than 20,000 nodes is no longer tractable. Instead I plotted the force-directed graph for the network of the clusters (all the nodes in a single cluster get compressed into a gaint node) with the size of the node representing the size of each cluster, while the link width represents number of citations between two clusters. Also note that the links between the cluster nodes are no longer directed since there are citations both ways. 

{% asset_img network_cluster.png %}

## Discussions

From the histogram of cluster sizes and the force-directed graph, we can see that there are a few larger clusters and a lot of smaller clusters. For the purpose of identifying research fields from the clustering results, we will pay more attention to the larger clusters as they clearly represent the main fields in physics such as condensed matter, high energy physics, astrophysics, etc, while the smaller clusters represent the more specific fields that are more isolated from other main fields. However, we still want to understand the subfields in the main fields such as condensed matter physics. In order to further break down the main fields into subfields, we can apply the clustering algorithm again for each of the big clusters we identified. 

In the next blog post, I will talk about how I applied the clustering algorithms on three levels to identify the subfields in physics and turn the clustering results into an interactive website using D3.js! 


## Website and Source Code
[website](http://www-edlab.cs.umass.edu/~yuanh/590v/project/pages/index.html)

[github](https://github.com/huangy22/Academic_Citation_Network)

## References
[^1]: E. A. Leicht, and M. E. J. Newman. Community structure in directed networks, Physical Review Letters, (2008) 100, 118703.[^2]: V. D. Blondel, J. L. Guillaume, R. Lambiotte, and E. Lefebvre, E. (2008). Fast unfolding of communites in large networks, Journal of Statistical Mechanics: Theory and Experiment, P1008, 17425468.[^3]: P. Zhao, and C. Zhang. A new clustering method and its application in social networks, Pattern Recognition Letters 32 (2011) 2109-2118.[^4]: M. E. J. Newman, and M. Girvan. Finding and evaluating community structure in networks, Phys. Rev. E 69, 026113 (2004).[^5]: L. Danon, A. Diaz-Guilera, J. Duch, and A. Arenas. Comparing community structure identification, J. Stat. Mech. (2005) P09008.[^6]: M. E. J. Newman. Fast algorithm for detecting community structure in networks, Phys. Rev. E 69, 066133 (2004).
[^7]: S. Fortunato, D. Hric, Community detection in networks: A user guide, Physics Reports 659, 1-44 (2016).


