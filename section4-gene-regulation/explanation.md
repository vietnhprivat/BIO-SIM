# Section 4 — Gene Regulation Explanation
*DTU 27020 Biocomputing Part 1 — Section 4*

## Biological Background

Most genes are not constitutively expressed (Section 3). Expression is controlled by **transcription factors** — proteins that bind to the DNA operator site of a promoter and either:
- **Repress** transcription (negative control): RNAP cannot bind → gene OFF
- **Activate** transcription (positive control): RNAP can bind → gene ON

The promoter can be in two states:
- **P'** (active) — RNAP can bind, gene is transcribed
- **P** (inactive) — operator occupied, transcription blocked

Conservation law:
```
P' + P = PT   (total promoter concentration is constant)
```

---

## Ex4-1 — Repressed Gene Expression (INV Gate)

### Reaction scheme

Transcription factor A binds the active promoter P' (written as `Pp` in Kaemika) to form the inactive complex P:

```
A + Pp ⇌ P     (binding/unbinding)
Pp     → Pp + B  (B produced only when promoter is active)
A      → Ø       (slow degradation, dA = 0.0001)
B      → Ø       (fast degradation, dB = 0.1)
```

### ODEs (Mass Action)

```
d[A]/dt  = -kon·[A]·[Pp] + koff·[P] - dA·[A]
d[Pp]/dt = -kon·[A]·[Pp] + koff·[P]
d[P]/dt  =  kon·[A]·[Pp] - koff·[P]
d[B]/dt  =  b·[Pp] - dB·[B]
```

### Steady-state promoter activity (Hill function, n=1)

From the course, at steady state with d[P]/dt = 0:

```
kon·[A]·[Pp] = koff·[P]
```

Defining the dissociation constant `kd = koff/kon`:

```
[P] = [A]·[Pp] / kd
```

Using conservation `[Pp] + [P] = PT`:

```
[Pp]/PT = 1 / (A/kd + 1)
```

The **promoter activity** (rate of B production) is:

```
promoter activity = b / (A/kd + 1)
```

With `kd = koff/kon = 1/1 = 1 M`:
- When `[A] = 0`: activity = b = 1 (full expression)
- When `[A] = kd = 1 M`: activity = b/2 (50% repressed)
- When `[A] >> kd`: activity → 0 (fully repressed)

### Simulation results (ex4_1_inv_gate.txt)

- **t = 0–50s**: A = 0 M → Pp fully active → B rises to steady state `b/dB = 1/0.1 = 10 M`
- **t > 50s**: A = 1 M injected → A binds Pp → B production drops → B falls
- This is **INV (inverter) logic**: when A is LOW, B is HIGH; when A is HIGH, B is LOW

### Extended toggle simulation (ex4_1_inv_toggle.txt)

A is toggled between 4 M and 0 M repeatedly, demonstrating repeated switching of B:

| Time | A | B |
|------|---|---|
| 0–50 | 0 M | HIGH (~10 M) |
| 50–150 | 4 M | LOW (~0.2 M) |
| 150–300 | 0 M | HIGH again |
| 300–400 | 4 M | LOW again |
| 400+ | 0 M | HIGH again |

At `[A] = 4 M`, `kd = 1 M`: activity = b/(4+1) = 0.2 → B_ss = 0.2/0.1 = 2 M (LOW)

---

## Ex4-2 — Cooperative Repression

### Why cooperativity?

A single protein binding gives a gradual (rectangular hyperbola) switch. Nature achieves sharper switches through **cooperativity**: multiple transcription factor molecules bind together to form a stronger, more switch-like interaction.

### Cooperative binding (n=2)

With two A molecules binding together to repress the promoter:

```
A + A + Pp → P    (two A's bind together)
P          → A + A + Pp
```

The Hill function extends to n cooperating factors:

```
promoter activity = b / ((A/kd)^n + 1)
```

For n=2, `kd = sqrt(koff/kon) = sqrt(1/1) = 1 M`:

| [A] | n=1 activity | n=2 activity |
|-----|-------------|-------------|
| 0 M | 1.0 | 1.0 |
| 1 M | 0.5 | 0.5 |
| 2 M | 0.33 | 0.2 |
| 4 M | 0.2 | 0.059 |
| 10 M | 0.09 | 0.0099 |

**Observation:** n=2 gives a sharper switch — B drops more steeply as A increases. At high [A], B is repressed more completely than with n=1, making the switch more digital.

---

## Section 4.2 — Activated Gene Expression

### Reaction scheme

The promoter logic is **reversed**: promoter P is inactive by default. Transcription factor A activates it by binding, producing the active form P' (`Pp`):

```
A + P  ⇌ Pp    (A activates the promoter)
Pp     → Pp + B  (B produced from active promoter)
A      → Ø
B      → Ø
```

Initial conditions: P @ 1 M (promoter starts inactive), A = 0 M → no B produced.

### Promoter activity

By analogous derivation:

```
[Pp]/PT = (A/kd) / (A/kd + 1) = A / (A + kd)
```

The **promoter activity** is:

```
promoter activity = b·(A/kd) / (A/kd + 1)
```

- When `[A] = 0`: activity = 0 (gene OFF)
- When `[A] = kd`: activity = b/2 (50% activated)
- When `[A] >> kd`: activity → b (fully activated)

### Simulation results (ex4_2_activated.txt)

- **t = 0–50s**: A = 0 M → P active, Pp = 0 → B = 0 (gene OFF)
- **t = 50–200s**: A = 4 M injected → A activates Pp → B rises to HIGH
- **t > 200s**: A removed → Pp falls → B falls back to 0
- This behaves as a **sensor**: B is produced only when A is present

### Comparison: Repressor vs Activator

| Property | Repressor (INV) | Activator |
|----------|----------------|-----------|
| Default state (no A) | Gene ON (B HIGH) | Gene OFF (B LOW) |
| When A present | Gene OFF (B LOW) | Gene ON (B HIGH) |
| Promoter default | Active (Pp = PT) | Inactive (P = PT) |
| Biological use | Switch OFF, protect from leaky expression | Sense/respond to signal |
