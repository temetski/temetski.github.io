---
layout: post
title: "Elementary Cellular Automata"
tags: [Python, Complex Systems]
---

Elementary Cellular Automata (ECA) was a toy model initially created by Von
Neumann and later on studied extensively by Stephen Wolfram, probably since he
was really bored. ECA is a simple model wherein you have cells with binary
states (1 or 0) that evolve through iterations. The evolution rules are defined
by the state at the previous timestep of the cells within the neighborhood the
the cell to be evaluated.

For this implementation, I defined functions that turn binary strings to
decimals and vice versa. This will facilitate the general creation of lookup
tables associated with the rules.

**In [1]:**

{% highlight python %}
%matplotlib inline
%config InlineBackend.figure_format = 'svg'
import numpy as np
import matplotlib.pyplot as plt
from **future** import division, print_function
from numba import jit
import seaborn as sns

@jit
def binary_str(num):
res = []
for i in range(7,-1,-1):
if num//(2**i)==1:
res.append(1)
num -= 2**i
else:
res.append(0)
return res

@jit
def binary_dec(arr):
dec = 0
for i in range(len(arr)):
dec += arr[-1-i]\*2\*\*i
return dec
{% endhighlight %}

I then initialized an empty array, and initialized the first row a 50-50 percent
chance of being 1 or 0 for each cell in the first row.

**In [2]:**

{% highlight python %}
SIZE = 50

ECA = np.zeros((SIZE,SIZE))
ECA[0,:] = 1
ECA[0, SIZE//2] = 0
{% endhighlight %}

I then allowed the system to follow the rules (Rule 30 in particular) and wait
for the resulting image...

**In [3]:**

{% highlight python %}
for i in range(1,SIZE):
for j in range(SIZE):
pattern = [ECA[i-1,k%SIZE] for k in [j-1,j,j+1]]
index = int(binary_dec(pattern))
ECA[i, j] = binary_str(26)[7-index]

with sns.axes_style('white'):
plt.figure(figsize=(5,5))
plt.imshow(ECA, cmap='gray', interpolation='nearest')
{% endhighlight %}

![svg]({{ BASE_PATH }}/assets/posts/elementary-cellular-automata_files/elementary-cellular-automata_5_0.svg)

# Characterizing ECA Rules

## Space: Entropy

Measure the Shannon Entropy
$$ H = \dfrac{1}{H*{max}} \sum*{i=0}^7 p*i\log{p_i} $$
where $p_i$ is the frequency of all configurations resulting in the microstate
$i$, where $H*{max}$ is the value of $H$ whenn all $p_i=\dfrac{1}{N}$ are equal.

$ \langle H \rangle$ is obtained after disregarding the transient period
(roughly half of the cell length in space).

**In [4]:**

{% highlight python %}
SIZE=128

def p_i(space_arr,N):
p_vals = np.zeros(N, dtype=np.bool)
states = np.array([binary_dec(space_arr[i-1:i+2]) for i in range(1,len(space_arr)-1)])
for i in range(N):
p_vals[i] = np.sum(states==i)
return p_vals/np.sum(p_vals)

N = 8
H_max = np.sum([1./N*np.log(1./N) for state_num in range(N)])
H = lambda timestep: np.sum([p*np.log(p+1e-10) for p in p_i(timestep,N)])/H_max

H_ave = lambda ECA: np.mean([H(timestep) for timestep in ECA[SIZE//2:,:]])
{% endhighlight %}

## Time: Kinetic Energy

$$ \langle K \rangle*t = \langle |s*{i+1}-s_i|^2\rangle_t $$
This essentially counts the number of times states are flipped averaged across
space and time.

**In [5]:**

{% highlight python %}
def K(evolution):
SIZE = len(evolution)
return np.mean([np.mean((evolution[time]-evolution[time-1])\*\*2) for time in range(SIZE//2,SIZE)])
{% endhighlight %}

An interesting observation is when you generate all ECA rules, measure their
respective $K$ and $H$ values, we will observe clustering for similarly classed
rules.

**In [6]:**

{% highlight python %}
def create_ECA(rule, SIZE):
ECA = np.zeros((SIZE,SIZE))
ECA[0,SIZE//2] = 1

# ECA[0,:] = 1\*(np.random.random(SIZE)>0.5)

    for i in range(1,SIZE):
        for j in range(SIZE):
            pattern = [ECA[i-1,k%SIZE] for k in [j-1,j,j+1]]
            index = int(binary_dec(pattern))
            ECA[i, j] = binary_str(rule)[7-index]
    return ECA

print(SIZE)
SIZE = 128
%time all_ECA = [create_ECA(rule, SIZE) for rule in range(SIZE)]
{% endhighlight %}

    128
    Wall time: 46.3 s

**In [7]:**

{% highlight python %}
CA1 = [0, 4, 16, 32, 36, 48, 54, 60, 62]
CA2 = [8, 24, 40, 56, 58]
CA3 = [2, 10, 12, 14, 18, 22, 26, 28, 30, 34, 38, 42, 44, 46, 50]
CA4 = [52, 110]
colors = []
for i in range(256):
if i in CA1:
colors.append('m')
elif i in CA2:
colors.append(u'g')
elif i in CA3:
colors.append(u'y')
elif i in CA4:
colors.append(u'b')
else:
colors.append(u'None')
{% endhighlight %}

**In [8]:**

{% highlight python %}
%time K_list = [K(ECA) for ECA in all_ECA]
%time H_list = [H_ave(ECA) for ECA in all_ECA]
{% endhighlight %}

    Wall time: 130 ms
    Wall time: 1.46 s

**In [9]:**

{% highlight python %}
plt.scatter(K_list,H_list, alpha=0.5, c=colors, s=20, linewidths=0)
plt.ylabel('$H$')
plt.xlabel('$K$')
{% endhighlight %}

    <matplotlib.text.Text at 0xd2d9fd0>

![svg]({{ BASE_PATH }}/assets/posts/elementary-cellular-automata_files/elementary-cellular-automata_14_1.svg)

### Clustering of ECA

Some form of clustering can be observed using the Entropy and Kinetic Energy
descriptors. However, this clustering cannot be traced back to the types of ECA
rules.
