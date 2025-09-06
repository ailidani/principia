---
title: "Tensor"
date: 2025-09-05
draft: false
---

# Tensor

Tensor is the basic building block of all the supported operations. Tensor represents a $n$-dimensional array of type `T` numbers. If $n=0$, Tensor is a single number, i.e. scalar. If $n=1$, Tensor is a vector. If $n=2$, Tensor is a matrix.
```go
// Tensor represents a multi-dimensional array of elements of type T.
type Tensor[T Number] struct {
    data []T
    shape Shape
    strides []int
    offset int
}

type Shape []int
```
