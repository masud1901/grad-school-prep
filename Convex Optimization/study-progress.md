# Convex Optimization — Study Progress

**Book:** Convex Optimization (Boyd & Vandenberghe)  
**Started:** Feb 2025  
**Current:** Chapter 1 complete

---

## Progress log

| Date       | Pages / section        | Notes |
|------------|------------------------|--------|
| Feb 2025   | ~1–5 (Ch 1 intro)      | Intro, optimization problem (1.1), linear vs nonlinear programs, data fitting, least-squares, solving least-squares (normal equations). |
| Feb 2025   | Ch 1 (full)           | **Chapter 1 done.** Problem formulation, linear/nonlinear programs, LP (1.5), least-squares, mature technology. |

---

## Concepts covered so far (Ch 1)

- **Optimization problem (1.1):** minimize \( f_0(x) \) s.t. \( f_i(x) \leq 0 \), \( h_j(x) = 0 \).
- **Linear program:** all \( f_i \) linear (affine); \( f_i(\alpha x + \beta y) = \alpha f_i(x) + \beta f_i(y) \).
- **Nonlinear program:** at least one \( f_i \) not linear.
- **Data fitting:** variables = model parameters; constraints = prior/limits; objective = misfit or likelihood.
- **Least-squares:** unconstrained; minimize \( \|Ax - b\|_2^2 \); solution via \( (A^T A)x = A^T b \) or \( x = (A^T A)^{-1} A^T b \).
- **Linear programming (1.5):** minimize \( c^T x \) s.t. \( a_i^T x \leq b_i \); parameters \( c, a_i, b_i \).
- **Mature technology:** least-squares and LP as special, well-solved cases of convex optimization.

---

## Next up

- **Chapter 2** (Convex sets / Convexity).
- Update this file when you start Ch 2 or finish sections.

---

*Update the "Current page" and the table above as you go.*
