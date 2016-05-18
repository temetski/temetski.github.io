---
layout: post
title:  "Erdos-Renyi"
date:   2016-02-02 21:06:33 +0800
categories: jekyll update
---

## Average Clustering Coefficient
$$\langle C \rangle = p  $$

## Average Shortest Path Length
$$\langle l \rangle = np  $$


```python
%matplotlib inline
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

def erdos_renyi(N=10000,p=0.8):
    adj = np.zeros((N,N), dtype=bool)

    for k in range(N):
        adj[k,k+1:] = np.random.random(N-(k+1))<p

    adj += adj.transpose()
    # adj = np.array(adj, dtype=bool)

    k = np.sum(adj, axis=1)
    return k
# mean_degree = np.mean(k)
# hist, edges = np.histogram(k, bins=np.max(k))


```


```python
k = erdos_renyi()
fig = plt.figure()
ax = fig.add_subplot(111)
histogram = ax.hist(k, bins=30, histtype='stepfilled')
ax.set_xlabel("Degree")
ax.set_ylabel("Frequency")
```

    7999.674



![png](/img/posts/Erdos%20Renyi_2_1.png)


## Average Degree

The degree of a node is defined to be the number of edges that node is a part of. For an Erdos-Renyi network, we expect the average degree to be
$$\langle k \rangle = np,  $$
for large networks $N$.


```python
N = np.arange(0,4,0.1)
degrees = [np.average(erdos_renyi(int(10**n))) for n in N]
```


```python
fig = plt.figure()
ax = fig.add_subplot(111)
ax.scatter(10**N, degrees)
ax.set_xlabel(r"$N$")
ax.set_ylabel(r"$\langle k \rangle$")
```




    <matplotlib.text.Text at 0xc54b240>




![png](/img/posts/Erdos%20Renyi_5_1.png)
