---
layout: post
title:  "Hierarchical Clustering"
date:   2023-01-27 12:26:00 +0530
categories: hierarchical clustering
---

In the previous [article](https://subclassy.github.io/kmeans-clustering/), I
talked about the Wholesale Dataset from the UCI Machine Learning Repository. And
then I implemented K-Means on the dataset in R. In this article, I will again 
use the same dataset including all the same pre-processing and then apply 
Hierarchical Clustering in R (hence, I will recommend you to read that 
[article](https://subclassy.github.io/kmeans-clustering/) first if you haven't 
already).

The main idea behind Hierarchical Clustering is that it creates a sequence of 
nested clusters based on the properties of the individual observations. There 
are two lines of thinking behind such algorithms -
1. Agglomerative Clustering: This is a bottom-up approach to clustering, where 
each individual observation is a unique cluster. The algorithm then iteratively 
merges the most similar clusters into a new cluster. This is done until there 
remains just one cluster.
2. Divisive Clustering: This is top-down clustering approach. In this, all of 
the observations are clustered into a single cluster. Then, each cluster is 
divided into two sub-clusters based on some form of similarity. This is done 
until each sub-cluster has just a single observation.

For this discussion, we will primarily focus on the first type - Agglomerative 
clustering.

#### Dendogram
The analysis in hierarchical clustering is often done with the help of a Dendogram. As shown in
Figure A.3.1 (from ISLR Chapter 10), a dendogram is a graphical representation of the hierarchy
of clusters. Each node in the dendogram is a cluster and the lead nodes represent the individual
observations or cluster with single observation. In agglomerative clustering, the lead nodes begin to
merge into clusters or nodes, representing similar observations. As we move further up, all of the
observations get clustered into a single node or the root of the tree.

Dendograms also provide a very simple way to determine the number of clusters in the data. In order
to determine the number of clusters all you have to do is to draw a horizontal line across the branches
of the dendogram as shown in Figure A.3.2. Then, the number of clusters is then determined by the
number of vertical lines on the dendogram below the drawn horizontal line - in this case, we get two
clusters. Herein lies the biggest advantage of using dendograms is that the user does not need to have
a superior knowledge about the number of clusters in the data. All the user has to do is to look at the
dendogram and identify the level of granularity desired for the application and then decide the cut.