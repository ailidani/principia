---
title: "Compressive Sensing"
date: 2016-05-27
draft: false
---

## Introduction

This survey is inspired by a seminar on the topic of crowd sensing systems. In which I have been exposed to the celebrated theory of compressive sensing, also known as compressive sampling or CS, a novel sensing paradigm that can help reduce the sampling rate of sensing tasks. Sampling is data acquisition protocol that aims to recover the original signal of interest. Such signals includes audio, medical images, radio, computer visions, etc. Traditionally, Shannon’s theorem states that: the sampling rate must be at least twice the maximum frequency present in the signal (Nyquist rate). However, CS suggest that it is possible to surpass the traditional limits, by the fact that we can represent many signals using only a few non-zero coefficients in a suitable basis or dictionary, utilizing nonlinear optimization to enable recovery of such signals from the small set of measurements. Another way to look at it is that rather than measuring at a high rate first, then compressing the sampled data, CS directly sense the data in a compressed form.

In this survey, we first provide a detailed review of the mathematical foundation underlying compressive sensing problem. Then we present the extensions of CS techniques.


## Compressive Sensing Primer

### Signals

Signals can be treated as real-valued functions $f$, with continuous or discrete domains, finite or infinite. Signals produced by natural or man-made systems, usually has discrete, finite domain. We can model such signal as linear structure as vectors living in an $n$-dimensional Euclidean vector space, denoted by $\mathbb{R}^n$.  Vector space allow us to add two signals together to generate a new, physically meaningful signal. Other geometry tools such as lengths, distances, angles can also be used to describe and compare signals of interest.

The length of a vector $x = (x_1, x_2, ..., x_n)$ in $n$-dimensional real vector space $\mathbb{R}^n$ can be given by the Euclidean norm:
$$|x|_2 = (x_1^2 + x_2^2 + ... + x_n^2)^{\frac{1}{2}}$$
The Euclidean distance between two points $x$ and $y$ is the length $|x-y|_2$ of the straight line between the two points. However, the Euclidean distance is insufficient in many situations of a given space. Instead, we make frequent use of the $p$-norms, which are defined for $p \in [0,\infty]$ as

$$ |x|_p = \begin{cases}
| \text{supp}(x) | & \quad p = 0; \\
( \sum_{i=1}^{n} | x_i |^p )^{\frac{1}{p}} & \quad p \in [1, \infty); \\
\text{max} | x_i | & \quad p = \infty \\
\end{cases} $$

where |supp($x$)| = { $i : x_i \neq 0$ } denotes the cardinality of support of $x$.
Also note that, in standard *inner product* in $\mathbb{R}^n$,
$$ \langle x, x \rangle = x^Tx = \sum_{i=1}^{n}x_i x_i $$
thus $\ell_2$ norm can be writen as $|x|_2 = \sqrt{\langle x,x \rangle}$.
The norms are typically used as a measure of the strength of a signal or size of an error.


### Bases

Any vector $f \in \mathbb{R}^n$ can be represented by a linear combination of basis vectors $\{ \psi_i \}_{i=1}^{n}$ and (unique) coefficients $\{x_i\}_{i=1}^{n}$, such that
$$f(t) = \sum_{i=1}^{n} x_i \psi_i(t)$$
Let $\Psi$ denote $n \times n$ matrix with $\psi_i$ as columns, $x$ denote vector with entries $x_i = \langle f,\psi_i \rangle$, we can rewrite the equation more compactly as
$$f = \Psi x$$
Discrete Fourier base and discrete cosine base are examples of typical choices for the sparsifying base $\Psi$.


### Sparsity

A signal $f$ is $S$-sparse when it has at most $S$ nonzeros, i.e., $|f|_0 \leq S$. Signals that are not themselves sparse, but can expressed as $f=\Psi x$ in some basis $\Psi$, where $|x|_0 \leq S$, can still be refered as $S$-sparse. When the signal has a sparse expansion, we can discard the small coefficients without much perceptual loss. Formally, $f_S(t)$ is obtained by keeping only the terms corresponding to the $S$ largest values of $x_i$ in above equation, such that $f_S := \Psi x_S$. We have
$$ | f - f_S |_{\ell_2} = | x - x_S |_{\ell_2} $$
and if the sorted magnitudes of $x_i$ decay quickly, we say $x$ is sparse or compressible, then $x$ is well approximated by $x_S$, therefore, the error $| f-f_S |_{\ell_2}$ is small. In other words, a large fraction of the coefficients can be ignored (set to 0) without much loss.


### Sensing Matrices

In this survey, we only consider the standard finite dimensional CS model. Specifically, given a signal $f \in \mathbb{R}^n$, we consider measurement systems that acquire $m$ linear measurements. This process is mathematically represent as
$$ y_k = \langle f, \phi_k \rangle $$
where $k = 1,...,m$. Let $A$ denote the $m\times n$ sensing matrix with vectors $ \phi_1^* ,..., \phi_m^* $ as rows (* is the complex transpose), we can rewrite equation as
$$ y = Af $$
The matrix $A$ represents a dimensionality reduction, i.e., it maps $\mathbb{R}^n$ into $\mathbb{R}^m$ where $n\gg m$. We assume that $f$ is a finite-length vector with a discrete-valued index (such as time or space).


