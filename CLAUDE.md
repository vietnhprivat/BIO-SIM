# CLAUDE.md — Biocomputing DTU 27020
## Instructions for Claude Code

This repo contains Kaemika simulation files for DTU course 27020 Biocomputing Part 1.
The goal is to organize all simulation scripts, results, and explanations cleanly.

---

## Repo Structure to Create

```
biocomputing-27020/
├── CLAUDE.md
├── README.md
├── section2-enzyme-kinetics/
│   ├── task3_enzyme_k2_1.txt        # Kaemika code, k2 = 1
│   ├── task3_enzyme_k2_10.txt       # Kaemika code, k2 = 10
│   └── explanation.md               # Written explanation of results
├── section3-gene-expression/
│   ├── task6_full_model.txt         # Full model with mRNA + Protein
│   ├── task7_simplified_model.txt   # Simplified model, Protein only
│   └── explanation.md               # Written explanation + parameter derivations
└── results/
    └── (screenshots of Kaemika graphs go here)
```

---

## File Contents to Write

### `section2-enzyme-kinetics/task3_enzyme_k2_1.txt`
```
number k1 = 1
number k2 = 1
number k3 = 1/10

species {E, S, ES, P}

amount E  @ 1 M
amount S  @ 10 M
amount ES @ 0 M
amount P  @ 0 M

E + S -> {k1} ES
ES -> {k2} E + S
ES -> {k3} E + P

equilibrate for 300
```

### `section2-enzyme-kinetics/task3_enzyme_k2_10.txt`
```
number k1 = 1
number k2 = 10
number k3 = 1/10

species {E, S, ES, P}

amount E  @ 1 M
amount S  @ 10 M
amount ES @ 0 M
amount P  @ 0 M

E + S -> {k1} ES
ES -> {k2} E + S
ES -> {k3} E + P

equilibrate for 300
```

### `section2-enzyme-kinetics/explanation.md`
```markdown
# Task 3 — Enzyme Kinetics Explanation

## Reaction Scheme
E + S ⇌ ES → E + P

## Differential Equations
- d[E]/dt  = -k1·[E]·[S] + k2·[ES] + k3·[ES]
- d[S]/dt  = -k1·[E]·[S] + k2·[ES]
- d[ES]/dt =  k1·[E]·[S] - k2·[ES] - k3·[ES]
- d[P]/dt  =  k3·[ES]

## Observations
- ES complex rapidly builds up then slowly decays
- S decreases as it is consumed
- P increases steadily over time
- E is recycled after each reaction

## Effect of changing k2 from 1 to 10
k2 is the off rate of the ES complex.
- k2 = 1:  ES is stable, k3 has time to convert ES to product → more P formed
- k2 = 10: ES breaks apart 10x faster than k3 can act → substrate escapes → less P formed
Conclusion: Higher k2 = less effective enzyme.
```

### `section3-gene-expression/task6_full_model.txt`
```
number k1 = 0.347
number k2 = 7.92
number d1 = 0.139
number d2 = 0.0198

species {mRNA, Protein}

amount mRNA    @ 0 M
amount Protein @ 0 M

# -> {k1} mRNA
mRNA -> {k2} mRNA + Protein
mRNA -> {d1} #
Protein -> {d2} #

equilibrate for 300
```

### `section3-gene-expression/task7_simplified_model.txt`
```
number k  = 19.8
number d2 = 0.0198

species {Protein}

amount Protein @ 0 M

# -> {k} Protein
Protein -> {d2} #

equilibrate for 300
```

### `section3-gene-expression/explanation.md`
```markdown
# Section 3 — Gene Expression Explanation

## Model
Gene → mRNA → Protein (full)
Gene → Protein (simplified)

## Parameter Derivations

### d1 — mRNA decay rate
formula: rate = ln(2) / half-life
d1 = 0.693 / 5 = 0.139 per minute

### d2 — protein degradation rate
E.coli divides every 35 minutes
d2 = 0.693 / 35 = 0.0198 per minute

### k1 — transcription rate
At steady state: 0 = k1 - d1·[mRNA]
k1 = d1 × 2.5 = 0.139 × 2.5 = 0.347 per minute

### k2 — translation rate
At steady state: 0 = k2·[mRNA] - d2·[Protein]
k2 = (d2 × 1000) / 2.5 = 7.92 per minute

## Task 6 Results
- mRNA steady state:    2.496 M  (expected 2.5 M)  ✅
- Protein steady state: 995.5 M  (expected 1000 M) ✅

## Task 7 — Simplified model
k = k2 × steady state mRNA = 7.92 × 2.5 = 19.8

## Key Difference Between Task 6 and Task 7
- Full model (Task 6):       Protein rises slowly — must wait for mRNA to build up first
- Simplified model (Task 7): Protein rises immediately — no mRNA delay
- Both reach same final steady state (~1000 M)
- Simplified model is faster but less biologically accurate
```

---

## Instructions for Claude Code

When setting up this repo, do the following:

1. **Create the full folder structure** exactly as shown above
2. **Write each Kaemika .txt file** with the exact code provided above
3. **Write each explanation.md** with the content provided above
4. **Create a top-level README.md** that summarizes:
   - What the course is (DTU 27020 Biocomputing)
   - What Kaemika is (chemical reaction simulator)
   - What each section covers
   - How to run the .txt files in Kaemika
5. **Do not modify the Kaemika syntax** — it is specific to the Kaemika simulator and is not standard code
6. **Keep all units consistent** — units are in M (molar) for concentrations and minutes for time in section 3, seconds for section 2

---

## Notes on Kaemika Syntax
- `number x = value` — defines a variable
- `species {A, B}` — declares chemical species
- `amount A @ 1 M` — sets initial concentration
- `A -> {k} B` — reaction from A to B at rate k
- `# or Ø` — means "nothing" (production from nothing or decay to nothing)
- `equilibrate for N` — runs simulation for N time units
- Avoid decimals like 0.1 — use fractions like 1/10 instead (Kaemika bug on some systems)
