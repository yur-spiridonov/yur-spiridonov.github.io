---
layout: post
title: "Two paths to the same result: IEEE 754 emulation and the NPAt format"
date: 2026-05-13
author: Iouri Spiridonov
---

The problem of floating-point computation on processors without a hardware FPU is not new. In 2004, researchers at INRIA (Bertin, Brisebarre, Dupont de Dinechin et al.) published "A floating-point library for integer processors" — a C library for IEEE 754 single precision arithmetic on processors without a hardware FPU, specifically targeting VLIW cores of the ST200 family.

This work deserves attention: the authors solved a real engineering problem and obtained a measurable result — approximately 30% speedup over the previous library with full IEEE 754 compliance. However, the fundamental approach remained unchanged: reproduce the IEEE 754 mechanism using integer ALU operations.

---

## What the INRIA library does

Each operation is decomposed into four phases:

- **Phase A** — operand decomposition: sign, mantissa, exponent
- **Phase B** — special value handling: ±∞, ±0, NaN
- **Phase C** — result computation, including mantissa normalization
- **Phase D** — output formatting, overflow/underflow detection

Denormal numbers are explicitly supported with a dedicated code path. All four IEEE 754 rounding modes are supported. Results are bit-exact with the standard.

This is a careful and thorough implementation. But it reproduces all IEEE 754 mechanisms — including those that are the source of complexity.

---

## The fundamental difference

The INRIA library answers the question: **how to implement IEEE 754 without an FPU?**

NPAt answers a different question: **why are all these mechanisms needed in the first place?**

### Normalization

In IEEE 754, normalization is a mandatory step of every arithmetic operation. The mantissa must have the form 1.xxx...x. After each addition or subtraction, the result may be unnormalized and must be brought back to normal form — by shifting and adjusting the exponent.

In the INRIA library this is Phase C — a separate algorithmic block involving a leading zero count and shift.

In NPAt, normalization is absent by design. A number is represented as X̂ = S · K̂ · 2^E, where 0 ≤ K̂ < K_max is an integer, and the exponent E is strictly tied to K̂ such that the relation X̂ = S · K̂ · 2^E holds exactly under any change of the mantissa. There are no requirements on the form of the mantissa. After addition, K̂ either stays within the valid range or exceeds it — in which case a single right shift by one bit occurs with an update of E. No leading zero count. No normalization.

### Denormal numbers

In IEEE 754, denormal numbers are a special case requiring separate handling. The INRIA library offers two versions: without denormals (wo/D) and with denormals (w/D). The version with denormals is slower and larger.

In NPAt, denormal numbers do not exist. All values — from minimum to maximum — are handled by the same algorithm. This is not a simplification of the implementation; it is a property of the format itself.

### Precision

In IEEE 754, precision is fixed: float = 23-bit mantissa, double = 52-bit mantissa. Switching between them requires different data types and different code paths.

In NPAt, precision is a user-defined parameter t. The same accumulation loop runs at t=4, t=24 (float equivalent), t=53 (double equivalent), and any value in between. The code does not change.

---

## Comparison

| Characteristic | INRIA library | NPAt |
|---|---|---|
| Target platform | VLIW without FPU | Any processor without FPU |
| Denormal numbers | Supported via dedicated branch | Absent by design |
| Normalization | Mandatory (Phase C) | Not required |
| Precision | Fixed (SP or DP) | Parameter t from 4 to 53 bits |
| IEEE 754 compatibility | Full (single precision) | Bit-exact for any t |
| Speedup | ~30% vs previous library | ×1.46–2.26 vs hardware FPU |

---

## Conclusion

The INRIA work of 2004 is an answer to the question "how". NPAt is a question about "why".

The INRIA approach is an engineering solution within the existing paradigm. An IEEE 754 number is decomposed into its components — sign, mantissa, exponent — computations are performed using integers, and the result is reassembled into IEEE 754 format. The format remains the frame of reference: it is left temporarily during computation and returned to at the end.

NPAt decomposes and reassembles nothing. A number is represented directly as X̂ = S · K̂ · 2^E, where S, K̂ and E are integers. This is not a transformation of IEEE 754 — it is a direct consequence of the mathematical definition of a number. Any finite number can be represented as an integer K multiplied by a power of two. No hidden bits, no normalization, no return to an original format — because there is no "original format". There is only the arithmetic of finite numbers, to which all NPAt transformations strictly conform.

This is why normalization, denormal numbers and signed zero disappear in NPAt — not as an implementation simplification, but as a mathematical consequence: these concepts simply do not arise in ordinary arithmetic of finite numbers. The result is the same: bit-exact agreement with IEEE 754 double over 10⁹ iterations. The mechanism is fundamentally different.

---

*Source code and results: [github.com/yur-spiridonov/NPAt_algorithm](https://github.com/yur-spiridonov/NPAt_algorithm)*

*Bertin et al., "A floating-point library for integer processors", INRIA Research Report RR-5268, 2004.*
