# Simulated annealing — Level 6: Researcher / frontier

> **Example problems:** Traveling Salesman Problem, VLSI circuit placement, Job-shop scheduling, Continuous function optimization  ·  **Type:** Metaheuristic  ·  **Guarantee:** No guarantee; stochastic
> **Used for:** Escaping local minima by accepting worse moves with cooling probability
> **Level 6 of 6** — researcher: convergence theory, parallel tempering, quantum annealing, open problems, references. See sibling files for other levels.

---

## 1. Position: the canonical metaheuristic

Simulated annealing is the **archetype** of the metaheuristic family and the cleanest bridge from optimization to statistical physics. Its lasting influence is conceptual: it recast "escape local optima" as **sampling a Boltzmann distribution and cooling it**, a framing that underlies MCMC, parallel tempering, stochastic-gradient-Langevin methods, and quantum annealing. On TSP it is not state of the art (Lin–Kernighan-class methods win), but as a **general-purpose** solver — needing only a cost, a neighborhood, and a schedule — it remains a default baseline across combinatorial and continuous optimization. In this registry it opens the metaheuristics cluster, defining the question its siblings re-answer.

## 2. Convergence theory frontier

- **Asymptotic optimality** (Geman–Geman 1984; Hajek 1988; Mitra–Romeo–Sangiovanni-Vincentelli 1986): logarithmic cooling `T_k = c/log k` with `c ≥ d*` (deepest barrier) gives convergence in probability to the global optimum. Necessary-and-sufficient schedule conditions are characterized via `Σ_k e^{−d*/T_k} = ∞`.
- **Finite-time analysis** is the hard frontier: barrier-based bounds (the "spectral gap / conductance" of `P_T`) tie mixing time to landscape geometry, but tight finite-time approximation guarantees for fast (geometric) schedules are largely **open** and problem-specific.
- **Continuous SA / Langevin connection:** SA is the discrete cousin of simulated tempering and of Langevin dynamics `dX = −∇E dt + √(2T) dW`; the non-asymptotic sampling-theory advances (log-Sobolev, mixing under cooling) are an active bridge between optimization and ML sampling theory.

## 3. Variants and the modern frontier

- **Parallel tempering / replica exchange:** maintain replicas at a ladder of temperatures and stochastically swap; dramatically improves barrier crossing on rugged landscapes — the most important practical SA upgrade.
- **Adaptive SA (ASA), adaptive schedules:** auto-tune `T0`, `α`, and per-coordinate temperatures from acceptance statistics.
- **Threshold accepting / great deluge / record-to-record** (Dueck et al.): deterministic acceptance variants, often faster, no `exp`.
- **Simulated quantum annealing & quantum annealing hardware** (D-Wave): replace thermal hopping with **tunneling**; theoretical and empirical comparisons to classical SA are an ongoing research thread (where, if ever, tunneling provably helps).
- **Population / hybrid SA:** SA inside memetic and population frameworks; SA as the acceptance rule inside iterated local search (chained LK's Metropolis acceptance is exactly this idea at the level of *local optima*).
- **Learned proposals / schedules:** RL- or amortized-inference-tuned proposal kernels and cooling schedules (2020s).

## 4. Why SA persists despite losing on TSP

- **Domain-agnostic:** decouples search from problem structure — only `(E, N, Q, schedule)` required.
- **Original killer app — VLSI placement** (Kirkpatrick, Gelatt & Vecchi 1983; Černý 1985 independently for TSP): SA became standard in physical chip design, where no LK-style move exists.
- **Anytime & tunable:** any time budget, trivially parallelizable (multistart, replicas), easy to implement correctly.
- **Theoretical clarity:** the Boltzmann/Markov-chain framing gives rare *provable* (if asymptotic) optimality among heuristics.

## 5. Open problems / threads

1. **Finite-time guarantees** for practical (geometric/adaptive) schedules tied to landscape geometry.
2. **Optimal schedule theory** beyond worst-case logarithmic — instance-adaptive cooling with provable speed/quality trade-offs.
3. **Classical vs. quantum annealing:** rigorous separations — when does tunneling beat thermal hopping?
4. **Learned SA:** guarantees for RL-tuned proposals/schedules and cross-instance generalization.
5. **Parallel-tempering theory:** optimal temperature ladders and swap rates with mixing-time bounds.

## 6. References (seminal → modern)

- Metropolis, Rosenbluth, Rosenbluth, Teller & Teller (1953). *Equation of state calculations by fast computing machines.* (The acceptance rule.)
- Kirkpatrick, Gelatt & Vecchi (1983). *Optimization by simulated annealing.* (SA as an optimization method; VLSI.)
- Černý, V. (1985). *Thermodynamical approach to the traveling salesman problem.* (Independent SA-for-TSP.)
- Geman & Geman (1984). *Stochastic relaxation, Gibbs distributions, and the Bayesian restoration of images.* (Logarithmic-cooling convergence.)
- Hajek, B. (1988). *Cooling schedules for optimal annealing.* (Necessary-and-sufficient schedule conditions; barrier `d*`.)
- Aarts & Korst (1989). *Simulated Annealing and Boltzmann Machines.* (Comprehensive treatment.)
- Dueck & Scheuer (1990). *Threshold accepting.* (Deterministic acceptance variant.)

## 7. Links to related problems

- **Inner move:** 2-opt / Or-opt (the neighborhood SA samples on TSP); shares reversal data structures with the local-search cluster.
- **Sibling metaheuristics (this registry):** tabu search (deterministic, memory-based escape), GRASP (randomized-greedy multi-start), variable neighborhood search (systematic neighborhood change), genetic / evolutionary and ant-colony (population-based), iterated local search (perturb + local search — chained LK is its TSP instance, with a Metropolis-style acceptance that *is* SA at the level of local optima).
- **Dominant on TSP instead:** Lin–Kernighan, chained Lin–Kernighan / LKH.
- **Cross-domain:** MCMC / Metropolis–Hastings, parallel tempering, Langevin dynamics, quantum annealing.

*Uncertainty flags:* the asymptotic convergence theorems are rigorous but require impractical logarithmic cooling; practical geometric-schedule SA has **no guarantee**. "SA loses to LK on TSP" and the "few percent over optimal" figures are empirical and tuning-dependent. Quantum-vs-classical annealing separations remain contested. The barrier constant `d*` is generally hard to compute.

