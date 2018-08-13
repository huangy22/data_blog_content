title: Intro to Multilevel Models (with a discussion about group-level predictors)
tags:
	- multilevel model
	- Bayesian analysis
categories: probabilistic modeling
date: 2018-08-11
---

For the past a few months, I have been learning and building multilevel models for the purchase data we have at Dia&Co. The deeper I dive into the mixed effect/multilevel/hierarchical modeling field, the more unknowns than knowns I discovered everyday. I feel now it is a good point to start writing down some of my **known knowns** and **known unknowns**, and hopefully by doing this I can identify more **unknown unknowns** and turn them into knowns.

<!-- more -->

In this very first post about multilevel modeling, I will mainly talk about three things:

1. In the first part, I'm going to talk about the motivation for building multilevel models for data with group structures.
2. I will use a simple two-level model to explain how to build such a model in both the Frequentist (mixed effect model using lme4) and Bayesian (bayesian model using PyMC3) way. 
3. Then, I will discuss how to add group-level predictors in multilevel models and how to interpret them properly.
## Why Multilevel models?In our real life data, there are a lot of situations where the data can have some intrinsic group structure in it. Take the e-commerce transaction data as an example. The transaction data can be grouped by the customers who made the purchase, and the customers can be further grouped by their geolocation, occupation, you name it. On the other hand, the transactions can also be grouped by the product that's being purchased, moreover the products can also fall into different categories. In a word, it's very common to see data that has a hierarchical group structure (nested groups, for example data can be grouped by products, then products grouped by categories), or a non-nested group structure (for example the customer groups and product groups are non-nested), or both. 

Suppose we have a dataset with some group structure, and each data point has an input variable and an output observation. In order to understand the relationship between the input feature and the output from the data, the simplest way is to build a regression model between the input and output and totally ignore the group structure in the data. But if we know that the relationship is different for different groups, the result we get from this simple model will not be very satisfying.

Or you may say, we could divide the data into groups and learn a separate model for each group. That may be a great strategy sometimes. But there are cases when we have a large number of groups, and for some of the groups, the number of observations might be too small to build any sensible model. It would be great if we can somehow combine the knowledge about the overall relationship from other groups.

Multilevel model is a generalization of regression models (usually in the family of generalized linear models) that sits in the middle ground between these two extreme approaches and strikes a proper balance between the overall average and group difference. 

## How to build a multilevel model?
I will take an example 

### How does the model look like?

### Frequentist or Bayesian?




## References

[^1]: 
Gelman, A., & Hill, J. (2006). Data Analysis Using Regression and Multilevel/Hierarchical Models (1st ed.). Cambridge University Press.

[^2]: 
https://docs.pymc.io/notebooks/multilevel_modeling.html

[^3]: 
https://web.as.uky.edu/statistics/users/pbreheny/701/S13/notes/3-21.pdf