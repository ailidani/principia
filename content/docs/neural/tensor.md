---
title: "Tensor"
date: 2025-09-05
draft: false
---

# Tensor

Tensor is the basic building block of all the supported operations. Tensor represents a $n$-dimensional array of type `T` numbers. If $n=0$, Tensor is a single number, i.e. scalar. If $n=1$, Tensor is a vector. If $n=2$, Tensor is a matrix.

The data type for a tensor can be any number or boolean which is used for mask tensors.
```go
// tensor represents a multi-dimensional array of elements of type T.
type tensor[T Number | bool] struct {
    data []T
    shape Shape
    strides []int
    offset int
}

type Shape []int
```

Adding auto-gradient to `Tensor` requires adding two more methods to the Tensor interface. Calculating gradient is only supported by non-boolean numbers.

```go
type Tensor[T tensor.Number] interface {
    tensor.Tensor[T]
    Grad() tensor.Tensor[T]
    Backward()
}
```
