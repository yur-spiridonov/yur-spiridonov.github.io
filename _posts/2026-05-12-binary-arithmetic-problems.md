---
layout: post
title: "Binary arithmetic for decimal computations:  problems"
date: 2026-05-12
author: Iouri Spiridonov
---

This blog presents research on binary arithmetic applied to decimal computations. We will refer to this as binary-decimal arithmetic.

A close study of the IEEE 754 standard raised a number of questions that, in my view, remain without a satisfactory answer within the standard itself.

---

## 1. The number of reliable digits in a result is undefined

IEEE 754 provides no tools for tracking the accumulation of rounding errors. In a binary result represented in decimal form, it is impossible to determine how many decimal digits are reliable.

Example:
```
8.964285939997911e−7 − 8.866511009111255e−7 = 9.7774930886655929e−09
```
The exact value of this difference is: 9.7774930886656e−09.

Looking at the result, we cannot determine which digits are reliable. If a few more terms are added to the accumulation, the number of reliable digits may decrease further — by an unpredictable amount.

---

## 2. All input data is treated as exact

Rounding errors accumulate at every stage: during decimal-to-binary conversion of input data, during intermediate binary operations, and during binary-to-decimal conversion of the result.

The standard makes no distinction between the nature of input data. Whether a number is the result of an exact count or a measurement — and therefore inherently approximate — IEEE 754 treats it as exact. The representation error of an approximate number in binary format is neither recorded nor tracked — and worse, it propagates and accumulates through every subsequent computation.

---

## 3. Precision, Accuracy, and the correctness of a result

Precision is the number of digits in a normalized number. This parameter is not directly related to the correctness of the computed result. This is why additional concepts have been introduced in the literature, referred to by different authors under different names.

One such concept is the agreement between the decimal value of a binary result and the exact decimal result of the same significance. Verifying this agreement requires a calculator with sufficient precision — one that can represent the exact result without rounding. The number of significant digits in the exact result is determined by the number of significant digits in the input data.

**Accuracy** is the closeness of the binary result, expressed in decimal form, to the exact decimal result — that is, a measure of the deviation from it.

In ordinary arithmetic these concepts are absent — a result is either exact within the available precision, or rounded with a known bound on the error — unless interval arithmetic is used, where the error is tracked precisely at every step.

---

## 4. Qualitative rather than mathematical definitions

In the literature on binary arithmetic, one frequently encounters qualitative rather than mathematical descriptions: "very large", "very small", "in most cases", "almost". Such formulations are unacceptable in a rigorous mathematical context and suggest that the behavior of the system cannot always be described precisely.

---

## 5. Subnormal numbers and underflow

Ordinary arithmetic has no concept of subnormal numbers or underflow. These are artifacts of binary representation with a fixed-width format. They require special handling that adds complexity to both hardware and software implementations.

---

## 6. Signed zero

Ordinary arithmetic has no concept of signed zero. IEEE 754 defines both +0 and −0, which creates ambiguity: for example, 1/+0 = +∞ and 1/−0 = −∞, whereas for a true zero, 1/0 = NaN. This distinction must be explicitly accounted for in algorithms.

---

## 7. The difference of approximate numbers and zero

In ordinary arithmetic, a fundamental property holds: a − b = 0 if and only if a = b exactly.

If at least one operand is an approximate number, their difference cannot be zero — since an approximate number is by definition not strictly equal to any other number.

IEEE 754 violates this property. Subtracting two distinct decimal numbers that share the same binary representation yields +0 or −0. In practice, this creates a serious problem for comparison algorithms: since direct equality comparison is unreliable, the developer must introduce an epsilon threshold. Moreover, a separate epsilon must be calculated for each range of magnitudes — there is no universal solution.

---

## 8. Normalization

Ordinary arithmetic has no normalization in the sense used by IEEE 754. Normalization is a mechanism for unifying the representation of a number in a binary format with a fixed-width mantissa. It adds complexity to every arithmetic operation and is the source of subnormal numbers.

---

## 9. TotalOrder and its violation for decimal numbers

IEEE 754 mandates a total order (TotalOrder) for all numbers. However, this requirement holds only for binary arithmetic and is violated for binary-decimal arithmetic — for 7-significant-digit decimal numbers in float format and for 16-significant-digit decimal numbers in double format.

This means that two distinct decimal numbers map to the same binary representation — violating the uniqueness of the mapping and therefore TotalOrder for decimal values.

**Example for float:** the 7-digit decimal numbers 9.766246e−4 and 9.766245e−4, represented in IEEE 754 float format, have the identical decimal value:
```
9.76624549366533756256103515625e−4
```

**Example for double:** the integers 9897544098765433 and 9897544098765434, represented in IEEE 754 double format, have the identical value 9897544098765434. TotalOrder is violated even for integers.

Verified using the Float (IEEE 754 Single precision 32-bit) and Double (IEEE 754 Double precision 64-bit) converters.

---

The problems listed above are not incidental implementation flaws — they are inherent to the nature of binary-decimal arithmetic. In the following posts we will examine how the NPAt format approaches these problems from a fundamentally different angle.
