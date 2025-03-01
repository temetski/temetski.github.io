---
layout: post
title: "Earthquake Networks"
tags: [Python, Complex Systems]
---
## Earthquake Data

I got the earthquake data from
[Phivolcs](http://www.phivolcs.dost.gov.ph/html/update_SOEPD/EQLatest.html)
website for earthquake that occured between March 1-9, 2016. The data was saved
to a `csv` file to be processed by `python`.
As is, the data did not need that much cleaning. I also used `pandas` to manage
the data.

**In [2]:**

{% highlight python %}
from __future__ import division, print_function
import numpy as np
import networkx as nx
import pandas as pd

earthquake_df = pd.read_csv('seismic.csv')

earthquake_df['Datetime'] = [pd.to_datetime(i) for i in earthquake_df['Date - Time']]
earthquake_df = earthquake_df.sort_values('Datetime', ascending=True, kind='quicksort')
earthquake_df.reset_index(drop=True, inplace=True)
earthquake_df = earthquake_df.drop(['Date - Time', 'Location'], axis=1)
{% endhighlight %}

In creating the network, we will need information about the distance between two
earthquake epicentres. The euclidean distance is inappropriate for this as the
distances are on the surface of the Earth, which is not a flat plane. Instead,
we will use the great circle distance or `haversine` formula, defined as
follows:

**In [3]:**

{% highlight python %}
def haversine(loc1, loc2):
    """
    Calculate the great circle distance between two points 
    on the earth (specified in decimal degrees)
    """
    lon1, lat1 = loc1['Longitude'], loc1['Latitude']
    lon2, lat2 = loc2['Longitude'], loc2['Latitude']
    # convert decimal degrees to radians 
    lon1, lat1, lon2, lat2 = np.radians([lon1, lat1, lon2, lat2])
    # haversine formula 
    dlon = lon2 - lon1 
    dlat = lat2 - lat1 
    a = np.sin(dlat/2)**2 + np.cos(lat1) * np.cos(lat2) * np.sin(dlon/2)**2
    c = 2 * np.arcsin(np.sqrt(a)) 
    km = 6367 * c
    return km
{% endhighlight %}

## Network construction

We will construct the earthquake network using locations of the earthquakes as
nodes.
The edges between the nodes will depend on the *Nearest record breaking*
criterion which has the following rules:

1. Two earthquakes $i$ and $j$ are connected if the occur consecutively in time.
2. An earthquake $k$ is connected to past earthquakes $i$ if the new earthquake
breaks the record $R_{ij}$ of previous earthquake pairs.

An earthquake record of a earthquake $i$ is the current shortest distance
between all other earthquake $j$. Thus a new earthquake $k$ is connected to an
older earthquake $i$ if $R_{ik}<R_{ij}$.

**In [4]:**

{% highlight python %}
%matplotlib inline
%config InlineBackend.figure_format = 'svg'
import matplotlib.pyplot as plt
import networkx as nx

loc = earthquake_df[['Longitude', 'Latitude']]

G = nx.DiGraph()

nodes = earthquake_df.index.values
G.add_nodes_from(nodes)
magnitude = [earthquake_df['Mag'][i] for i in nodes]
nx.set_node_attributes(G, 'datetime', earthquake_df['Datetime'].tolist())
nx.set_node_attributes(G, 'record', 1e10)

for i in range(1,len(earthquake_df['Datetime'])):
    distance = haversine(loc.iloc[i], loc.iloc[i-1])
    G.add_edge(i-1,i, {'distance':distance})
    G.node[i-1]['record'] = distance
    for j in range(i):
        node_dist = haversine(loc.iloc[i], loc.iloc[j])
        if node_dist < G.node[j]['record']:
            G.node[j]['record'] = node_dist
            G.add_edge(j,i, {'distance':node_dist})
{% endhighlight %}

**In [5]:**

{% highlight python %}
import mplleaflet
from mpl_toolkits.basemap import Basemap

fig = plt.figure(figsize=(10,10))
ax = fig.add_subplot(111)
m = Basemap(projection='merc',llcrnrlat=3,urcrnrlat=20, resolution='i',
            llcrnrlon=117,urcrnrlon=128, lat_ts=0, ellps='WGS84')
m.drawcountries(zorder=0)
m.fillcontinents(color='#555555')
m.drawmapboundary(fill_color='#111111')
# positions = {i:tuple(loc.iloc[i].values) for i in nodes}
positions = {i:m(*loc.iloc[i].values) for i in nodes}

s = nx.draw_networkx_nodes(G, ax=ax, pos=positions, node_color=np.array(magnitude),
                     alpha=0.7, cmap='YlOrRd', with_labels=False)
s.set_zorder(3)
edges = nx.draw_networkx_edges(G, ax=ax, pos=positions, edge_color="#aaaaaa", arrows=True)
edges.set_zorder(2)

plt.show()
{% endhighlight %}


![svg]({{ BASE_PATH }}/assets/posts/earthquake-networks_files/earthquake-networks_6_0.svg)


Connections in this network are directed. The directionality of links actually
implies some possible causal relation, hence, the directionality also tells us
the relative sequence of events. Future events do not point to past events, but
past events can point to future events.

## Degree distributions

The degree distribution is one of the usual metrics by which networks are
characterized. The earthquake network created in this entry is a directed graph,
wherein the directionality of links is important. Thus, we characterize the in
and out degree distributions of the network.

**In [6]:**

{% highlight python %}
from statsmodels.distributions.empirical_distribution import ECDF
out_deg = np.sort(G.out_degree().values())
in_deg = np.sort(G.in_degree().values())

def degree_distribution(degree):
    
    frequency = np.arange(len(degree))/float(len(degree))
    ecdf = ECDF(degree)

    return ecdf.x, 1-ecdf.y

fig = plt.figure()
ax = fig.add_subplot(111)
#     ax.scatter(degree, 1-frequency)
# ax.step(ecdf.x, 1-ecdf.y)
ax.set_xscale('log')
ax.set_yscale('log')
ax.set_xlabel("Degree")
ax.set_ylabel("CCDF")
ax.step(*degree_distribution(out_deg), label="Out Degree", color='r')
ax.step(*degree_distribution(in_deg), label="In Degree", color='b')
plt.legend()

{% endhighlight %}




    <matplotlib.legend.Legend at 0xa258a20>



![svg]({{ BASE_PATH }}/assets/posts/earthquake-networks_files/earthquake-networks_9_1.svg)

Unfortunately the dataset only contained 69 earthquakes, which is too small to
one order of magnitude.

At first glance, it appears that there are more high out-degree nodes than in-
degree ones. This tells us that we can link more previous quakes to many
different subsequent quakes. On the other hand, there are less connections that
can be made as to which previous earthquakes are connected to a future
earthquake.

I believe that a possible insight we can get from this small dataset is that a
single earthquake can cascade into more earthquakes. This is a reasonable claim
since we have an idea of the concept of aftershocks produced by earthquakes.
However, the size of the dataset is insufficient to back up any claims we draw
from the data.

## Magnitude distribution

Finally, we'll look into the magnitude distribution of the Earthquakes in the
recorded period. This analysis is a purely statistical one, independent of the
network structure of the earthquakes. Let's start by plotting the CCDF of the
earthquakes.

**In [7]:**

{% highlight python %}
magnitude = earthquake_df['Mag']

frequency = np.arange(len(magnitude))/float(len(magnitude))
ecdf = ECDF(magnitude)

fig = plt.figure()
ax = fig.add_subplot(111)
ax.set_xscale('log')
ax.set_yscale('log')
ax.set_xlabel("Magnitude")
ax.set_ylabel("CCDF")
ax.step(ecdf.x, 1-ecdf.y, color='r')

plt.legend()
{% endhighlight %}




    <matplotlib.legend.Legend at 0x8a4feb8>




![svg]({{ BASE_PATH }}/assets/posts/earthquake-networks_files/earthquake-networks_12_1.svg)


At first glance, we cannot characterize the distribution that results as a power
law. the low slope at low magnitudes means that there are only a few low
magnitude earthquakes recorded. This can most likely be attributed to the data
gathering of Phivolcs, wherein not all earthquakes are recorded. It would appear
that their cutoff is less than magnitude 1.9.

The sharp slope around 3 indicates that most earthquakes that occur are within
this magnitude. Unfortunately, as is with the degree distributions, the size of
the dataset is not significant enough to draw meaningful analysis from the
distribution of data.

## Clustering coefficient distribution


**In [12]:**

{% highlight python %}
cluster = nx.clustering(G.to_undirected()).values()

ecdf_clust = ECDF(cluster)

fig = plt.figure()
ax = fig.add_subplot(111)
# ax.set_xscale('log')
# ax.set_yscale('log')
ax.set_xlabel("Clustering coefficient")
ax.set_ylabel("CDF")
ax.step(ecdf_clust.x, ecdf_clust.y, color='r')

plt.legend(loc='best')
{% endhighlight %}

    E:\Applications\Anaconda\lib\site-packages\matplotlib\axes\_axes.py:519: UserWarning: No labelled objects found. Use label='...' kwarg on individual plots.
      warnings.warn("No labelled objects found. "
    


![svg]({{ BASE_PATH }}/assets/posts/earthquake-networks_files/earthquake-networks_15_1.svg)


The clustering coefficient distribution has a sharp increase at the center,
flattening out at the extreme values.
The form of this CDF appears to be [gaussian](https://en.wikipedia.org/wiki/Norm
al_distribution#Cumulative_distribution_function), shifted to the lower
clustering coefficients.
The clustering coefficients obtained are indeed larger than a [randomly
connected network](https://en.wikipedia.org/wiki/Network_science#Erd.C5.91s.E2.8
0.93R.C3.A9nyi_Random_Graph_model), implying the fact that there is an
underlying structure in the network.
Naturally, we'd expect that this is a consequence of the network construct
