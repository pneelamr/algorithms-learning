# Simulated annealing — Level 3: High-school student

> **Example problems:** Traveling Salesman Problem, VLSI circuit placement, Job-shop scheduling, Continuous function optimization  ·  **Type:** Metaheuristic  ·  **Guarantee:** No guarantee; stochastic
> **Used for:** Escaping local minima by accepting worse moves with cooling probability
> **Level 3 of 6** — high-school: a worked example with real numbers, numbered steps, and why it works. See sibling files for other levels.

---

## The problem, concretely

Same 4-city instance as the other artifacts:

|       | A  | B  | C  | D  |
|-------|----|----|----|----|
| **A** | –  | 10 | 15 | 20 |
| **B** | 10 | –  | 35 | 25 |
| **C** | 15 | 35 | –  | 30 |
| **D** | 20 | 25 | 30 | –  |

Optimum is **80** (A–B–D–C–A). Simulated annealing (SA) doesn't *build* a tour — like 2-opt, it **changes** one. What's new is *which* changes it accepts.

## The acceptance rule (the whole idea)

You're at some current tour. You propose a small change (say a 2-opt move) giving a length difference `ΔE = new − current`.

- If **`ΔE ≤ 0`** (the change is shorter or equal): **always accept.**
- If **`ΔE > 0`** (the change is *worse*): accept it anyway with probability

```
P = e^(−ΔE / T)
```

where **`T` is the temperature.** Roll a random number in [0,1); if it's below `P`, accept the worse tour; otherwise stay put. Then **lower `T`** a little and repeat.

Two things to notice in `P = e^(−ΔE/T)`: a **bigger** worsening `ΔE` makes `P` smaller (big mistakes are rarely accepted), and a **hotter** `T` makes `P` bigger (early on, you accept freely).

## A worked acceptance

Say your current tour is the optimum **A–B–D–C–A (80)** and SA proposes a 2-opt move to **A–B–C–D–A (95)** — that's worse by `ΔE = 95 − 80 = 15`. Should you accept this uphill move? It depends on temperature:

| Temperature `T` | `P = e^(−15/T)` | Meaning |
|----------------|------------------|---------|
| **100 (hot)**  | `e^(−0.15) ≈ 0.86` | accept ~86% of the time — roam freely |
| **30 (warm)**  | `e^(−0.5) ≈ 0.61`  | accept ~61% — still exploring |
| **5 (cold)**   | `e^(−3) ≈ 0.05`    | accept only ~5% — almost frozen |

So the *same* worsening move is very likely accepted when hot and almost always rejected when cold. **That cooling is what turns wild early exploration into careful late settling.** And crucially, SA always **remembers the best tour it has ever seen** (here, 80), so even if it wanders uphill, it never loses its best find.

> On a tiny 4-city map there's no bad valley to escape (every tour is one swap from the optimum), so this example shows the *mechanism*. On a 1000-city map, those occasional accepted uphill moves are exactly what lets SA climb out of a deep-but-not-best valley and find a better one.

## The numbered recipe

1. **Start** with any tour and a **high** temperature `T`.
2. **Propose** a small change (e.g., a 2-opt swap); compute `ΔE`.
3. **Accept** if `ΔE ≤ 0`; else accept with probability `e^(−ΔE/T)` (roll the dice).
4. **Cool:** lower `T` slightly (e.g., `T ← 0.99·T`).
5. **Track the best** tour seen. **Repeat** 2–4 until `T` is near zero; return the best.

## Why it works

- **Uphill moves are escapes.** Allowing the tour to get temporarily worse is the only way to leave a valley that's locally best but globally mediocre — exactly what pure 2-opt can't do.
- **Cooling balances explore vs. settle.** Hot = explore widely (accept almost anything); cold = exploit/polish (accept only improvements). Lowering `T` smoothly shifts from one to the other.
- **The exponential is "gentle first, strict later."** `e^(−ΔE/T)` automatically makes small worsenings easier to accept than big ones, at every temperature — a smooth, tunable picky-ness.

## The catch

There's **no guarantee** in practice (the theory only promises optimality if you cool *infinitely* slowly). Everything hinges on the **cooling schedule**: cool too fast and SA freezes into a bad tour like plain local search; too slow and it burns huge time. For the TSP specifically, a tuned Lin–Kernighan usually wins — SA's appeal is its **generality** (it also does chip layout, scheduling, and continuous tuning with the same recipe). It's the first of the "accept-worse-to-escape" family; **tabu search, genetic algorithms, ant colony, GRASP,** and **VNS** are different answers to the same escape problem.

