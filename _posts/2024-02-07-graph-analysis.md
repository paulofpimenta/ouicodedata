---
layout: post
title:  "Unleashing the power of graph analysis with Networkx, NzViz and PyViz"
date:   2024-02-07 20:19:16 +0200
tags:
- Graph
- Data visualization
---



### Introduction
Graph neural netowrks (GNN) have got a lot of "attention" (:-)) in the past decade. Almost every type of data can be represent as a graph. When we combine the power of neural networks with some knwoledge of graph theory, we are able to achieve many tasks related to machine learning, such as node classification, edge prediction and even explainability of networks. In that sense, newtwork analysis can be a powerfull and usesuful approach as an exloratory data analysis approach to GNN. This post will focus on Network Analysis of a real data network presented by {%cite valentin2023%}. But before we dive into the graph analysis,  I will make a brief introduction about the dataset and the context in which it was produced.

### Disease outbreak

In the domain of epidemiology, an outbreak is defined as a sudden increase in the disease frequency, related to time, place, and observed population {%cite outbreak%}
The outbreaks of certain desease are usually reported by agencies that are spread around the globe. These agencies play a key role to minimize the effects of any epidemics. An alert triggered in a certain region may give enough time to take mesures of preventing a desease to spread. And that is the reason why the agencies must be present in all geographical levels (local, regional and global) on the globe.

However, not every desease is systematycally repported : some deaseases (or syntoms) are not reported by all the agencies that are present in the same region where the outreak is declared. Which may lead to some lack of trust and delay the process of confirming or not a possible outbreak.

### The dataset

