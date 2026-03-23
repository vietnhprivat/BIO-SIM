# Section 3 — Gene Expression Explanation
*DTU 27020 Biocomputing Part 1 — Section 3*

## Biological Model (Central Dogma)

The model follows the central dogma of molecular biology (Ex3-1 from the course):

```
Gene → (transcription) → mRNA → (translation) → Protein
              k1                        k2
                              ↓                  ↓
                              d1                 d2
                              Ø                  Ø
```

Both mRNA and Protein are degraded naturally. The gene acts as a **modifier** (template) — it is not consumed in the reaction, so it is modelled as a constant source (`#` in Kaemika).

---

## Differential Equations

From the course (Ex3-1), the kinetics of the model are described by two ODEs:

```
d[mRNA]/dt    = k1 - d1·[mRNA]
d[Protein]/dt = k2·[mRNA] - d2·[Protein]
```

- Production of mRNA is constant (gene copy number and RNA Polymerase assumed non-limiting).
- Protein production depends on current mRNA concentration.
- Both species decay at first-order rates (d1, d2).

---

## Parameter Derivations

All values use **minutes** as the time unit, consistent with E. coli biology.

### Q1 — d1: mRNA degradation rate

The course gives the formula (from exponential decay mathematics):

```
rate(k) = ln(2) / half-life(t½)
```

The typical mRNA half-life in E. coli is **5 minutes** (range: 2–8 min):

```
d1 = ln(2) / 5 = 0.693 / 5 = 0.139 min⁻¹
```

### Q2 — d2: protein dilution rate

Proteins in stable E. coli are not actively degraded — the dominant loss mechanism is **dilution by cell division**. E. coli divides every **35 minutes**:

```
d2 = ln(2) / 35 = 0.693 / 35 = 0.0198 min⁻¹
```

### Q3 — k1: transcription rate

At steady state, `d[mRNA]/dt = 0`, so:

```
0 = k1 - d1·[mRNA]_ss
k1 = d1 × [mRNA]_ss
```

The average number of mRNA molecules per gene in E. coli is **2.5 M**:

```
k1 = 0.139 × 2.5 = 0.347 min⁻¹
```

### Q4 — k2: translation rate

At steady state, `d[Protein]/dt = 0`, so:

```
0 = k2·[mRNA]_ss - d2·[Protein]_ss
k2 = (d2 × [Protein]_ss) / [mRNA]_ss
```

The average number of proteins per gene in E. coli is **1000 M**:

```
k2 = (0.0198 × 1000) / 2.5 = 19.8 / 2.5 = 7.92 min⁻¹
```

---

## Task 6 — Full Model Results

**Kaemika model:** `# → mRNA → Protein`, with mRNA degradation and protein dilution.

| Species | Expected steady state | Simulated result | Match? |
|---------|----------------------|-----------------|--------|
| mRNA    | 2.5 M                | ~2.496 M        | ✅     |
| Protein | 1000 M               | ~995.5 M        | ✅     |

**Verification using steady-state formulas:**

```
[mRNA]_ss    = k1 / d1                    = 0.347 / 0.139        = 2.497 M   ✅
[Protein]_ss = k2·[mRNA]_ss / d2          = 7.92 × 2.5 / 0.0198 = 1000 M    ✅
```

**Dynamics observed:**
- mRNA rises first and reaches steady state relatively quickly (~30–40 min).
- Protein rises more slowly — it must wait for mRNA to accumulate before translation can proceed at full rate.
- Both plateau at their expected steady-state values.

---

## Task 7 — Simplified Model

**Simplification (Ex3-2 from the course):** skip the mRNA level entirely and model:

```
Gene → Protein directly
```

The simplified ODE is:

```
d[Protein]/dt = k - d2·[Protein]
```

The combined rate `k` absorbs the steady-state mRNA level:

```
k = k2 × [mRNA]_ss = 7.92 × 2.5 = 19.8 min⁻¹
```

At steady state: `[Protein]_ss = k / d2 = 19.8 / 0.0198 = 1000 M` ✅

---

## Key Difference: Task 6 vs Task 7

| Aspect | Task 6 (Full model) | Task 7 (Simplified) |
|--------|---------------------|---------------------|
| Species tracked | mRNA + Protein | Protein only |
| Protein dynamics | Slow S-shaped rise — delayed by mRNA build-up | Immediate exponential rise from t = 0 |
| Steady state | ~1000 M | ~1000 M (same) |
| Biological accuracy | Higher — captures transcription delay | Lower — ignores mRNA dynamics |

**Conclusion:** The simplified model is mathematically valid at steady state, but it misses the lag phase where mRNA must first accumulate before protein synthesis ramps up. The full model is more biologically realistic, especially for studying transient behaviour (e.g., after a sudden change in gene expression).
