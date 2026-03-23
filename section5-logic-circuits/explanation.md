# Section 5 — Genetic Logic Circuits Explanation
*DTU 27020 Biocomputing Part 1 — Section 5*

## Background

From Section 4, the INV (inverter) gate implements: **B = NOT(A)**.

By chaining and combining INV gates, more complex logic functions can be built entirely from gene regulatory components. The P'/P model is used throughout — the promoter can be active (Pp) or inactive (P), and transcription factors determine which state it occupies.

---

## Ex5-1a — NOR Gate

### Logic

```
B = NOR(A, C) = NOT(A OR C)
```

B is HIGH only when **both** A=0 **and** C=0. If either input is present, B is OFF.

### Truth table

| A | C | B |
|---|---|---|
| 0 | 0 | HIGH |
| 1 | 0 | LOW  |
| 0 | 1 | LOW  |
| 1 | 1 | LOW  |

### Reaction scheme

Two repressors A and C independently compete to bind and inactivate the same promoter Pp:

```
A + Pp ⇌ PA     (A binds promoter → inactive PA)
C + Pp ⇌ PC     (C binds promoter → inactive PC)
Pp     → Pp + B  (B produced only from free Pp)
```

Promoter conservation:
```
[Pp] + [PA] + [PC] = PT
```

### ODEs

```
d[Pp]/dt = -kon·[A]·[Pp] + koff·[PA] - kon·[C]·[Pp] + koff·[PC]
d[PA]/dt =  kon·[A]·[Pp] - koff·[PA]
d[PC]/dt =  kon·[C]·[Pp] - koff·[PC]
d[B]/dt  =  b·[Pp] - dB·[B]
```

### Promoter activity (NOR)

At steady state, by analogous derivation to Section 4:

```
[Pp]/PT = 1 / (A/kd + C/kd + 1)
```

When either A or C exceeds kd, the promoter becomes occupied and B production stops. This is NOR logic.

### Simulation (ex5_1_nor_gate.txt)

The trigger sequence demonstrates all 4 input combinations:

| Time window | A | C | Expected B |
|-------------|---|---|------------|
| 0–100s | 0 | 0 | HIGH (~10 M) |
| 100–250s | 4 M | 0 | LOW |
| 250–400s | 0 | 0 | HIGH |
| 400–550s | 0 | 4 M | LOW |
| 550–700s | 0 | 0 | HIGH |
| 700–850s | 4 M | 4 M | LOW |

---

## Ex5-1b — OR Gate

### Logic

```
D = OR(A, C)
```

D is HIGH when **either** A=1 **or** C=1 (or both).

### Design: NOR → INV cascade

OR is constructed by feeding a NOR output into an INV stage:

```
D = NOT(NOR(A, C)) = OR(A, C)
```

**Stage 1 — NOR gate**: A and C repress Gene B → `B = NOR(A, C)`

**Stage 2 — INV gate**: B represses Gene D → `D = NOT(B) = NOT(NOR(A, C)) = OR(A, C)`

### Truth table

| A | C | B (NOR) | D = NOT(B) = OR |
|---|---|---------|-----------------|
| 0 | 0 | HIGH | LOW |
| 1 | 0 | LOW | HIGH |
| 0 | 1 | LOW | HIGH |
| 1 | 1 | LOW | HIGH |

### Reaction scheme

```
Stage 1 (NOR):
A + PpB ⇌ PAB    (A represses Gene B)
C + PpB ⇌ PCB    (C represses Gene B)
PpB → PpB + B    (B produced when promoter free)

Stage 2 (INV):
B + PpD ⇌ PBD    (B represses Gene D)
PpD → PpD + D    (D produced when promoter free)
```

### ODEs

```
d[B]/dt = b·[PpB] - dB·[B]
d[D]/dt = b·[PpD] - dD·[D]
```

Where `[PpB]/PT = 1/(A/kd + C/kd + 1)` and `[PpD]/PT = 1/(B/kd + 1)`

### Note on signal inversion

When A=0, C=0 at t=0: B is initially LOW (takes time to build up), so D initially spikes HIGH before falling as B accumulates. This is a biological propagation delay — the circuit reaches steady-state (D=LOW when A=0,C=0) after ~100s. Triggers are therefore applied after t=100s.

### Simulation (ex5_1_or_gate.txt)

