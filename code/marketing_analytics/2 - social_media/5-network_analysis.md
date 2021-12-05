# Network Analysis

## Directed networks

* Relationships are not mutual

Source node - where relationship begins
Target node - where relationship ends

### Example Networks from Twitter

* Retweet Network: Source node retweets target network
* Quote Network: Add commentary to an existing tweet
* Reply Network: Source user replies to tweet by target user


### Importing and visualizing twitter networks

Edge list - list all of edges between nodes

```python
import networkx as nx

## flatten and convert tweets to json

# Retweet network
G_rt = nx.from_pandas_edgelist(
    tweets,
    source = 'user-screen_name',
    target = 'retweeted_status-user-screen_name',
    create_using = nx.DiGraph()
)

# Basic plot
nx.draw_networkx(G_rt)
plt.axis('off')

# Improved plot options
sizes = [x[1]*100 for x in G_rt.degree()]
nx.draw_networkx(
    G_rt,
    node_size = sizes,
    with_labels = False,
    alpha = 0.6,
    width = 0.3
)
plt.axis('off')

# Circular layout
circle_pos = nx.circular_layout(G_rt)
nx.draw_networkx(
    G_rt,
    pos = circle_pos,
    node_size = sizes,
    with_labels = False,
    alpha = 0.6,
    width = 0.3
)
plt.axis('off')
plt.show()
```

## Node level metrics

* Centrality: Node importance
  * measures importance of a node in a network
  * ideas of "importance"
    * Degree centrality: measure of how many edges are connected to a particular node
      * Two types of degrees: In-degree, out-degree
    * Betweenness centrality: How many shortest paths between two nodes pass through this node

**Summary**
In the case of retweet network...

* In-Degree: Gets retweeted
* Out-Degree: Shares retweets
* Betweenness: Bridges different topic/ideology communities

In the case of replied network...

* In-Degree: Gets most replies
* Out-Degree: Replies the most
* Betweenness: Bridges different topic/ideology communities

```python
nx.in_degree_centrality(G_rt)
nx.out_degree_centrality(G_rt)

nx.betweenness_centrality(G_rt)

bc = nx.betweenness_centrality(G_rt)
betweenness = pd.DataFrame(
    list(bc.items()),
    columns = ['Name', 'Cent']
)
print(betweenness.sort_values('Cent', ascending=False).head())


# Calculating the ratio of retweets vs replies
degree_rt = pd.DataFrame(list(G_rt.in_degree()),
                         columns = ['screen_name', 'degree'])
degree_reply = pd.DataFrame(list(G_reply.in_degree()), 
                            columns = ['screen_name','degree'])
ratio = degree_rt.merge(degree_reply,
                        on = 'screen_name',
                        suffixes = ('_rt', '_reply'))
ratio['ratio'] = ratio['degree_reply'] / ratio['degree_rt']
```