### Incoherent Sampling

With given pair $(\Phi, \Psi)$ of orthobases of $\mathbb{R}^n$, $\Phi$ is sampling matrix as in equation~\ref{eq:measure}, $\Psi$ is basis used to represent $f$ in equation~\ref{eq:basis}.
Definition 1 The coherence between the sensing basis $\Phi$ and representation basis $\Psi$ is
\begin{equation}
\mu(\Phi, \Psi) = \sqrt{n} \cdot \max_{1\leq k,j\leq n} |\langle \phi_k, \psi_j \rangle|.
\end{equation}
The coherence measures the largest correlation between any two elements of $\Phi$ and $\Psi$ \cite{uncertainty}. If there is correlated elements, the coherence is large. Compressive sampling is mainly concerned with low coherence pairs.

Random matrices are highly incoherent with any fixed basis $\Psi$. If we select $\Phi$ uniformly at random, then with high probability, the coherence between $\Psi$ and $\Phi$ is $\mu(\Phi, \Psi) = \sqrt{2\log n}$.


## Signal Recovery via $\ell_1$ Minimization

|              |                    Key Notations                   |
|--------------|:--------------------------------------------------:|
|      $n$     | Size of original signal                            |
|      $m$     | Number of measurements                             |
|      $S$     | Size of the support of the signal in sparse domain |
|      $f$     | Original signal                                    |
|      $x$     | Coefficients                                       |
|      $e$     | Error in sample data                               |
|    $\Phi$    | $m \times n$ sampling matrix                       |
|    $\Psi$    | $m \times n$ sparsifying matrix                    |
| $A=\Phi\Psi$ | Sensing matrix                                     |

To summarize above sections, consider $f\in\mathbb{R}^n$ is a target signal we would like to reconstruct, $f$ can be decomposed under a certain base $\Psi$, i.e. $f = \Psi x$ where $x$ is the coefficient vector. $\Phi$ is a linear encoder which projects an $n$-dimensional data into an $m$-dimensional subspace ($m < n$). CS allows us to reconstruct sparse $f$ from its linear measurements
\begin{equation}
s = \Phi f = \Phi \Psi x
\label{eq:recover}
\end{equation}
In above equation, $m$ can be as small as $O(S\log\frac{n}{S})$ \cite{compressive}. The Sensing matrix $A$ as mentioned in Table 1 has to satisfy the Restricted Isometry Property (RIP) \cite{csstable}.

Finally, we can perform the $\ell_1$-norm minimization
\begin{equation}
(\mathrm{P}_1) \text{arg min}_{\hat{x} \in \mathbb{R}^n} \|\hat{x} \|_{\ell_1}, \text{ subject to } \Phi\Psi\hat{x} = y = Ax
\label{eq:l1min}
\end{equation}
where $\|x\|_{\ell_1} := \sum_i |x_i|$.

This $\ell_1$-norm minimization replaces the NP-hard $\ell_0$-norm minimization that directly searches for the sparsest $\hat{x}$. $\ell_1$-min can be solved in polynomial time by linear programming, besides, various greedy algorithms are also practical alternatives.



## Extensions

### Robust Compressive Sampling

In section~\ref{sec:math} we have show that how to recover sparse signals from a few measurements. However, to be powerful in practice, CS needs to be able to deal with nearly sparse signals and with noise. In real applications, measured data will inevitably be corrupted by some noise as the sensing device does not have infinite precision. Here, our goal is that small perturbations in the data should cause small perturbations in the reconstruction.
Recovering a vector $x\in\mathbb{R}^n$ from data
$$ y = Ax + e$$
where $A$ is the same $m\times n$ sensing matrix, and $e$ is a stochastic or deterministic unknown error term. We can modify the $\ell_1$ minimization with relaxed constraints for reconstruction:
\begin{equation}
(\mathrm{P}_1) \text{arg min}_{\hat{x} \in \mathbb{R}^n} \|\hat{x} \|_{\ell_1}, \text{ subject to } \|\Phi\Psi\hat{x} - y \|_{\ell_2} \leq e
\label{eq:l1min2}
\end{equation}


### Hybrid CS

