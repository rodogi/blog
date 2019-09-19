+++
title = "Using pandas for network analysis"
author = ["Rodrigo Dorantes-Gilardi"]
date = 2019-05-15T00:00:00-05:00
draft = false
+++

## Network analysis {#network-analysis}

[Networkx](https://networkx.github.io/) is a well known python module for the analysis of networks. I used it a lot during my PhD
for the creation of protein networks and to compare them. I stopped using it for a while after I moved
to the industry as a data scientist.

My job consisted 90% of the time to process data (pre-process, clean, feature creation, wrangling)
before modeling anything. Pandas is indeed a data-analysis library for python, and a very good one.


## Problem {#problem}

I came back to the academy not so long ago to work on gene regulatory networks and decided to dust
my networkx skills. When doing network analysis, it is often the goal to deal with the structure
(or topology) of the network. For this, `networkx` is great.

However, I need to deal also with the attributes of nodes and edges, with make the task more
complex.

For instance, a simple task like asking `networkx` what is the node with the maximum degree becomes
this, where G is a network of size 20,000:

```python
degree_sequence = sorted([d for n, d in G.degree()], reverse=True)
dmax = max(degree_sequence)
```

Or if we want to know the name of the node as well:

```python
nmax, dmax = sorted(G.degree, key=lambda x: x[1], reverse=True)[0]
```

In my opinion, both ways are a bit too verbose for such an important network parameter.


## Pandas to the rescue {#pandas-to-the-rescue}

To get the max degree of network, we used G.degree which is a `DegreeView` class in networkx: a
dict-like structure. Other classes like `DegreeView` are:

-   `NodeView`
-   `EdgeView`
-   `NodeDataView`
-   `EdgeDataView`

As you can see, dict-like structures are the way to view the network attributes in
networkx. Dict-like structures are not optimal for data analysis, but they are just fine to define a
pandas dataframe with little code:

```python
import pandas as pd

# data frame with edgelist
df_edges = pd.DataFrame(G.edges(), columns=["node_1", "node_2"])

# data frame with edgelist + edge attributes
df_edges_data = pd.DataFrame(G.edges(data="weight"), columns=["node_1", "node_2", "weight"])

# degree dataframe
df_degree = pd.DataFrame(G.degree(), columns=["node", "degree"])

# weighted degree dataframe
df_weighted_degree = pd.DataFrame(G.degree(weight="weight"), columns=["node", "weighted degree"])
```
