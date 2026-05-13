<link href="https://fonts.googleapis.com/css2?family=Merriweather:wght@400;700&display=swap" rel="stylesheet">
<style>
body { font-family: "Merriweather", Georgia, serif !important; color: #111 !important; font-size: 16px !important; line-height: 1.8 !important; }
h1,h2,h3 { font-weight: 700 !important; color: #000 !important; }
a { color: #0044bb !important; }
p { color: #111 !important; }
</style>
**Independent Researcher · Numerical Computing · CPU Architecture**

Newmarket, ON, Canada

The IEEE 754 floating-point standard has served computing well for decades — but it carries fundamental limitations that become increasingly problematic in modern hardware design: subnormal number penalties, signed zero ambiguity, FPU dependency, and fixed precision.

This site presents research on an alternative approach: the **NPAt format** — a new representation of finite real numbers that addresses these limitations while remaining fully compatible with existing software.

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
|---|---|
| [NPAt-Core-Research](https://github.com/yur-spiridonov/NPAt-Core-Research) | Theoretical foundation of the NPAt format |
| [NPAt_algorithm](https://github.com/yur-spiridonov/NPAt_algorithm) | Full source code — open source |
| [PresentationNPat](https://github.com/yur-spiridonov/PresentationNPat) | Verification results: NPAt vs IEEE 754 |
| [Benchmark_Hardware-vs-NPAt-](https://github.com/yur-spiridonov/Benchmark_Hardware-vs-NPAt-) | Performance benchmark results |

---

## IEEE 754: Problems

The posts on this site examine specific limitations of the IEEE 754 standard and how the NPAt format addresses them:

- Subnormal numbers: performance penalty and special-case complexity
- Signed zero (±0): semantic ambiguity and its consequences
- FPU as a mandatory hardware block: cost, power, and portability
- Fixed precision: the challenge of supporting multiple precision levels

---

## About

**Iouri Spiridonov** · Independent Researcher

- Former Head of Laboratory at NPO "Agat"
- USSR State Prize Laureate · Medal for Labor Distinction
- 18 Invention Certificates and 2 patents in digital architecture
- Over 20 years of R&D in digital systems and numerical algorithms

[GitHub Profile](https://github.com/yur-spiridonov) · [LinkedIn](https://www.linkedin.com/in/iouri-spiridonov)