In some problems, the compressive sensing model can be deployed in a hybrid way, in particular, CS is not to carry all the load. In such deployment, we can apply CS to measure only fine scale properties of the signal, while ordinary linear measurement and reconstruction was used to obtain the coarse scale properties of the signal.
For example, we can expand the object $x_0$ in the wavelet basis
$$x_0 = \sum_k \beta_{j_0,k}\phi_{j_0,k} + \sum_{j=j_0}^{j_1}\sum_k \alpha_{j,k}\psi_{j,k}$$
where $j_0$ is some specified coarse scale, $j_1$ is the finest scale, $\phi_{j_0,k}$ are mail wavelets at coarse scale and $\psi_{j,k}$ are fine scale female wavelets. $\alpha$ denote the grouping together of all wavelet coefficients, and $\beta$ denote the male coefficients. For male coarse scale coefficients, we take direct measurements, whereas for the female fine scale coefficients, we apply the CS scheme. Let
$$ y = \Phi\Psi^T x_0 $$
To reconstruct from these observations, consider the basis-pursuit optimization problem
\begin{equation}
(\mathrm{BP}) \min_{\hat{x}} \|\hat{x}\|_{\ell_1} \text{ subject to } y_n = \Phi\Psi^T\Psi \hat{x}
\end{equation}
Results show that Hybrid CS reconstruct signals with even fewer samples and the accuracy is evidently comparable.


### Multiscale CS

Inspired by the success of Hybrid CS, we may consider a fully multiscale deployment of CS. We may expand the object $x_0$ in the wavelet basis in the same way, then partition the coefficient vector as $[\beta_{j_0,\cdot}, \alpha_{j_0,\cdot},...,\alpha_{j_1-1,\cdot}]$. We then apply ordinary linear samping to measure the coefficients $\beta_{j_0,\cdot}$ directly, and then separately apply compressed sensing scale-by-scale, sampling data $y_j$ about the coefficients $\alpha_{j,\cdot}$ at level $j$ using an $n_j \times 2^j$ CS matrix $\Phi_j$. To obtain a reconstruction, we solve the sequence of problems
\begin{equation}
(\mathrm{BP_j})  \min_{\hat{x}} \|\hat{x}\|_{\ell_1} \text{ subject to } y_j = \Phi_j \hat{x}
\end{equation}


### Cost Aware CS

In applications like mobile crowd-sensing or wireless sensor networks, the resource burden of collecting samples is usually a major concern. However, the ordinary CS assumes that every sample has the same cost, it simply reduce the number of samples while the Cost Aware CS takes the cost of each sample into consideration. The new objective becomes not only maximizing recovery accuracy, also minimizing the sampling cost. CACS in \cite{cacs} uses Regularized Column Sum (RCS) to predict recovery accuracy, which is equivalent to Statistical RIP (Restricted Isometry Property) condition, to formulate the RCS-constrained Optimization (RO) to fund an optimal randomized sampling strategy $\pi$. In order to make RO solvable, the paper uses two relaxation methods:
\begin{align}
\sigma(F_{\Omega}) \leq \alpha \quad & \to \quad \mathbb{E}[\sigma(F_{\Omega})] \leq \alpha \\
\pi \in \{0,1\}^n \quad & \to \quad \pi \in [0,1]^n
\end{align}

Then we can use the following convex optimization problem to find an optimal randomized sampling strategy $\pi$ which satisfies the given RCS constraint with the lowest cost.
\begin{align*}
(\mathrm{P}) \min_{\pi} \quad & c^T \pi \\
\text{subject to} \quad & 1^T \pi = m \\
\quad & (Re(F_j)^T \pi)^2 + (Im(F_j)^T \pi)^2 \leq \alpha^2 \\
\quad & 0 \leq \pi_i \leq 1, i = 1,...,n.
\end{align*}
where $c$ is the cost map, $\pi$ is the sampling strategy, $m$ is the expected sample size, and $Re(F_j)$ and $Im(F_j)$ denote the real and imaginary component of the $j$th column in $F$. Above problem can be solved in polynomial time via standard interior point methods.

However, RO is centralized algorithm since it requires global information of the cost map, which makes it impractical. The paper further provides two decentralized algorithm that only uses partial information.


### Distributed CS

Distributed Compressive Sensing (DCS) \cite{dcs} improves the signal recovery performance of multi signal ensembles by exploiting both intra- and inter-signal correlation and sparsity structure. DCS is particularly useful in a scenario of multiple sensors carry out the compression in a distributed way without cooperation with each other and transmit the compressed signal to the sink node. At the sink node, the received signals from all the sensors are recovered jointly. The key of DCS is joint sparsity, defined as the sparsity of the entire signal ensemble. DCS consider three types of models as joint sparse signal:

Each signal is individually sparse, there are also common components shared by every signal, namely common information. This allows joint recovery with a reduced measurements.
All signals share the supports, the locations of the nonzero coefficients.
No signal is sparse itself, nevertheless, they share the large amount of common information.

One example application for DCS is in \cite{multimodal}, while combining multiple images of the same scene into a single image, they made the constant background image as common information and the variable foreground image as innovation information for efficiency of the process.


### Generalized Distributed CS

The Generalized Distributed Compressive Sensing (GDCS) \cite{gdcs} can improve sparse signal detection performance given arbitrary types of common information which are classified into not just full common information as DCS, but also a variety of partial common information.
