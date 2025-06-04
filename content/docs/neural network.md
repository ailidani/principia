---
title: "Neural Network"
date: 2021-05-29
draft: false
---

## Multilayer Perceptrons
The model of each neuron in the network includes a nonlinear activation function that is differentiable.
Let $T = \{x(n), t(n)\}_{n=1}^N$ denote the training sample.
Let $y_i(n)$ denote the function signal produced by output neuron $j$.
The error signal produced at neuron $j$ is defined by

$$
\begin{aligned}
e_j(n) &= d_j(n) - y_j(n)\\\
       &= t_j(n) - y_j(n)
\end{aligned}
$$

The instantaneous error energy of neuron $j$ is defined by $E_j(n) = e_j^2(n)/2$.

Total instantaneous error energy of the whole network $E(n) = \sum_{j \in C} E_j(n)$ where $C$ includes all neurons in output layer.

With $N$ training samples, the error energy averaged over the training sample or empirical risk is

$$
\begin{aligned}
E_{av}(N) &= \frac{1}{N} \sum_{n=1}^{N} \mathcal{E}(n)\\\
                    &= \frac{1}{2} \sum_{j \in C} e_j^2(n)
\end{aligned}
$$

---

$$y(\mathbf{x, w}) = \sigma(\sum_{i=1}^{D}w_{ji}x_i + w_{j0})$$

$w^k_{ij}$ : weight for node $j$ in layer $k$ for incoming node $i$

$b^k_i$ : bias for node $i$ in layer $k$

$a^k_i$ : product sum plus bias (activation) for node $i$ in layer $k$

$o^k_i$ : output for node $i$ in layer $k$

$r_k$ : number of nodes in layer $k$


*The output layer*
$$\delta^m_j = (\hat{y}-y) f'(a^m_j)$$

*The hidden layer*
$$\delta^k_j = f'(a^k_j) \sum_{l=1}^{r^{k+1}} w_{jl}^{k+1} \delta_{l}^{k+1}$$


## Graph Neural Network

Message Passing Neural Networks (MPNNs)

Let $G=(V, E)$ be a graph, $N_u$ be the neighbourhood of node $u \in V$, $\mathbf{x_u}$ be features of node $u$, and $\mathbf{e_{uv}}$ be the feature of edge $(u, v) \in E$.
MPNN layer can be expressed as follows

$$\mathbf{h_u} = \phi(\mathbf{x_u}, \bigoplus_{u \in N_u} \psi(\mathbf{x_u}, \mathbf{x_v}, \mathbf{e_{uv}})),$$

where $\phi$ and $\psi$ are differentiable functions (e.g. artificial neural networks),
$\bigoplus$ is permutation invariant aggregation operator that can accept an arbitrary number of inputs (e.g. element-wise sum, mean, max).
$\phi$ is update function,
and $\psi$ is message function.

{{< mermaid >}}
%%{init: {'theme':'dark'}}%%
graph RL;
x0((x0))
f((f))
x1((x1))

x0 -.-> f
x1 -.-> f --> x0
{{< /mermaid >}}
