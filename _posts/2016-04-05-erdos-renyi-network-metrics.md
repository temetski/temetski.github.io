---
layout: post
title: "Erdos-Renyi Network Metrics"
tags: [Python, Complex Systems]
toc: 
    sidebar: true
---
The Erdos Renyi networks consist of nodes that have a probability $p$ to be
connected to other nodes. This is otherwise known as a random network, and its
statistical properties and characteristics are well studied. In this entry, we
will be looking at the following:

## Table of Contents
1. [Creating an Adjacency Matrix](#creating-an-adjacency-matrix)
2. [Average Degree](#average-degree)
3. [Average Clustering Coefficient](#average-clustering-coefficient)
4. [Average Shortest Path Length](#average-shortest-path-length)
5. [References](#references)



## Creating an Adjacency Matrix

Before the days of `networkx`, networks were defined using adjacency matrices.
The values of the adjacency matrix $A$ are given by
\begin{equation}
 A_{ij} = 1
\end{equation}
when node $i$ is connected to node $j$.
In undirected networks, $A_{ij}=A_{ji}$, while this is not necessarily the case
for directed networks.

**In [1]:**

{% highlight python %}
%matplotlib inline
%config InlineBackend.figure_format = 'svg'
from __future__ import division, print_function
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

def erdos_renyi(N=10000,p=0.3):
    adj = np.zeros((N,N), dtype=bool)

    for k in range(N):
        adj[k,k+1:] = np.random.random(N-(k+1))<p

    adj += adj.transpose()
    return adj

def degree(adj):
    k = np.sum(adj, axis=1)
    return k
{% endhighlight %}

## Average Degree

The degree of a node is defined to be the number of edges that node is a part
of. For an Erdos-Renyi network, we expect the average degree to be
\begin{equation}\langle k \rangle = Np,  \end{equation}
for large networks $N$.

**In [2]:**

{% highlight python %}
k = degree(erdos_renyi())
fig = plt.figure()
ax = fig.add_subplot(111)
histogram = ax.hist(k, bins=30, histtype='stepfilled')
ax.set_xlabel("Degree")
ax.set_ylabel("Frequency")
{% endhighlight %}




    <matplotlib.text.Text at 0x9cce7f0>




![svg]({{ BASE_PATH }}/assets/posts/erdos-renyi-network-metrics_files/erdos-renyi-network-metrics_4_1.svg)


**In [3]:**

{% highlight python %}
N = np.arange(0,4,0.1)
degrees = [np.average(degree(erdos_renyi(int(10**n)))) for n in N]
{% endhighlight %}

**In [4]:**

{% highlight python %}
from scipy import stats

slope, intercept, r_value, p_value, std_err = stats.linregress(10**N,degrees)
fig = plt.figure()
ax = fig.add_subplot(111)
ax.plot(10**N, 10**N*slope+intercept, linestyle="--", color='r')
ax.scatter(10**N, degrees, zorder=3)
ax.set_xlabel(r"$N$")
ax.set_ylabel(r"$\langle k \rangle$")


print(r'$\langle k \rangle = %s $' % slope, ", p=", p_value)
{% endhighlight %}

    $\langle k \rangle = 0.300024590071 $ , p= 1.69578686191e-114
    


![svg]({{ BASE_PATH }}/assets/posts/erdos-renyi-network-metrics_files/erdos-renyi-network-metrics_6_1.svg)


## Average Clustering Coefficient

The clustering coefficient $C_i$ of a node $i$ is defined by the equation
\begin{equation}
C_i  = \frac{2t_i}{k_i(k_i-1)},
\end{equation}
where $t_i$ is the number of triangles that contain the node, and $k_i$ is the
degree of the node.

For an Erdos-Renyi network, the average clustering coefficient for large $N$
reduces to
\begin{equation}\langle C \rangle = p.  \end{equation}

Counting the number of triangles is not a trivial task, therefore, we will
*cheat* and use `networkx` to calculate the clustering coefficient of our
networks.

**In [5]:**

{% highlight python %}
def clustering(adj):
    import networkx as nx
    G = nx.Graph(adj)
    clust = nx.clustering(G).values()
    return clust

N = np.arange(1,3,0.1)
clust_co = [np.average(clustering(erdos_renyi(int(10**n)))) for n in N]
{% endhighlight %}

**In [6]:**

{% highlight python %}
from scipy import stats

fig = plt.figure()
ax = fig.add_subplot(111)
slope, intercept, r_value, p_value, std_err = stats.linregress(10**N[-6:], clust_co[-6:])
ax.plot(10**N, 10**N*slope+intercept, linestyle="--", color='r')
ax.scatter(10**N, clust_co, zorder=3)
ax.set_xlabel(r"$N$")
ax.set_ylabel(r"$\langle C \rangle$")

print(r'$p = %s $' % intercept, ", p=", p_value)
{% endhighlight %}

    $p = 0.299735616273 $ , p= 0.743566602627
    


![svg]({{ BASE_PATH }}/assets/posts/erdos-renyi-network-metrics_files/erdos-renyi-network-metrics_9_1.svg)


Note: obtaining the clustering coefficient can be really slow, with the naive
implementation having computational complexity $\mathcal{O}(n^3)$ . This is a
resource intensive process and for the sake of brevity, I calculated the
clustering coefficient for a smaller network. I obtained a result of $\langle C
\rangle = 0.3003$ which is close to $p=0.3$.

## Average Shortest Path Length

The shortest path length between two nodes $i$ and $j$ is the path with the
least number of edges connecting the two nodes. For an Erdos-Renyi network, the
average shortest path length $\langle l \rangle$ is given by

\begin{equation}\langle l \rangle = \frac{\ln{N}}{\ln{\langle k \rangle}}.
\end{equation}


**In [7]:**

{% highlight python %}
N = np.array([int(10**n) for n in np.arange(1,3,0.1)])
degrees = np.array([np.average(degree(erdos_renyi(n))) for n in N])

fig = plt.figure()
ax = fig.add_subplot(111)
slope, intercept, r_value, p_value, std_err = stats.linregress(np.log(degrees[-10:]), np.log(N[-10:]))
ax.plot(degrees, degrees*np.exp(slope), linestyle="--", color='r')
ax.scatter(degrees, N, zorder=3)
ax.set_ylabel(r"$N$")
ax.set_xlabel(r"$\langle k \rangle$")
ax.set_xscale('log')
ax.set_yscale('log')

print(r'$<l> = %s $' % slope, ", p=", p_value)
{% endhighlight %}

    $<l> = 1.00407716536 $ , p= 5.62192309984e-18
    


![svg]({{ BASE_PATH }}/assets/posts/erdos-renyi-network-metrics_files/erdos-renyi-network-metrics_12_1.svg)


It's clear form the figure that there is an exponential relationship between
$\langle k \rangle$ and $N$. The slope of the log-log plot obtained corresponds
to $\langle l \rangle =1.004$. To verify if the slope of the logarithmic plots
is indeed $\langle l \rangle$, we can use the built-in
`nx.average_shortest_path_length`.

**In [8]:**

{% highlight python %}
import networkx as nx
import numpy as np

def get_spl(N=1000, p=0.3):
    G = nx.Graph(nx.erdos_renyi_graph(N,p))
    spl = nx.average_shortest_path_length(G)
    k = np.average(G.degree().values())
    return spl, k

N_nodes = np.array([int(10**i) for i in np.arange(1.5,3.1,0.1)])
results = np.array([get_spl(n) for n in N_nodes])
spl = results[:, 0]
k = results[:, 1]
{% endhighlight %}

**In [9]:**

{% highlight python %}
fig = plt.figure()
ax = fig.add_subplot(111)
slope, intercept, r_value, p_value, std_err = stats.linregress(np.log(N_nodes)/np.log(k), spl)
ax.plot(np.log(N_nodes)/np.log(k), np.log(N_nodes)/np.log(k)*slope+intercept, linestyle="--", color='r')
ax.scatter(np.log(N_nodes)/np.log(k), spl, zorder=3)
ax.set_xlabel(r"$N$")
ax.set_ylabel(r"$\langle l \rangle$")
# ax.set_xscale('log')

{% endhighlight %}




    <matplotlib.text.Text at 0xd05ca90>




![svg]({{ BASE_PATH }}/assets/posts/erdos-renyi-network-metrics_files/erdos-renyi-network-metrics_15_1.svg)


The obtained result isn't quite what we are expecting.  However, it should be
noted that $N=1000$ used is quite small, and the analytical trend was derived
for a large random network. It is quite possible that we are unable to replicate
the results since the networks sizes used are too small, due to limitations in
the memory inefficient implementation of `networkx`.

## References
1. http://dx.doi.org/10.1103/PhysRevE.75.027105
2. http://people.seas.harvard.edu/~babis/tsourICDM08.pdf
