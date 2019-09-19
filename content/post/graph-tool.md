+++
title = "Create a network with Graph-tool and Pandas"
author = ["Rodrigo Dorantes-Gilardi"]
date = 2019-06-26T00:00:00-05:00
draft = false
+++

## Graph-tool {#graph-tool}

Recently, I discovered the python package [graph-tool](https://graph-tool.skewed.de/) for network analysis. The advantage of
`graph-tool` over the very popular `networkx` is the speed, as said by its creator:

<p class="verse">
Contrary to most other python modules with similar functionality, the core data structures and<br />
algorithms are implemented in C++, making extensive use of template metaprogramming, based heavily<br />
on the Boost Graph Library. This confers it a level of performance that is comparable (both in<br />
memory usage and computation time) to that of a pure C/C++ library.<br />
<br />
<br />
---Tiago Peixoto<br />
</p>

Some other really cool features (besides speed) of `graph-tool` are:

-   Filters and views get subgraphs without creating a new python object
-   Interactive drawing
-   Beautiful layouts
-   Topological algorithms

Cons:

-   Could use more documentation examples.


## Pandas + graph-tool {#pandas-plus-graph-tool}

It is very convenient to analyze networks using tables to look at:

-   Structural parameters (e.g. degree: mean, std, max min)
-   Node and edge lists with attributes

And even have the network in the form of an edge-list of the sort:

<style>
.my-table th,
.my-table td {
    padding: 20px;
    text-align: left;
}
</style>

<div class="ox-hugo-table my-table">
<div></div>

<div class="table-caption">
  <span class="table-number">Table 1</span>:
  Edge list
</div>

| node 1 | node 2 | color | weight |
|--------|--------|-------|--------|
| a      | b      | red   | 2      |
| a      | c      | black | 5      |
| b      | c      | red   | 1      |

</div>

Where the first two column define the nodes within the edge and the rest of the columns are edge
attributes (color and weight). Pandas is a python library for data analysis that focus on creating dataframes to work
with tables. Here, we will be reading a table using pandas and converting it to a graph-tool `Graph`
object.

```python
import pandas as pd
import graph_tool.all as gt
import numpy as np

# Load table
df = pd.read_csv("table.csv")

g = gt.Graph()
# Set property maps for edge attributes
weight = g.new_edge_property('int')
color = g.new_edge_property('string')

# Create numpy array of edgelist
edglist = df.values

# Add edges
node_id = g.add_edge_list(edgelist, hashed=True, eprops=[color, weight])

# Access node id of each vertex
for node in range(g.num_vertices()):
    print("Node {} has id: {}".format(node, node_id[node]))

```

Now we are ready to use the nice algorithms of graph-tool. Before saving your network, remember to
also save the node ids or labels as an **internal vertex property map** of the graph:

```python
g.vertex_properties['node_id'] = node_id

# Same with edge properties `color` and `weight`
g.edge_properties['color'] = color
g.edge_properties['weight'] = weight

# Save graph
g.save('my_network.graphml')
```
