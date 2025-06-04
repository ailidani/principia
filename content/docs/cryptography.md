---
title: "Cryptography"
date: 2023-09-07
draft: false
---

# Shamir's Secret Sharing (SSS)

SSS has the property of information-theoretic security.

**Lagrange interpolation theorem**: $k$ points on the polynomial uniquely determines a polynomial of degree less than or equal $k-1$.

Assume secret $s$ can be represented as element $a_0$ of finite field $GF(q)$ and $q>n$. e.g. a byte $b \in GF(2^8)$
Randomly choose $k-1$ elements $a_1...a_{k-1}$ from $GF(q)$ and generate polynomial $f(x)= a_0 + a_1x + a_2x^2 +...+a_{k-1}x^{k-1}$.
Compute $n$ points of ($x, f(x)$) pair on $f(x)$ curve. e.g. $x=1...n$
Every share gets one point.
Obtain $a_0$ by interpolation: $$a_0 = f(0) = \sum_{j=0}^{k-1} y_j \prod_{m=0,m \neq j}^{k-1} \frac{x_m}{x_m-x_j}$$

```go
// an input/output pair
type point struct {
    x, y byte
}

// Lagrange interpolation
func interpolate(points []point, x byte) (value byte) {
    for i, a := range points {
        weight := byte(1)
        for j, b := range points {
            if i != j {
                top := x ^ b.x
                bottom := a.x ^ b.x
                factor := div(top, bottom)
                weight = mul(weight, factor)
            }
        }
        value = value ^ mul(weight, a.y)
    }
    return
}
```


To verify if $m$ out of $n$ shares are consistent, we only need to check whether the interpolation of $m$ points $(x_1, y_1),...(x_m, y_m)$ yields a polynomial with degree $k-1$ or not.

**Lagrange Interpolation** $$L(x) = \sum_{j=0}^{k} y_j \prod_{m=0,m\neq j}^{k} \frac{x-x_m}{x_j-x_m}$$
**Newton Polynomial** $$N(x) = \sum_{j=0}^{k} a_j \prod_{i=0}^{j-1}(x-x_i)$$
where $a_j = [y_0,...,y_j]$

$$N(x) = \sum_{i=0}^{k} \binom{s}{i} i! h^i [y_0,...y_i]$$
