---
layout: post
title: "Floating-point comparison: why epsilon is not a solution"
date: 2026-05-17
author: Iouri Spiridonov
tags: [NPAt, IEEE754, floating-point, numerical-computing]
---

Every programmer who has worked with floating-point numbers has encountered this problem. You expect two numbers to be equal — and they are not. Or you subtract one from the other and get a result that looks wrong.

Here is a concrete example:

```cpp
double a = 0.3e26;
double b = 0.4e26;
double d = 0.7e26;
double c = a + b;

std::cout << std::setprecision(17);
std::cout << "c = " << c << "\n";  // 7.0000000000000008e+25
std::cout << "d = " << d << "\n";  // 6.9999999999999999e+25
std::cout << "c - d = " << (c - d); // 8589934592
```

Expected: `c - d = 0`. Got: `8589934592`.

This is not a bug in your code. Not a compiler issue. This is a property of finite floating-point representation.

## Why this happens

The numbers 0.3, 0.4, and 0.7 are not exactly representable in binary format. Their exact values in double precision are:

```
a = 0.3e26 → 30000000000000000570425344
b = 0.4e26 → 40000000000000003623878656
d = 0.7e26 → 69999999999999999899336704
```

When we add `a + b`, the representation errors of both operands add up:

```
c = a + b → 70000000000000008489271296
```

The result differs from `d` by `8589934592`. This is not a computational error in the usual sense — it is the inevitable consequence of representing decimal fractions in binary with finite precision.

## The standard solution and its problem

The standard approach is to compare numbers using an epsilon threshold:

```cpp
// How do you choose epsilon?
// Without knowing the scale of your operands — you can't.
if (std::abs(c - d) < epsilon)  // which epsilon?
```

The right epsilon depends on the magnitude of your numbers. For numbers around 10²⁵ it will be one value, for numbers around 10⁻⁵ — a completely different one. There is no universal solution.

A relative epsilon is better:

```cpp
bool equal = std::abs(c - d) <= epsilon * std::max(std::abs(c), std::abs(d));
```

But this still requires the developer to choose an epsilon value and understand the scale of the computation. The algorithm itself provides no guidance.

## What NPAt adds

NPAt (Number with Point After t) is an arithmetic paradigm where a number is represented as:

```
X̂ = K × 2^E
```

where K and E are integers. This representation makes explicit something that IEEE 754 keeps implicit: the scale of the number and the precision of its representation.

### Flag q — the precision indicator

In NPAt every number carries a flag q:

- **q = 0** — the number is exact: no bits were lost during any operation in its history
- **q = 1** — the number is approximate: at least one bit was lost at some point

The flag is sticky: once q = 1, it remains 1 through all subsequent operations. This is analogous to the inexact flag in IEEE 754, but with one important difference — it is directly associated with the number, not with a global processor state.

### Guaranteed epsilon

When q = 1, the guaranteed bounds of the approximation are known automatically:

```
epsilon = 2^E
```

This follows directly from the representation. The least significant bit of the mantissa K corresponds to the value 2^E. Any result with flag q = 1 is accurate to within ±2^E.

No guessing. No manual calibration. The epsilon is a consequence of the representation.

## The example in NPAt

For our example, the NPAt representations are:

```
a = 0.3e26 = 6984919309616089 × 2³²  (q = 1)
b = 0.4e26 = 4656612873077393 × 2³³  (q = 1)
c = a + b  = 8149072527885438 × 2³³  (q = 1)
d = 0.7e26 = 69999999999999999899336704  (q = 1)

c - d = 8589934592 = 1 × 2³³
```

The numerical result is the same as IEEE 754. But NPAt also tells us:

- q = 1 → all numbers are approximate
- Guaranteed epsilon = 2^E = 2³³ = 8589934592
- The difference c − d equals exactly one least significant bit of the mantissa
- **Conclusion:** c and d are equal to within one LSB — the maximum possible closeness for approximate numbers in this case

## Three levels of information

NPAt provides the developer with three unambiguous cases for any comparison:

| Result | q | Meaning |
|--------|---|---------|
| c − d = 0 | 0 | **Exactly equal** |
| c − d = 0 | 1 | **Approximately equal** within ±2^E |
| c − d ≠ 0 | any | **Not equal** |

In IEEE 754, the first two cases are indistinguishable without manual epsilon handling. The developer must know the scale of the computation in advance and choose an appropriate threshold. NPAt derives that threshold automatically from the representation.

This is not a claim that NPAt is more precise. The numerical results are identical. It is a claim that NPAt gives the developer information that IEEE 754 does not.

## What comes next

The NPAt algorithm is open source and available on GitHub: [NPAt_algorithm](https://github.com/yur-spiridonov/NPAt_algorithm)

The repository contains four programs:

- Verification of bitwise identity with IEEE 754 over 10⁹ iterations
- Performance benchmark: NPAt vs hardware FPU
- Demonstration of the algorithm at different values of t (4…53)

The theoretical foundation of the NPAt paradigm — including a formal treatment of the q flag, guaranteed error bounds, and comparison with existing approaches — will be the subject of the next post.
