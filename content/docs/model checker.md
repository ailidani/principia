---
title: "Model Checker in Go"
date: 2021-08-23
draft: false
---

## Temporal logic

More than 2000 years ago, Greek philocersor Aristotle, as the first man ever systematically studied _logic_, has already raised concers about how time (futures in particular) plays a complex role in logical reasoning in his book On Interpretation. Since then, very little was developed for millennia. It was until 1947, a formalization of temporal functions was created by Polish mathematician Jerzy Los. Ever since 1950s, lots of theoretical works has been developed in this area. Eventually, temporal logic has find its biggest application in computer science as model checker.

Some of them, like Computation Tree Logic (CTL) finds its application in computer science as model checkers.

CTL models are defined as transition system $M = (S, \to, L)$ where $S$ is the total set of states, $\to \subseteq S \times S$ is the finite set of state transitions, and $L$ is the labeling function for states. Under the definition of $M$, a model starts from initial state $s_0 \in S$, and generates all possible states by iteratively applying the well defined state transitions. Such model is the minimal abstraction needed to describe any algorithm, and a _model checker_ can verify any temporal properties on state while exploring the state space, as a result checking the correctness of the algorithm.

Imaging a simple model contains two possible actions, $a_0$ and $a_1$ on any state. Ideally, the two actions will generate a full binary tree of states as shown in Figure 1, thus the name computation tree. But in general, the state graph might be

an action may have no effect, i.e. the next state is equal to previous state.

{{< mermaid >}}
%%{init: {'theme':'dark'}}%%
flowchart LR;
s0 --a0--> s1
s0 --a1--> s2
s1 --a0--> s3
s1 --a1--> s4
s2 --a0--> s5
s2 --a1--> s6
{{< /mermaid >}}




Model checker is a type of program


Probabilistic CTL
