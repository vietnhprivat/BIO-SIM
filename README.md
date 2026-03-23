# DTU 27020 Biocomputing Part 1 — Simulation Files

## Course
**DTU 27020 — Biocomputing Part 1**
Technical University of Denmark. This course covers computational modelling of biological systems, including enzyme kinetics and gene expression.

## What is Kaemika?
[Kaemika](https://kaemika.app) is a chemical reaction network simulator. It lets you define species, reactions, and rate constants, then numerically integrates the resulting ODEs to simulate concentration dynamics over time.

## Repo Structure

```
BIO-SIM/
├── section2-enzyme-kinetics/
│   ├── task3_enzyme_k2_1.txt        # Enzyme kinetics simulation, k2 = 1
│   ├── task3_enzyme_k2_10.txt       # Enzyme kinetics simulation, k2 = 10
│   └── explanation.md               # ODEs, observations, and analysis
├── section3-gene-expression/
│   ├── task6_full_model.txt         # Full model: Gene → mRNA → Protein
│   ├── task7_simplified_model.txt   # Simplified model: Gene → Protein
│   └── explanation.md               # Parameter derivations and comparison
├── section4-gene-regulation/
│   ├── ex4_1_inv_gate.txt           # INV gate: A represses B (basic trigger)
│   ├── ex4_1_inv_toggle.txt         # INV gate: A toggled on/off repeatedly
│   ├── ex4_2_cooperative.txt        # Cooperative repression (n=2)
│   ├── ex4_2_activated.txt          # Activated gene expression
│   └── explanation.md               # Hill function, cooperativity, activation
├── section5-logic-circuits/
│   ├── ex5_1_nor_gate.txt           # NOR gate: A and C repress B
│   ├── ex5_1_or_gate.txt            # OR gate: NOR → INV cascade
│   ├── ex5_1_toggle_switch.txt      # Toggle switch: mutual repression, bistable
│   └── explanation.md               # Gate logic, truth tables, bistability
└── results/
    └── (screenshots of Kaemika graphs)
```

## Section Summaries

### Section 2 — Enzyme Kinetics
Models the classic Michaelis-Menten enzyme reaction:

> E + S ⇌ ES → E + P

Explores how the ES dissociation rate (k2) affects product formation. Higher k2 means the enzyme-substrate complex breaks apart before product can form, reducing enzyme effectiveness.

### Section 3 — Gene Expression
Models transcription and translation in E. coli:

- **Task 6 (full model):** Gene → mRNA → Protein — captures the mRNA delay before protein accumulates
- **Task 7 (simplified):** Gene → Protein directly — faster but less biologically accurate; both reach the same steady state (~1000 M protein)

Parameters are derived from biological data (mRNA half-life = 5 min, cell doubling time = 35 min).

### Section 4 — Gene Regulation
Models transcription factor control using the P'/P promoter model:

- **Ex4-1 (INV gate):** Protein A represses Gene B — B is HIGH when A is absent, LOW when A is present. Demonstrates the `trigger` command to inject A at t=50s.
- **Ex4-2 (cooperative):** Two A molecules cooperatively bind to repress Gene B — produces a sharper switch than single-molecule binding (Hill n=2 vs n=1).
- **Ex4-2 (activated):** A activates an otherwise-silent promoter — B is only expressed when A is present (sensor/enhancer behaviour).

Key formula — promoter activity for repression with n cooperating factors:
> `activity = b / ((A/kd)^n + 1)`  where `kd = koff/kon`

### Section 5 — Genetic Logic Circuits
Implements digital logic gates using gene regulatory components:

- **NOR gate:** A and C both repress the same promoter — B is HIGH only when both inputs are absent.
- **OR gate:** NOR output feeds into a second INV stage — D = NOT(NOR(A,C)) = OR(A,C).
- **Toggle switch:** Two genes mutually repress each other with n=2 cooperativity — bistable memory that flips between (A-HIGH, B-LOW) and (A-LOW, B-HIGH) states on transient input pulses.

## How to Run

1. Open [Kaemika](https://kaemika.app) in your browser (or use the desktop app)
2. Copy the contents of any `.txt` file
3. Paste into the Kaemika editor
4. Click **Run** to simulate

## Notes on Kaemika Syntax

| Syntax | Meaning |
|---|---|
| `number x = value` | Define a rate constant or variable |
| `species {A, B}` | Declare chemical species |
| `amount A @ 1 M` | Set initial concentration |
| `A -> {k} B` | Reaction A → B at rate k |
| `# -> {k} A` | Production of A from nothing (source) |
| `A -> {k} #` | Degradation of A (sink) |
| `equilibrate for N` | Run simulation for N time units |
| `trigger A @ 4 M when time > 50` | Set [A] = 4 M at t > 50 (step input) |
| `A + B -> {k} C` | Bimolecular reaction at rate k·[A]·[B] |
| `A <-> {kf} {kr} B` | Reversible reaction (forward/reverse rates) |

> **Note:** Use fractions (e.g., `1/10`) instead of decimals (e.g., `0.1`) to avoid a known Kaemika parsing bug on some systems.

## Units
- **Section 2:** concentrations in M (molar), time in seconds
- **Section 3:** concentrations in M (molar), time in minutes
- **Sections 4–5:** concentrations in M (molar), time in seconds