Same trigger sequence as NOR, but D is the output:

| Time window | A | C | B (NOR) | D (OR) |
|-------------|---|---|---------|--------|
| 0–100s | 0 | 0 | rises to HIGH | falls to LOW |
| 100–250s | 4 M | 0 | LOW | HIGH |
| 250–400s | 0 | 0 | HIGH | LOW |
| 400–550s | 0 | 4 M | LOW | HIGH |
| 550–700s | 0 | 0 | HIGH | LOW |
| 700–850s | 4 M | 4 M | LOW | HIGH |

---

## Ex5-1c — Toggle Switch (Bistable)

### Concept

The toggle switch is a **bistable** genetic circuit — it has two stable steady states and can be flipped between them by a transient input signal. It acts as a biological memory element.

Design: two genes mutually repress each other:
- **A represses Gene B**: when A is HIGH, B is LOW
- **B represses Gene A**: when B is HIGH, A is LOW

This creates two self-consistent stable states:
- **State 1**: A HIGH, B LOW (A represses B, and with B LOW, nothing represses A)
- **State 2**: A LOW, B HIGH (B represses A, and with A LOW, nothing represses B)

### Why cooperativity is needed

With simple n=1 binding, cross-repression may not be strong enough for true bistability (the system can settle at an intermediate state). With **n=2 cooperative binding**, the switch is much sharper:

```
promoter activity = b / ((repressor/kd)^2 + 1)
```

When `[A] = 10 M`, `kd = sqrt(koff/kon) = sqrt(0.1) ≈ 0.316 M`:

```
PpB activity = 1 / ((10/0.316)^2 + 1) = 1 / (1000 + 1) ≈ 0.001
B_ss ≈ 0.001 / 0.1 = 0.01 M  → effectively zero → B stays LOW ✓
```

### Reaction scheme

```
B + B + PpA ⇌ PBA    (2 B molecules cooperatively repress Gene A)
PpA → PpA + A        (A produced when PpA is free)

A + A + PpB ⇌ PAB    (2 A molecules cooperatively repress Gene B)
PpB → PpB + B        (B produced when PpB is free)
```

### ODEs

```
d[A]/dt = b·[PpA] - dA·[A]
d[B]/dt = b·[PpB] - dB·[B]

[PpA]/PT = 1 / ((B/kd)^2 + 1)
[PpB]/PT = 1 / ((A/kd)^2 + 1)
```

### Bistability analysis

With `b = 1`, `dA = dB = 0.1`, `kd = sqrt(0.1) ≈ 0.316 M`:

Without repression: steady state = `b/d = 1/0.1 = 10 M`

At A=10 M, B repression is: `B_ss = (1/(1 + (10/0.316)^2)) / 0.1 ≈ 0.01 M`
With B=0.01 M, A production: `A_ss = (1/(1 + (0.01/0.316)^2)) / 0.1 ≈ 9.99 M` ✓ — self-consistent

Both states are self-consistent → **bistability confirmed**.

### Simulation (ex5_1_toggle_switch.txt)

| Time | Event | A | B |
|------|-------|---|---|
| 0 | Initial | 5 M (biased HIGH) | 0 M |
| 0–100s | Evolves | rises to ~10 M | falls to ~0 M (A-HIGH state) |
| t=100 | trigger B @ 8 M | drops | 8 M → stabilises HIGH |
| 100–300s | B-HIGH state | ~0 M | ~10 M |
| t=300 | trigger A @ 8 M | 8 M → stabilises HIGH | drops |
| 300–500s | A-HIGH state | ~10 M | ~0 M |

### Key properties

| Property | Value |
|----------|-------|
| Two stable states | (A≈10, B≈0) and (A≈0, B≈10) |
| Switch mechanism | Transient input pulse flips state |
| Memory | System holds state after input removed |
| Required for bistability | Cooperative binding (n=2) |
| Biological example | Cell fate decisions, phage lambda switch |

---

## Summary: Gate Comparison

| Gate | Inputs | Output | Kaemika implementation |
|------|--------|--------|----------------------|
| INV | A | NOT(A) | A represses Pp of Gene B |
| NOR | A, C | NOT(A OR C) | A and C each repress same Pp |
| OR | A, C | A OR C | NOR feeding into INV (2-stage) |
| Toggle | i1, i2 | bistable | Mutual repression with n=2 cooperativity |
