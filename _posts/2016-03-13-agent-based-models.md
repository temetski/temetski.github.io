---
layout: post
title: "Agent Based Models"
tags: [Python, Complex Systems]
---
## Schelling model

    This work was made together with Alfred Abella

The Schelling model is one of the early agent-based automata models used to
study the formation of black and white communities. As racist as it may sound,
it does make sense that a population would naturally segregate into a community
with common traits. A more general treatment would probably consider multiple
layers such as proximity to a workplace, nationality.

In this model, cells on an $n\times n$ lattice would represent space on the map.
A cell may be `EMPTY` or occupied. When occupied, it can take on two values,
`BLACK` or `WHITE`, representing the race of agents. We initialize the lattice
`grids` with `percent_empty` `EMPTY` cells and `percent_white` `WHITE` cells.

**In [1]:**

{% highlight python %}
from __future__ import division
import numpy as np
import matplotlib.pyplot as plt
from numba import jit
from matplotlib import animation

EMPTY = 3
BLACK = 1
WHITE = 2

n = 128
percent_empty = 33.3
percent_white = 33.3
percent_thres = 50
n2 = n**2
grids = np.zeros((n,n))
emp = int((percent_empty / 100) * n**2)
two = int((percent_white / 100) * n**2)
tot = emp + two
rand_loc=np.random.permutation(n2)
init_emp=np.unravel_index(rand_loc[:emp+1], (n,n))
init_two=np.unravel_index(rand_loc[emp + 1:tot+1], (n,n))
init_one=np.unravel_index(rand_loc[tot+1:], (n,n))
grids[init_emp]=3
grids[init_two]=2
grids[init_one]=1

grid2 = np.zeros((n+2, n+2))
grid2[1:-1,1:-1] = grids

{% endhighlight %}

All agents moves along the grid until it comes within contact of other agents.
The agent looks at its neighborhood (in this case, a Moore neighborhood), and if
the number of similar neighbors exceeds a threshold, the agent will settle down
and stop moving. The rules stated are contained and executed within the function
`place_indiv()`.

**In [2]:**

{% highlight python %}
def place_indiv(grid2):
    num_unsat = [0,0]
    for i,j in [(i,j) for i in np.arange(1,n+1) for j in np.arange(1,n+1)]:
        value = grid2[i,j]
        if value != EMPTY:
            block = grid2[i - 1:i + 2,j - 1:j + 2]
            cnzero = np.sum(block != 0)
            cnvalue = np.sum(block == value)
            thres = round((percent_thres / 100) * cnzero)
            if cnvalue < thres:
                if value == BLACK:
                    num_unsat[0] += 1
                elif value == WHITE:
                    num_unsat[1] +=1
                grid2[i,j] = EMPTY
    return grid2, num_unsat
{% endhighlight %}

We will run this code for all agents at every timestep until all agents settle
down. At the same time, we are going to save a snapshot of the state of the
lattice after every 3 timesteps. This is to save on memory since there will be a
lot of timesteps before all agents settle down.

**In [3]:**

{% highlight python %}
FFMpegWriter = animation.writers['ffmpeg']
writer = FFMpegWriter(fps=30, extra_args=['-vcodec', 'libx264'])

fig =  plt.figure()
ax = fig.add_subplot(111)
fig.subplots_adjust(left=0, bottom=0, right=1, top=1, wspace=None, hspace=None)
ax.axis('off')
cmap = plt.get_cmap('bone', 3)
im = ax.imshow(grids, cmap=cmap, interpolation='nearest')
count=0
with writer.saving(fig, "writer_test.mp4", 64):
    while True:
        grid2, num_unsat = place_indiv(grid2)
        empty_y, empty_x = np.where(grid2 == EMPTY)
        perm = np.random.permutation(len(empty_y)) # perm determines which empty cells are filled
        grid2[empty_y[perm[:num_unsat[0] ] ], empty_x[perm[:num_unsat[0] ] ] ] = BLACK
        grid2[empty_y[perm[num_unsat[0]:sum(num_unsat)] ], empty_x[perm[num_unsat[0]:sum(num_unsat)] ] ] = WHITE
        count += 1
        if np.sum(grids!=grid2[1:-1,1:- 1])==0 or count>=1000:
#             print count

            break
        else:
            grids = (np.copy(grid2[1:-1,1:- 1])) # remove pad and store to im
            if count%3==0:
                im.set_data(grids)
                writer.grab_frame()
{% endhighlight %}

The video below shows the evolution of the Schelling model through time. We can
clearly see that from a randomly arranged lattice of `BLACK` and `WHITE` agents,
communities are formed. It would probably be interesting to characterize the
size distributions of these communities.

<video id="videoId" width="100%" autoplay controls>
  <source src="/vid/writer_test.mp4" type="video/mp4">

Your browser does not support the video tag.
</video>
