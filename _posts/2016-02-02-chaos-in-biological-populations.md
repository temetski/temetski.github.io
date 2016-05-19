---
layout: post
title: "Chaos in Biological Populations"
tags: python
---
## Single species model
Consider the non-linear equation
\begin{equation}
N_{t+1} = N_t\exp{[r(1-N_t/K)]},
\end{equation}
where $r$ and $K$ are the growth rate and carrying capacity, respectively. This
is a simple model for the population growth of a single species.

We can plot this equation for different values of $r$.

**In [1]:**

{% highlight python %}
%matplotlib inline
%config InlineBackend.figure_format = 'svg'
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

def subplotaxis_off(ax):
    # Turn off axis lines and ticks of the big subplot
    ax.spines['top'].set_color('none')
    ax.spines['bottom'].set_color('none')
    ax.spines['left'].set_color('none')
    ax.spines['right'].set_color('none')
    ax.tick_params(labelcolor='w', top='off', bottom='off', left='off', right='off')

N_0 = 1
K = 1000
eq1 = lambda r, N_0, K: N_0*np.exp(r*(1-N_0/K))


def population_growth(r, timesteps, N_0=1, K=1000):
    N = [N_0]
    for time in timesteps[1:]:
        N.append(eq1(r, N[-1], K))
    return np.array(N)

    
timesteps = np.arange(0, 30)
r_list = [1.8, 2.3, 2.6]
population = [population_growth(r, timesteps) for r in r_list]

fig, axes = plt.subplots(3, figsize=(3,3), sharex=True, dpi=200)
with sns.axes_style("whitegrid"):
    ax = fig.add_subplot(111, zorder=-1)
ax.grid(False)
for i, r in enumerate(r_list):
    axes[i].plot(timesteps, population[i]/K)
    axes[i].locator_params(axis='y', nbins=2)
    axes[i].set_ylim((0,3))
    axes[i].text(0.5, 0.99,r'$r=%.2f$' % r, horizontalalignment='center', 
                 verticalalignment='top', transform = axes[i].transAxes)
ax.set_ylabel(r"$N/K$")
ax.set_xlabel(r"time, $t$")
subplotaxis_off(ax)
{% endhighlight %}


![svg]({{ BASE_PATH }}/img/posts/chaos-in-biological-populations_files/chaos-in-biological-populations_1_0.svg)


In the above examples, we observe stable equilibrium ($r=1.8$), and stable
cycles with 2 points ($r=2.3$) or 4 points ($r=2.6$). Although not explicitly
shown, this stable behavior can be seen for any initial value of $N_0$ and $K$.

On the other hand, setting $r=3.3$, we will observe that the behavior of the
population depends on the initial value $N_0/K$. The code to produce the plots
is as follows:

**In [2]:**

{% highlight python %}
timesteps = np.arange(0, 30)
r_list = [3.3, 3.3, 3.3]
NK_ratio = [0.075, 1.5, 0.02]
population = [population_growth(r_list[i], timesteps, N_0=K*NK) for i, NK in enumerate(NK_ratio)]

fig, axes = plt.subplots(3, figsize=(3,3), sharex=True, dpi=200)
with sns.axes_style("whitegrid"):
    ax = fig.add_subplot(111, zorder=-1)
ax.grid(False)
for i, r in enumerate(r_list):
    axes[i].plot(timesteps, population[i]/K)
    axes[i].locator_params(axis='y', nbins=3)
    axes[i].set_ylim(0,3.8)
    axes[i].text(0.5, 0.99,r'$r=%.2f$' % r, horizontalalignment='center', 
                 verticalalignment='top', transform = axes[i].transAxes)
ax.set_ylabel(r"$N/K$")
ax.set_xlabel(r"time, $t$")
subplotaxis_off(ax)
{% endhighlight %}


![svg]({{ BASE_PATH }}/img/posts/chaos-in-biological-populations_files/chaos-in-biological-populations_3_0.svg)


The behavior seen above heavily depends on the initial seed value of $N_0$, and
we can see that although the 3 graphs have the same $r$ value, no two graphs
exhibit the same bahavior. This strong dependence on the inital values indicates
that the value of the growth rate $r$ is in the chaotic regime.

It is interesting to see that the equation we are modelling appears as a very
simple and deterministic population growth model, and yes it exhibits
deterministic, as well a arbitrarily dynamic behavior for large enough values of
the growth rate ($r>2.692$).

## Two competing species

We can also model the competition of two species using two coupled deterministic
equations similar to the single species case,
$$ N_1(t+1) = N_1(t)\exp{\{r_1[K_1 - \alpha_{11}N_1(t) -
\alpha_{12}N_2(t)]/K_1\}} $$
$$ N_2(t+1) = N_2(t)\exp{\{r_2[K_2 - \alpha_{21}N_1(t) -
\alpha_{22}N_2(t)]/K_2\}} $$

**In [3]:**

{% highlight python %}
N1_0 = 10
N2_0 = 10
K_1 = 1000
K_2 = 1000
alpha = np.array([[1,0.5], [0.5,1]])
species1 = lambda r_1, N_1, N_2, K_1=K_1, K_2=K_2: N_1*np.exp(r_1*(K_2 - alpha[0,0]*N_1 - alpha[0,1]*N_2)/K_2)
species2 = lambda r_2, N_1, N_2, K_1=K_1, K_2=K_2: N_2*np.exp(r_2*(K_2 - alpha[1,0]*N_1 - alpha[1,1]*N_2)/K_2)

def competition_growth(r_1, r_2, timesteps, N1_0=N1_0, N2_0=N2_0, K_1=K_1, K_2=K_2):
    N = np.zeros([len(timesteps), 2])
    N[0,:] = [N1_0, N2_0]
    for i, time in enumerate(timesteps):
        if i>0:
            N[i,:] = [species1(r_1, N[i-1,0], N[i-1,1]), species2(r_2, N[i-1,0], N[i-1,1])]
    return N
 
    
timesteps = np.arange(0, 30)
r_list = [1.1, 1.5, 2.5, 4]
population = [competition_growth(r, 2*r, timesteps)[:,0] for r in r_list]

fig, axes = plt.subplots(len(r_list), figsize=(3,4), sharex=True, dpi=200)
with sns.axes_style("whitegrid"):
    ax = fig.add_subplot(111, zorder=-1)
ax.grid(False)
for i, r in enumerate(r_list):
    axes[i].plot(timesteps, population[i]/K)
    axes[i].locator_params(axis='y', nbins=2)
    axes[i].set_ylim(0,np.max(population[i]/K)+0.5)
    axes[i].text(0.5, 0.99,r'$r=%.2f$' % r, horizontalalignment='center', 
                 verticalalignment='top', transform = axes[i].transAxes)
ax.set_ylabel(r"$N_1/K$")
ax.set_xlabel(r"time, $t$")
subplotaxis_off(ax)
{% endhighlight %}


![svg]({{ BASE_PATH }}/img/posts/chaos-in-biological-populations_files/chaos-in-biological-populations_6_0.svg)