The official dataset can be found [here](https://zenodo.org/records/7828530). This dataset contains sveral csv files, but will focus on 3 main files : 

* The `sources.csv` : represents the data about the source of outbreak declaration, 
* The `event.csv` : represents the data about each outbreak event, 
* The where $$ S $$ : represents all outbreak declared events

If we transform our data into a graph, `source` and `event` will be considered as node types, and `event_source` are edges of our graph. But before transforming the dataset into a graph, we need to know which type of graph we are working with
 
### Graph types

In graph theory, graphs can be classed by different ways (e.g. interconnectivity, number of edges, etc). An exaustive list (with a visual representation) with several types of graphs can be found [here] (https://www.javatpoint.com/graph-theory-types-of-graphs). In our case, every `source` of outbreak declares (edge) many `events`. With means that for __every__ source of outbreak, there is __at least one__ event associated to the that source. And one event can be associated to many sources. This type of graph is call a [bipartite graph](https://en.wikipedia.org/wiki/Bipartite_graph) : 

<br />
<p align="center">
  <img src="/img/posts/graph/bipartite.svg"  width="500">
</p>
<p align="center">Examples of bipartite graphs <br> Source https://https://mathworld.wolfram.com/BipartiteGraph.html</p>
<br />

complete bipartite graph or biclique is a special kind of bipartite graph where every vertex of the first set is connected to every vertex of the second set

### Buiding the graph with Networkx

[Networkx](https://networkx.org/) is a python library for network analysis, aloow creation, manipulation and many other features when working with simple and complex network. In order to create a graph using Networkx, we must map the the source dataframe into a tuple. Given the formal graph definition : 

$$ G = (V,E) $$

where $$ V $$ represent the vertices(nodes) of the graph and $$ E $$ its edges, we must first build some mapping to feed the graph creation using Networkx. For the nodes, the mappings are just list of tuples for each type of node, where each node can contain features, represented by a dictionary of attribute name and value. For the nodes (`source` and `event`) creation, the code is the same : 

```python
source_nodes_mappings = [(s,{"type_source":ts}) for s, ts in zip(list(source['source']),list(source['type_source']))]
event_nodes_mappings = [(e,{"region":r}) for e, r in zip(list(events['id_event']),list(events['region']))]
```
and the output with the top four nodes of `event` would be :

```python
[(323, {'region': 'EUR'}), (309, {'region': 'EUR'}), (353, {'region': 'EUR'}), (325, {'region': 'EUR'})]
```

Notice that for each node, we added a dictionary containing the attribute name and its value. We could as many as we want. But for the sake of simplicity, we added the type of source (for source nodes) and region (for event nodes). And it's done for nodes. 

To build the edges, we must build a triplet containing $$ (S,T,E) $$ where $$ S $$ is the source node (in our case, the source dataframe), $$ T $$ for target node (event dataframe) and $$ E $$ for every edge, represented by the event_source data frame. Since the edges are all maped in the `event_source` dataframe, we can proceed applying the following code : 

```python
source_event_mapping_list = [(s,e,{"num":n}) for s, e, n in zip(list(events_source_early['source']),
                                                                      list(events_source_early['id_event']),
                                                                      list(events_source_early['n']))]
```
and the output with the top four edges of edges would be :

```python
[('Channel NewsAsia', 293, {'num': 1}), ('DesMoinesRegister.com', 293, {'num': 1}), ('Devdiscourse', 293, {'num': 2}), ('Free Malaysia Today', 293, {'num': 1})]
```

As for nodes, we also decided to add the only one attribute (`n`) of each edge in the graph. Finally, we use the defined mapings to initialize a network using networkx :

```python
import networkx as nx

# Initialize the graph
G = nx.Graph()

# Add nodes with the node attribute "bipartite"
G.add_nodes_from(source_nodes, bipartite="Source Type",color="blue" )
G.add_nodes_from(event_nodes, bipartite="Region", color="red")

# Add edges only between nodes of opposite node sets
G.add_edges_from(source_event_mapping_list)

print(list(Ge.nodes)[:10])
print(list(Ge.edges)[:4])
```

The output :

```python
['OIE', 'Malaysia Vet Auth', 'Gulf Business News', 'Vietnam Vet Auth', 'Reuters', 'The Star Online', 'Sabbah Vet Auth', 'Devdiscourse', 'Deccan Chronicle', 'The Borneo Post']
[('OIE', 293), ('OIE', 294), ('OIE', 295), ('OIE', 296)]
```
NetworkX will use the node name

### Relabel nodes and edges

To

### Visualize graph with NzViz

Visualize graph with NzViz

### Indicators of centrality (centrality mesure)

In network analysis, indicators of centrality are really useful when we want to determine the importance of distinct nodes in a graph.  They generally reflect a unit's prominence; in different substantive settings, this may be its structural power, status, prestige, or visibility {%cite marsden2009%}.These indicators are based on calculations of some sort of distance, to later assign numbers or rakings to nodes within a graph corresponding to their network postion. There are several centrality mesures in the literature of graph theory, but for the sake of brevity, I will (try to) explain three among them : Betweeness centrality, Degree Centrality and Closeness Centrality

#### 1. Betweeness centrality

Betweenness centrality is a way of detecting the amount of influence a node has over the flow of information in a graph. The algorithm calculates shortest paths between all pairs of nodes in a graph. Each node receives a score, based on the number of shortest paths that pass through the node. The algorithm definition is given by :

$$ C_{b}(i) = \sum_{s\neq v\neq t} \frac{\sigma_{st}(v)} {\sigma_{st}} $$


where $$ \sigma_{st} $$ is the total number of shortest paths from node $$ s $$ to node $$ t $$ and $$ \sigma_{st}(v) $$ is the number of those paths that pass through $$ v $$ as long as $$ v $$ is not the end point

#### 2. Degree Centrality

Betweenness centrality is a way of detecting the amount of influence a node has over the flow of information in a graph. The algorithm calculates shortest paths between all pairs of nodes in a graph. Each node receives a score, based on the number of shortest paths that pass through the node. The algorithm definition is given by :

$$ g(v) = \sum_{s\neq v\neq t} \frac{\sigma_{st}(v)} {\sigma_{st}} $$


where $$ \sigma_{st} $$ is the total number of shortest paths from node $$ s $$ to node $$ t $$ and $$ \sigma_{st}(v) $$ is the number of those paths that pass through $$ v $$ as long as $$ v $$ is not the end point

#### 3. Closeness Centrality

A closeness measure conceives of a unit as central to the extent that it is related to other units via short geodesics:

$$ C_{c}(i) = \frac{N - 1} {\sum_{j=1}^{N}d_{ij}} $$

where $$ d_{ij} $$ is the distance (length of the shortest path) of the shortest path between nodes _i_ and _j_. Since the closiness centrality captures the average between each node and every other node in the graph, we use the normalized form of the average by multiplying $$ 1 / \sum_{j=1}^{N}d_{ij} $$ by $$ N - 1 $$. 

Low closeness centrality means that a node is directly connected (or “just one connection away") from most others in the network. In contrast, nodes in very peripheral locations may have high closeness centrality scores, indicating the high number of connections they need to take to connect to distant others in the network  {%cite hansen202031 %}


### Calculating indicators of centrality

Hopefullly, we won't have to implement by hand the algorithms shown above. The excelent python package `Networkx` has implemetations of these algorithms out of the box and ready to use. 


### Plot an interactive graph with Pyviz

Plot an interactive graph with Pyviz

{% include post_results/bipartite_communities.html %}

<br />

{% bibliography --cited %}
