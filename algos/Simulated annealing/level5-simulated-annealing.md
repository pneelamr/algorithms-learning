# Simulated annealing — Level 5: Graduate

> **Example problems:** Traveling Salesman Problem, VLSI circuit placement, Job-shop scheduling, Continuous function optimization  ·  **Type:** Metaheuristic  ·  **Guarantee:** No guarantee; stochastic
> **Used for:** Escaping local minima by accepting worse moves with cooling probability
> **Level 5 of 6** — graduate: the Markov-chain theory, Boltzmann stationarity, convergence, schedules, TSP practice. See sibling files for other levels.

---

## 1. SA as an inhomogeneous Markov chain

Fix a finite solution space `Ω`, cost `E: Ω → ℝ`, and a symmetric **proposal** kernel `Q(s, s')` supported on a neighborhood `N(s)` (e.g. random 2-opt moves, with `Q(s,s') = Q(s',s)`). At temperature `T`, SA's transition kernel is

```
P_T(s, s') = Q(s,s') · min(1, exp(−ΔE/T)),   s' ≠ s     (Metropolis acceptance)
P_T(s, s)  = 1 − Σ_{s'≠s} P_T(s, s').
```

For *fixed* `T`, `P_T` is an irreducible, aperiodic, reversible Markov chain whose unique **stationary distribution** is the **Boltzmann–Gibbs** measure

```
π_T(s) = exp(−E(s)/T) / Z_T,     Z_T = Σ_{u} exp(−E(u)/T).
```

Reversibility (detailed balance) `π_T(s) P_T(s,s') = π_T(s') P_T(s',s)` is immediate from the Metropolis form with symmetric `Q`. As `T → 0`, `π_T` concentrates on the global minimizers `argmin E`. SA runs this chain while **lowering `T`** — an *inhomogeneous* chain `P_{T_1}, P_{T_2}, …`.

## 2. Two regimes: equilibrium vs. annealing

- **Homogeneous (fixed `T`, `L → ∞`):** the chain mixes to `π_T`; sampling at decreasing temperatures with full equilibration at each is the idealized "quasi-static" annealing. Mixing time governs how large `L` must be.
- **Inhomogeneous (cooling during the run):** practical SA never equilibrates; convergence depends on cooling *slowly enough relative to mixing*.

**Convergence theorem (Hajek 1988).** The inhomogeneous chain converges (in probability) to the global optimum **iff** the schedule satisfies `Σ_k exp(−d* / T_k) = ∞`, where `d*` is the **maximum depth** of any non-global local minimum (the highest barrier that must be climbed). The canonical sufficient schedule is logarithmic:

```
T_k = c / log(k + 2),   with  c ≥ d*.
```

So global convergence requires `T_k` to decay **no faster than logarithmically** — exponentially slow in iteration count. Geman & Geman (1984) gave the analogous result for Gibbs sampling/annealing. These certify soundness but are useless as finite-time bounds; practical schedules (geometric `T←αT`) **forfeit** the guarantee.

## 3. The role of barriers and landscape

`d*` — the deepest barrier separating a local minimum from a better basin — controls everything: it sets the constant `c` in the convergence schedule and explains *why* rugged landscapes (large `d*`) need slow cooling. This connects SA to **landscape theory**: the same big-valley structure that makes chained Lin–Kernighan effective on TSP also bounds the barriers SA must cross. SA's "temperature crosses barriers of height `≲ T`" picture is the probabilistic analogue of LK's "double-bridge jumps a basin."

## 4. Design parameters as theory-guided knobs

- **Initial temperature `T0`:** choose so the uphill **acceptance ratio** `χ0 = (accepted uphill)/(proposed uphill) ≈ 0.8` (Kirkpatrick's calibration); equivalently `T0 ≈ ⟨ΔE⟩ / ln(1/χ0)`.
- **Epoch length `L`:** proportional to neighborhood size (`Θ(n²)` for 2-opt) so each temperature approximately equilibrates.
- **Cooling `α`:** geometric `α ∈ [0.9, 0.999]`; larger `α` ↔ closer to the slow-cooling ideal.
- **Move/proposal `Q`:** must be symmetric for the clean Boltzmann stationarity; asymmetric proposals need the **Metropolis–Hastings** correction `min(1, [Q(s',s)/Q(s,s')]·exp(−ΔE/T))`.
- **Stopping/reheating:** terminate on frozen acceptance; reheat to escape premature freezing (a bridge toward **adaptive** SA and the restart logic of iterated local search).

## 5. TSP practice and comparison

- **Moves:** 2-opt and Or-opt are the standard SA neighborhoods; `ΔE` is `O(1)`, reversal `O(√n)` with two-level lists — identical machinery to the local-search cluster.
- **Quality:** well-tuned SA reaches a few percent over optimal on geometric TSP — comparable to a good 2-opt/3-opt local optimum, **below** Lin–Kernighan-class methods. On TSP specifically SA is rarely state of the art; **chained LK / LKH dominate**.
- **Why SA still matters:** problem-agnosticism. It needs only `(E, N, Q)` and a schedule, so it ports to **VLSI placement** (its original killer app, Kirkpatrick–Gelatt–Vecchi 1983), **job-shop scheduling**, **continuous** optimization (Gaussian proposals), and countless combinatorial problems where no LK-style specialized move exists.

## 6. Edge cases & relatives

- **Asymmetric proposals** ⇒ use Metropolis–Hastings; otherwise stationarity is wrong.
- **Continuous SA:** proposals are perturbations `s' = s + 𝒩(0, σ_T)`; `σ_T` shrinks with `T`. **Adaptive simulated annealing (ASA)** auto-tunes per-coordinate temperatures.
- **Threshold accepting** (Dueck–Scheuer): deterministic variant accepting `ΔE ≤ θ_k` with `θ_k ↓ 0` — cheaper, no exponentials.
- **Parallel tempering / replica exchange:** run multiple temperatures concurrently and swap — a powerful SA upgrade for rugged landscapes.
- **Quantum / simulated quantum annealing:** tunneling-based escape, a different barrier-crossing mechanism.

## 7. Synthesis

Simulated annealing is **Metropolis sampling of the Boltzmann distribution under a cooling schedule**: at each `T` the chain is pulled toward `π_T ∝ e^{−E/T}`, and lowering `T` concentrates that measure on global minima. Hajek's theorem ties exact convergence to **logarithmic** cooling governed by the landscape's deepest barrier `d*` — sound in principle, impractically slow, so real SA is a tunable heuristic with no guarantee. On TSP it is competitive with simple local search but beaten by Lin–Kernighan; its enduring value is **generality**. As the opening metaheuristic of this cluster, it frames the shared question — *escape local optima* — that **tabu search** (memory), **GRASP** (randomized restart), **VNS** (neighborhood change), and **population methods** each answer with different machinery.

