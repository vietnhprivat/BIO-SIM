# Task 3 — Enzyme Kinetics Explanation
*DTU 27020 Biocomputing Part 1 — Section 2*

## Reaction Scheme

The enzyme reaction follows the classic scheme from the course (Ex2-1):

```
         k1       k3
E + S ⇌ ES → E + P
         k2
```

- **E** — enzyme
- **S** — substrate
- **ES** — enzyme-substrate complex
- **P** — product
- **k1** — diffusion-controlled binding rate (encounter of E and S in solution)
- **k2** — off rate of the ES complex (ES dissociates back to E + S)
- **k3** — catalytic rate (ES converts to product P, releasing E)

Initial conditions: `[E] = 1 M`, `[S] = 10 M`, `[ES] = 0 M`, `[P] = 0 M`

---

## Differential Equations (Mass Action Kinetics)

Applying mass action laws to each species (the direction of each reaction determines production/consumption):

| Species | ODE |
|---------|-----|
| E  | `d[E]/dt  = -k1·[E]·[S] + k2·[ES] + k3·[ES]` |
| S  | `d[S]/dt  = -k1·[E]·[S] + k2·[ES]` |
| ES | `d[ES]/dt =  k1·[E]·[S] - k2·[ES] - k3·[ES]` |
| P  | `d[P]/dt  =  k3·[ES]` |

> These are the ODEs Kaemika generates internally and shows under "Show initial CRN" in the output panel.

---

## Simulation Results (k2 = 1, k3 = 0.1)

**Parameters used:** `k1 = 1`, `k2 = 1`, `k3 = 1/10`

### What happens over 300 seconds:

1. **[ES] rises sharply** at the start — E and S bind rapidly (k1·[E]·[S] is large when both are high).
2. **[ES] then decays slowly** — once S is depleted, no new ES is formed and existing ES converts to P.
3. **[S] decreases steadily** — substrate is consumed by binding to E.
4. **[P] increases over time** — product accumulates at rate `k3·[ES]`.
5. **[E] is recycled** — enzyme is released after each catalytic cycle and is not consumed.

### Michaelis Constant (for reference)

From Ex2-2 in the course, the Michaelis-Menten constant is:

```
Km = (k2 + k3) / k1 = (1 + 0.1) / 1 = 1.1 M
```

This represents the substrate concentration at which the reaction rate is half its maximum. Since `[S]₀ = 10 M >> Km = 1.1 M`, the enzyme starts near its maximum rate.

---

## Effect of Changing k2 from 1 to 10

**k2** is the off rate — how fast ES falls apart back into E + S before k3 can act.

| Parameter | k2 = 1 | k2 = 10 |
|-----------|--------|---------|
| ES stability | High — complex stays bound | Low — complex dissociates quickly |
| Product formed | More P over 300s | Less P over 300s |
| Km | `(1 + 0.1)/1 = 1.1 M` | `(10 + 0.1)/1 = 10.1 M` |

**Interpretation:**

- When `k2 = 1`: the ES complex is stable relative to k3. There is time for k3 to catalyse the reaction → more product formed.
- When `k2 = 10`: ES dissociates 10× faster than k3 can act (`k2/k3 = 100`). Most ES complexes break apart before converting to P → substrate "escapes" without being converted → less product formed.

**Conclusion:** A higher k2 means a less efficient enzyme, since the substrate is less likely to be converted to product before it escapes the complex. This is also reflected in the higher Km — a higher Km means lower substrate affinity, requiring more substrate to reach half-maximal rate.

---

## Key Takeaway

This model is a **complete kinetic model** — it does not rely on the steady-state assumption (unlike Michaelis-Menten). Every species concentration is tracked explicitly over time as a system of ODEs, giving a more accurate picture of the reaction dynamics, especially at early time points.
