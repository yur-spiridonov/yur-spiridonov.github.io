---
layout: home
list_title: "Latest Posts"
---

**Independent Researcher · Numerical Computing · CPU Architecture**

Newmarket, ON, Canada

The IEEE 754 floating-point standard has served computing well for decades — but it carries fundamental limitations that become increasingly problematic in modern hardware design: subnormal number penalties, signed zero ambiguity, FPU dependency, and fixed precision. A more detailed look at the problems we see arising from the use of the IEEE 754 standard is presented in the post — [Binary arithmetic for decimal computations: problems](/2026/05/12/binary-arithmetic-problems/).

This site presents research on an alternative approach: the **NPAt format** — a new representation of finite real numbers that addresses these limitations while remaining fully compatible with existing software. Alongside NPAt, this site is also where I publish shorter notes and reflections on numerical computing more broadly — not all of them tied to NPAt directly.

---

## NPAt Project

**NPAt (Number with Point After t)** is a new numerical format and computational paradigm. The first algorithm built on the NPAt format — NPAt-algorithm — demonstrates:

- **×1.46–2.26 faster** than hardware FPU (`ADDSD`) across a wide range of input types
- **Bit-exact IEEE 754 compatibility** — verified to 50 decimal places over 10⁹ iterations
- **No FPU required** — integer ALU only
- **No subnormal numbers** — all values handled by the same algorithm
- **Explicit exact zero** — resolves ±0 ambiguity in IEEE 754
- **User-controlled precision** — parameter `t` from 4 to 53 bits

### Repositories

| Repository | Description |
| --- | --- |
| [NPAt-Core-Research](https://github.com/yur-spiridonov/NPAt-Core-Research) | Theoretical foundation of the NPAt format |
| [NPAt_algorithm](https://github.com/yur-spiridonov/NPAt_algorithm) | Full source code — open source |
| [PresentationNPat](https://github.com/yur-spiridonov/PresentationNPat) | Verification results: NPAt vs IEEE 754 |
| [Benchmark_Hardware-vs-NPAt-](https://github.com/yur-spiridonov/Benchmark_Hardware-vs-NPAt-) | Performance benchmark results |

---

## Other Projects

**[fast-float-compare](https://github.com/yur-spiridonov/fast-float-compare)** — a small, standalone header-only C++ library. Not part of the NPAt format; it's a general property of the IEEE 754 bit layout, useful on its own: a branchless equality check for `double`/`float` using a fixed, physically-motivated 1-ULP threshold instead of a caller-chosen epsilon.

---

## Topics

Posts on this site are tagged by topic. Recurring tags include `NPAt`, `IEEE754`, `floating-point`, and `numerical-computing` — see the full post list below for everything published so far.

---
