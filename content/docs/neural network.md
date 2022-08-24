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

The instantaneous error energy of neuron $j$ is defined by $\mathcal{E}_j(n) = e_j^2(n)/2$.

Total instantaneous error energy of the whole network $\mathcal{E}(n) = \sum_{j \in C} \mathcal{E}_j(n)$ where $C$ includes all neurons in output layer.

With $N$ training samples, the error energy averaged over the training sample or empirical risk is

$$
\begin{aligned}
\mathcal{E}_{av}(N) &= \frac{1}{N} \sum_{n=1}^{N} \mathcal{E}(n)\\\
                    &= \frac{1}{2} \sum_{j \in C} e_j^2(n)
\end{aligned}
$$


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
