---
layout: post
title: "Is complexity science... science?"
tags: [Complex Systems]
---

# More is really different

The authors used Ising lattices to encode the behavior of a Cellular Automata.
In order to do this, they used layers of *designer Ising blocks*, which would map
ground state configurations into corresponding states of the CA.
I feel however that in their discussion, the Ising model only served as a sufficiently large
state vector in generating just as many different configurations of CAs.

That aside, the authors also invoke the use of a Turing machine that interacts with an Ising lattice,
such that complete knowledge of the lattice implies complete knowledge of the machine.
By arguing that decidability or predictability of $P$, which is dependent on the output of the Turing machine,
implies knowledge of the resulting configuration of the CA, but with the contradiction that this can only happen
for certain configurations of the CA, they say that $P$ is not decidable, and thus,
knowledge of the microscopic states (the Ising lattice underneath) cannot generally predict
the macroscopic behavior (Cellular Automata).
Thus their claim that complexity is indeed a phenomenon underivable from even a reductionist's *Theory of Everything*.

Although I agree with the conclusion of the authors, I do believe that the way they presented their argument was a little lackluster.
The physicist in me is more used to seeing plots of measurements of their system for different variables, and the many unfamiliar concepts they used in the layering of their model was highly unintuitive and difficult to follow.
I believe that the weakness of the argument is it was constructed in more of a *tell* fashion.
Again, I think it would be nice if there was also some *showing* of actual simulations and plots which could support their claims.

# From Complexity to Perplexity

The author tackles some leading ideas in the field of complexity science, particularly among its proponents at the Santa Fe Institute.
The first concept he criticizes is the vision of *complexologists* of a unifying theory of complexity: a bold claim that suggests that a single theory of complexity could explain complex phenomena in different fields.
He then proceeds to question how can a field gun for a unifying theory of complexity, when complexologist themselves cannot agree on a proper definition of complexity.
While I cannot deny the fact that **there are several ground breaking and interesting insights** resulting from complexity science, I personally agree that it might be too farfetched to claim a unified theory.
After all, seeing how difficulty a unifying theory is using the reductionist approach (which by definition I guess means these laws will indeed be common to everything in the universe), it might be much more difficult to find a unifying theory among totally different emergent systems.

The author then proceeds to critique *Artificial life*; computer simulations that use agents following a simple set of rules as a toy model to mimic biological systems.
Naomi Oreskes points out one major *problem* with Artificial life:

    "Verification and validation of models of natural systems is impossible."

True, automata models can never capture the full picture of real systems, nor can we definitely say that we have *verified* the model even if it fits really well with actual data.
Verification is best done when you have knowledge of all relevant variables, and have a way to control and isolate the dependent and independent variables.
However, I also believe that the author misses the point: complexity science gives us a tool to simplify a physical system, turn it into a model in which we now have control over some parameters, and see how the system behaves in response to these parameters.
In the context of traffic systems, **this is an invaluable tool** since it allows a researcher to *mess with traffic* without actually screwing over millions of citizens and the economy of a country.

Lastly, the author proceeds to tackle the concept of self-organized criticality (SOC) as a defining feature of complexity.
Bak, the proponent of SOC, discovered that his sandpile model organizes itself in such a way that when you look at the distribution of avalanche sizes, a power law distribution would emerge.
He then went on to note that other real world phenomenon such as the stock market, earthquakes, extinction of species and
even human brain waves also exhibit this power law distribution.
According to Bak, this power law distribution was the defining feature of SOC, and thus, complexity.
This was criticized by Crutchfield, saying the following:

    "If a theory applies to everything, it may really to nothing."
    "You need not only statistics but also mechanisms."

Crutchfield is correct in saying that statistics (power law) alone is not enough as a theory for a phenomenon.
However, my personal experience working in other complex systems research such as traffic also involves defining mechanisms for the model.
These mechanisms limit the validity of predictions that can be obtained from the model itself.
In conjunction with statistics, the insights obtained from the model are much more meaningful and insightful.

# Complex Systems Science: Dreams of Universality, Reality of Interdisciplinarity

This last paper attempts to deal the crushing blow to complex systems science's *dreams of universality*.
The authors show that while complex systems science claims that it has found a universal theory for real world systems, the universal trend in the field is the use of computers and computational techniques.
They criticize the fact that methodology, rather than theory, is the central denominator to the field of complex systems.
Ironically, they arrived at this conclusion using tools that complex systems scientists use: the network.

    "To try to become universal, theoretical approaches have to be “translated” into other disciplines."

Collaboration between disciplines manifested as *trading zones* shows that the collaboration appeared to one-way rather than mutual.
Interestingly, interdisciplinarity of the field suffers from the fact that no experimentalists (to the author's or my knowledge) actually refer to the results of the theorists of complexity science.
It may be that despite its attempts at unifying different fields, the different approach at solving problems may have alienated and discouraged collaboration as well.

The take away from all of this is that complex science is far of from being a universal theory, but it has taken some steps in the right direction.
Strong interdisciplinarity is key to discovering the possibility (or lack) of a unifying theme for real world systems.
As a relatively young science trying to tackle a really large problem (as if reductionism already wasn't hard enough...), it is reasonable tools and skill building would play a large role in its development.
Science shouldn't really be trigger happy at dismissing emerging theories that show promise, but at the same time, criticisms like this show shortcomings in the approach of a field, and actually provide feedback that can be used to actually improve.

# Final thoughts

It is interesting to read all three papers, which actively criticized/supported the field of complexity science.
Since my research field somewhat falls within complexity science, I have a rough idea of how the criticisms presented may or may not have merit.
I wouldn't actually say that it was very offensive to me as a researcher, but rather, enlightening that while scientists criticize some claims of complexity science, they also recognize its merits.
I see all these articles as a challenge for all complex scientists to up their game, and show that world the impact of this new paradigm of science.
Who knows, we might find a universal theory one way or another.

So is complexity science a science? *Definitely*.
