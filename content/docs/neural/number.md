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

In addition to the fundamental arithmetic operations ($+,-,\times,\div$), it is advantageous to enumerate a collection of standard functions that are broadly applicable to the generic `Number` type, including but not limited to `Sign`, `Abs`, `Exp`, `Sqrt`, `Sin`, `Cos`, and `Log`.
```go
func Sign[T Number](x T) T {
	switch v := any(x).(type) {
	case int:
		if v > 0 {
			return T(1)
		} else if v < 0 {
			return T(-1)
		}
		return T(0)

	case float32:
		if v > 0 {
			return T(1)
		} else if v < 0 {
			return T(-1)
		}
		return T(0)

	case float64:
		if v > 0 {
			return T(1)
		} else if v < 0 {
			return T(-1)
		}
		return T(0)

	case complex64:
		if v == 0 {
			return T(0)
		}
		abs := float32(cmplx.Abs(complex128(v)))
		return any(v / complex(abs, 0)).(T)

	case complex128:
		if v == 0 {
			return T(0)
		}
		return any(v / complex(cmplx.Abs(v), 0)).(T)

	default:
		panic("unsupported type")
	}
}
```
