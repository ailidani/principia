---
title: "Neural Network"
date: 2021-05-29
draft: false
---

# Neural Network

Ever since the creation of computers, we are constantly searching for innovative ways of computing to solve difficult problems. Monte Carlo methods was invented during Manhattan Project for the development of the hydrogen bomb, and used widely in optimization, numerical integration, and sampling in probability distribution. Model Checker based on temporal logic is also a field with decades of research that proves correctness of software systems. State machine abstractions such as one from the automata theory play a major role in computation theory and compilers. But perhaps the most successful method developed since the early days of computing is the artificial neural networks, which was inspired by neural cells in animal central nervous system. The neural networks are still our best approach to AI today.

Artificial neural network is a computing process that transform any input data (signals) to any output value in a continues value space with combinations of linear and non-linear functions. A network is defined by its structure and enormous number of weights.


# Neuro-dynamics

We can study neural networks as non-linear dynamic systems and focus on its stability problem, which is referred to as neurodynamics.

$$C_j \frac{dv_j(t)}{dt} + \frac{v_j(t)}{R_j} = \sum_{i=1}^{N} w_{ji}x_i(t) + I_j$$

$C_j$ leakage capacitance
$R_j$ leakage resistance
$I_j$ current source externally applied bias

$x_j(t) = \varphi(v_j(t))$


## Feedforward Neural Network (Multilayer Perceptrons)
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


## Stochastic gradient descent (SGD)

$$w = w - \eta \nabla L(w) = w - \frac{\eta}{n} \sum_{i=1}^{n} \nabla L_i(w)$$
$\eta$ is learning rate (step size)
$L_i(w)$ is the value of loss function at $i$-th example

$$ w' = w + \eta (y_i - x_i w) x_i $$

Implicit Stochastic Gradient Descent (ISGD)
$$ w' = w + \frac{\eta}{1+\eta||x_i||^2} (y_i -x_iw) x_i $$
numerically stable for all $\eta$ as learning rate is normalized.

$w' = w - \eta \nabla L_i(w) + \alpha \Delta w$
$\alpha$ is exponential decay factor \[0, 1\].


## Adam (Adaptive Moment Estimation)

$$
\begin{align*}
m &= \beta_1 m + (1 - \beta_1) \nabla L \\
v &= \beta_2 v + (1 - \beta_2) (\nabla L)^2 \\
\hat{m} &= \frac{m}{1 - \beta_1^t} \\
\hat{v} &= \frac{v}{1 - \beta_2^t} \\
w' &= w - \eta \frac{\hat{m}}{\sqrt{\hat{v}} + \epsilon}
\end{align*}
$$

$\epsilon = 10^{-8}$
$\beta_1 = 0.9$
$\beta_2 = 0.999$

Adam stores the momentum for each parameter separately using update equations that consist of exponentially weighted moving averages for both the gradients and the squared gradients.

```go
// Adam (Adaptive Moment Estimation) https://arxiv.org/abs/1412.6980
type Adam struct {
    eta float64 // learning rate
    beta1 float64 // decay term
    beta2 float64 // decay term
    epsilon float64 // constant value to avoid zero-division
    
    v, m float64
}

func (a *Adam) Update(gradient float64, t int) float64 {
    t++
    a.m = a.beta1*a.m + (1.0-a.beta1)*gradient
    a.v = a.beta2*a.v + (1.0-a.beta2)*math.Pow(gradient, 2.0)
    m := a.m / (1.0 - math.Pow(a.beta1, float64(t)))
    v := a.v / (1.0 - math.Pow(a.beta2, float64(t)))
    return -a.eta * (m / (math.Sqrt(v) + a.epsilon))
}
```

## Normalization

Batch normalization
$$y = \frac{x-\mathbf{E}[x]}{\sqrt{\text{Var}[x]+\epsilon}} * \gamma + \beta$$

Layer normalization


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


## Spiking Neural Network

$$u_i(t) = \eta(t - \hat{t_i}) + \sum_j \sum_f \epsilon_{ij}(t - t_j^{(f)}) + u_{rest}$$

## Transformer

$$\text{Attention}(Q, K, V)=\text{softmax}_{seq}(\frac{QK^T}{\sqrt{d_k}})V$$
