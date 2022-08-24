---
title: "Topology Mapping Algorithm"
date: 2021-08-23
draft: false
---

[ICS 2011](https://dl.acm.org/doi/abs/10.1145/1995896.1995909)

#### Formalization of the Problem

Network is modeled by a weighted graph $H=(V_H, \omega_H, R_H)$ where
$V_H$ is set of vertices/nodes
$\omega_H(u, v) \in \mathbb{R}$ are the weighted edges with $u, v \in V_H$
$R_H(u, v)$ is the routing function as a probability distribution of on the set of simple paths $P(u, v)$ between vertices $u$ and $v$.

A static application graph is modeled as weighted graph $A=(V_A, \omega_A)$

Topology mapping considers mapping $\sigma : V_A \mapsto V_H$. $\sigma$ assigns each vertex $s \in V_A$ in the application graph a target vertex $t \in V_H$ in the architecture (host) graph.

Cost metrics
- **Dilation** is maximum or sum of the pairwise distances of neighbors in $A$ mapped to $H$. Let $d_H(x,y)$ be shortest distance vertices $x, y\in V_H$, the weighted sum of dilation is defined as

$$\text{Dilation}(\sigma) = \sum_{u,v \in V_A} d_H(\sigma(u), \sigma(v)) \times \omega(u, v).$$

- **Congestion** counts how many communication pairs use a certain link. Let $p_e(u,v)$ be the probability that any of the routes from $u$ to $v$ crosses an edge $e \in V_H$, congestion of this edge $e$ is $C_e=\sum_{u,v\in V_A} p_e(u,v)$.

The maximum congestion $C_{max}=\max_{e}C_e$ often correlates strongly with the execution time of Bulk Synchronous Parallel (BSP) application.

----------

$A=(V_A, \omega)$ Application graph

$\omega(u,v)$ weight of the edge represents the volume of communication

$H=(V_H, C_H, c_H, R_H)$ Host graph

$C_H(u)$ is the number of processes that can be hosted at $u$, $C_H(u)=0$ for a switch

$c_H(u,v)$ is the capacity (bandwidth) of link

$R_H(u,v)$ routing algorithm

$$\text{Dilation}(u,v) = \sum_{p\in P(\sigma(u),\sigma(v))} R_H(\sigma(u), \sigma(v))(p) \times |p|$$

$$\text{Dilation}(\sigma) = \sum_{u,v \in A} \omega(u,v) \times \text{Dilation}(u,v)$$

$$\text{Traffic}(e) = \sum_{u,v \in A} \omega(u,v)(\sum_{p} R_H(\sigma(u), \sigma(v))(p))$$

$$\text{Congestion}(e) = \text{Traffic}(e) / c_H(e)$$

$$\text{Congestion}(\sigma) = \max_{e} \text{Congestion}(e)$$
