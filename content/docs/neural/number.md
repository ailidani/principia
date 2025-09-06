---
title: "Number"
date: 2025-09-05
draft: false
---

# Number

In general, machine learning algorithm tries to solve problems using numerical computations, in a continues field like real numbers $\mathbf{R}$ or complex numbers $\mathbf{C}$.

Using Go genetics, lets define a `Number` type that includes all built-in numbers.
```go
// Number is a custom type set of constraints extending all number types
type Number interface {
    ~int | ~float32 | ~float64 | ~complex64 | ~complex128
}
```

However, some commonly used operations in machine learning, like `Max` assumes the numbers is directly comparable, but complex numbers are not. We define the `OrderedNumber` type by adding a `cmp.Ordered` constraint to all numbers.
```go
type OrderedNumber interface {
    Number
    cmp.Ordered
}
```

Finally, the system types are limited by their size (bits), therefore have a limited precision. We can add `BigNumber` types provided by Go standard library.
```go
type BigNumber interface {
    big.Float | big.Rat | big.Int
}
```
