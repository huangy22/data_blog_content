title: A news recommendation engine driven by collaborative reader behavior
tags: 
  - natural language processing
  - recommendation system
  - network analysis 
categories: data science
date: 2017-10-20 13:17:09
---

Reading the news online has exploded as the web provides access to millions of news sources from around the world. The sheer volume of articles can be overwhelming to readers. Therefore, building a news recommendation system to help users find articles that are most interesting to them is a crucial task for every online news service.

In this article, I will describe how to combine collaborative filtering with content-based filtering to develop a recommendation engine for news articles based on user behavior.

<!-- more -->

## The task: building a news recommendation engine

News recommendation systems must be able to handle the challenge of fresh content: breaking news that hasn’t yet been viewed by many readers. Thus we need to leverage the data associated with the article content available at time of publishing *such as topics, categories, and tags* to build a content-based model, and match it to readers’ interests learned from their reading histories. However, one drawback of [content-based recommendations](https://en.wikipedia.org/wiki/Recommender_system#Content-based_filtering) is that when there is insufficient user history, the coverage of the recommendations is very limited. This is referred to as the [cold-start](https://en.wikipedia.org/wiki/Cold_start) problem, common in recommender systems.

This blog post introduces a news recommendation engine which combines [collaborative-filtering](https://en.wikipedia.org/wiki/Recommender_system#Collaborative_filtering) with [content-based filtering](https://en.wikipedia.org/wiki/Recommender_system#Content-based_filtering) to diversify news recommendations. This so-called [hybrid-filtering](https://en.wikipedia.org/wiki/Recommender_system#Hybrid_recommender_systems) recommendation system takes into account not only the content of the articles and the user’s reading history, but also the reading history of people who share similar interests. By learning from the history of people with similar interests, this engine will recommend news with a broader coverage of topics, even when the historical information about a particular user is limited. [Give it a try!](http://www.yuanhuang.club/)

![](/images/2017-10-20-news/demo.gif)

In this post, I’ll explain how I built the recommendation engine from the ground up. The discussions will include:

  - Step 1: Finding readers with similar interests
  - Step 2: Topic modeling
  - Step 3: Making recommendations
  - Step 4: Evaluation of the recommender

### Step 1: Finding readers with similar interests

As a first step, the engine identifies readers with similar news interests based on their behavior of retweeting articles posted on Twitter. The data was collected from articles posted on Twitter by three different publishers (New York Times, Washington Post, and Bloomberg) between May 23rd and June 2nd, 2017. I also collected information from the Twitter profiles of the users retweeting the articles. By looking at how many news posts two users share in common, I can define a [cosine similarity](https://en.wikipedia.org/wiki/Cosine_similarity) score for the users. This similarity score enables the construction of a network by weighting the links between users.

By applying a [hierarchical clustering algorithm](https://en.wikipedia.org/wiki/Hierarchical_clustering_of_networks) to the user network, I can detect the community structures among the readers. The hierarchical clustering algorithm uses a [greedy method](https://en.wikipedia.org/wiki/Greedy_algorithm) to try and optimize the [modularity](https://en.wikipedia.org/wiki/Modularity_(networks)) of clusters (defined below). The modularity is an important metric for network clustering, which indicates how dense the connections within clusters are compared to the connections between different clusters.

![Modularity score of a clustering in networks. In the equation, $m$ is the total number of links in a network, $v$ and $w$ are indices of the nodes in the network (users), $A$ is the link matrix between each pair of nodes (defined as the similarity score), $k_v$ is the total link weight that is connected to a specific node $v$, and $c_v$ is the group label of node $v$.](https://cdn-images-1.medium.com/max/800/1*YZ1p3NgerqqXRqUFiyV4KQ.png)

Any real-world network will have a modularity value between 0 and 1. In my user network, the modularity score of the hierarchical clustering algorithm peaks at 6 clusters with value 0.151.


![Community structure in the user network detected from the clustering algorithm. Each point represents a user, with colors representing their group label. Due to visualization limitation, in the graph I only show the users with more than 30 retweets. The length of links between users are inversely related to the similarity between them. More similar users will be pulled closely together while dissimilar users will be pushed further apart.](/images/2017-10-20-news/clusters.png)

With the help of the [D3](https://d3js.org/) visualization package, I plotted the community structure in the subset of readers with highest number of retweets. In the graph, users with high similarities are pulled closer to each other and less similar users will be pushed further apart. Some groups (for    example the red, purple, green and light blue groups) show a very tight structure with extremely high similarities within the group, while the other groups are in a more diffuse shape, indicating more diverse interests among the group members.

Now that I’ve identified several user groups with high similarities, but how do I know what kind of news they like to read?

### Step 2: Topic modeling

In order to understand the content that users like to read, I used a natural language processing technique called [Latent Dirichlet Allocation](https://en.wikipedia.org/wiki/Latent_Dirichlet_allocation) (LDA) that allows computers to identify hidden topics of documents based on the co-occurrence of words collected from those documents. LDA can also be used to understand how much of an article is devoted to a particular topic, which allows the system to categorize an article, for instance, as 50% environment and 40% politics.

I trained the LDA model on more than 8,000 articles collected using a Python package called Newspaper. The number of topics was chosen by trying to achieve a diverse topic coverage without having too many topics. The diversity of topics can be evaluated by the average [Jaccard similarity](https://en.wikipedia.org/wiki/Jaccard_index) between topics; this index measures similarity between two finite sample sets, and is defined as the size of the
intersection divided by the size of the union of the sample sets. High Jaccard similarity indicates strong overlap and less diversity between topics, while low similarity means the topics are more diverse and have a better coverage among all the aspects in the articles.

![The average Jaccard similarity between each pairs of topics. The overlap between topics drops rapidly when increasing the number of topics from 5 to 20, and starts to saturate after 20 topics.](/images/2017-10-20-news/Jaccard.png)

The following figure shows the probability distributions of all the articles among the 20 topics learned from LDA model. There are several hot topics with high probability among all the articles we collected, which include politics, finance, technology, environment, etc.

![Topic distribution among 20 topics learned from LDA in all the articles. Topics with high probability are about politics(1), finance(2), technology(3), and environment(4).](/images/2017-10-20-news/topic_distribution.png)

With the topic information of each article, I can learn the topic interests of each user group by summarizing the topics of the popular articles in each reader group. By aggregating the topics of each article weighted by the number of retweets, I obtained the topic probability distribution for all of the six user groups. The following figure shows two examples, the blue and green groups, which show completely different interest in different topics.

![Topic distribution among 20 topics learned from LDA for user group 0 and group 3. Group 0 (blue) shows more interests in finance and technology topics, while group 3 (green) has more interests in politics.](/images/2017-10-20-news/group_0_3.png)

Another way to visualize the different topic distributions is by looking at the keywords in each group. Here I show two examples of user group 0 (blue in the network) and user group 3 (green).

![Word clouds generated from the topic distributions of different user groups. Here I show two examples, group 0 (left) which has more interests on finance, and group 3 (right) with more focused interest on politics.](/images/2017-10-20-news/wordcloud.png)

### Step 3: Making recommendations

Now that I have divided the users into different groups based on their similarity and identified their interests among different topics, the next step is to recommend fresh news by matching the topics of the article with the topic profile of each user group. In other words, my recommendation engine doesn’t provide personalized recommendations solely based on a particular user’s interest, but instead gives group-based recommendations in order to obtain a more diverse result.

When recommending new articles to a user group, I want to find articles that have the most similar topics with the group’s interests. The similarity between each new article and the group is calculated as the [cosine similarity](https://en.wikipedia.org/wiki/Cosine_similarity) of their topic distributions.

![Demonstration of the cosine similarity between each article and the user groups.](/images/2017-10-20-news/match.png)

By ranking the articles by similarity score, the best-matching articles will be recommended to all of the readers within the reader group. [Take a look at the news recommendations for you!](http://www.yuanhuang.club/news)

### Step 4: Evaluation of the recommender

Great! The recommender seems to be working, but how do I know whether it is working well? How much will the readers like the recommendations that they get? The evaluation of a recommending system can be quite tricky. The golden metric for a recommending system is how much the system will add value to the user and business. Ultimately, you want to perform A/B testing to see whether recommendations will increase usage, subscriptions, clicks, etc.

However, in practice there are other common metrics to evaluate a recommender, which can still help me gain some insights on the performance before actually putting the system into use. Most of these offline methods require hold-out subset of the training data (in this case, holding out a subset of previously retweeted news posts), pretending the users haven’t seen these items and trying to recommend them back to the users. Since the reader groups’ history about this test set already exists, we can leverage this information to validate the performance of recommender.

One natural goal of recommender systems is to distinguish good recommendations from bad ones. In the binary case, this is very natural — a “1” is a good recommendation, while “0” means a bad recommendation. However, since the data that we have (number of retweets of an article by users from a user group) is non-binary, a threshold must be chosen such that all ratings above the threshold are good and called “1”, while the rest are bad with label “0”. A natural way to set the threshold is to choose the median value of the number of retweets in a user group, labeling all of the articles above the median as “good” recommendations and all the rest as “bad”. The predicted score from the recommending system is the cosine similarity between the topics of an article and the topics in a user group, which ranges from 0 to 1.

This good/bad, positive/negative framework is the same as binary classification in other machine learning settings. Therefore, standard classification metrics could be useful here: [precision and recall](https://en.wikipedia.org/wiki/Precision_and_recall). In this project, the precision score is the fraction of actually “good” recommendations out of all the recommendations the system made, while recall is the fraction of good recommendations out of all the “good” articles in the data set.

All of these metrics are clear once we have defined the threshold of good/bad in the predictions. For instance, in a binary situation the labels are 0, 1 while the predictions are continuous from 0–1. To compare the predictions, I select a threshold (here I choose the median value of all the predictions), above which we call predictions 1 and below 0. From 10 rounds of validations with 1000 hold-out articles, the average precision score of our recommending system is 65.9%, while the average recall is 66.5%.

The choice of the threshold can be varied depending on desired tradeoffs (for a more in-depth discussion, see [this blog](http://blog.insightdatalabs.com/visualizing-classifier-thresholds/)). Therefore to summarize classification performance generally, we need metrics that can provide summaries over this threshold. One tool for generating such a metric is the [Receiver Operator Characteristic (ROC)](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) curve, which plots the [True Positive Rate (TPR)](https://en.wikipedia.org/wiki/Sensitivity_and_specificity) versus the [False Positive Rate (FPR)](https://en.wikipedia.org/wiki/Sensitivity_and_specificity) at different threshold levels. The area under the curve (often referred to as the AUC) indicates the probability that a classifier will rank a randomly chosen positive instance higher than a randomly chosen negative one, which can provide some insight on the predictive power of our model.

![The receiver operating characteristic curve for different user groups calculated from 10 rounds of validations on 1000 hold-out articles. The average area under curve among all the user groups is 0.77.](/images/2017-10-20-news/roc.png)

## Summary

The framework of this recommendation engine is quite versatile: it can be adapted to combine data from different platforms (Twitter, Facebook, news     websites) to construct the user network; if combined with more personal information from the users, the system can also be developed to analyze user behavior and improve marketing and advertising decisions.
