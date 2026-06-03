# Survival games: expectimax to multi-agent RL — Level 5: Graduate

> **Example problems:** Battle royale (Fortnite / PUBG), poker, partially-observable stochastic games  ·  **Type:** Stochastic + imperfect-info + multi-agent RL (ensemble)  ·  **Guarantee:** Expectimax exact on the modeled tree; CFR converges to Nash in 2p zero-sum; general-sum POSGs have no canonical solution and no convergence guarantee
> **Used for:** Maximizing expected survival/placement under uncertainty among many adaptive agents, rather than greedily taking local rewards
> **Level 5 of 6** — graduate: formal models, paradigm, bounds/convergence, the risk/survival objective, edge cases, optimizations. See sibling files for other levels.

---

## 1. Formal setting: from POMDP to POSG

**POMDP** `(S, A, T, R, Ω, O, γ)`: states `S`, actions `A`, transition `T(s'|s,a)`, reward `R(s,a)`, observations `Ω`, observation model `O(o|s',a)`, discount `γ`. The agent never sees `s`; it maintains a **belief** `b ∈ Δ(S)`, a sufficient statistic updated by Bayes:
`b'(s') ∝ O(o|s',a) Σ_s T(s'|s,a) b(s)`.

The belief MDP has the Bellman optimality equation
`V*(b) = max_a [ Σ_s b(s)R(s,a) + γ Σ_o P(o|b,a) V*(b_a^o) ]`.
**Sondik's theorem:** the finite-horizon optimal value function is **piecewise-linear and convex (PWLC)** in `b`, representable by a finite set of **α-vectors** `Γ`, with `V(b) = max_{α∈Γ} ⟨α, b⟩`. This is what point-based solvers (PBVI, SARSOP) and value nets approximate.

**Partially Observable Stochastic Game (POSG):** lift to `N` agents, each with actions `A_i`, observations, and rewards `R_i`. Battle royale is a POSG that is:
- **General-sum**, not zero-sum. Eliminating agent `j` raises the survival prospects of *all* remaining `k ≠ i`, not only `i` — a **positive externality**. Formally `i` pays the full cost of the engagement but the benefit `Δ(survival)` is shared across the field, a free-rider / war-of-attrition structure (the game-theoretic reason "passing the kill" is often a best response).
- **Many-agent**, so exact equilibrium is intractable; large-`N` limits motivate **mean-field** approximations (Level 6).

**Design paradigm:** the ensemble is *decision-theoretic planning under uncertainty* (expectimax/expectiminimax over chance) + *belief-state inference* (POMDP) + *equilibrium/learning* across agents (CFR, multi-agent RL). Minimax is the degenerate special case: 2 agents, zero-sum, no chance, full observability.

## 2. Expectimax / expectiminimax, formally

With a stochastic "nature" player, the value recursion replaces MIN by expectation:
`V(s) = max_a Σ_o P(o|s,a) V(result(s,a,o))` at agent/chance layers; with an adversary present, interleave MAX/MIN/CHANCE = **expectiminimax**. Pruning is weaker than α-β: chance nodes need all outcomes unless leaf values are bounded, where **`*`-minimax** (Ballard 1983: Star1/Star2) prunes using `[L,U]` bounds. Unlike minimax, the result is *not* robust to a worst-case opponent at chance nodes — it is only as good as the outcome distribution `P(o|·)`, i.e. the model.

## 3. Imperfect-information equilibria: CFR

Against *adaptive* opponents, optimizing expected value vs. a fixed model is **exploitable**. The principled target in two-player zero-sum imperfect-info games is a **Nash equilibrium**, computed by **Counterfactual Regret Minimization** (Zinkevich et al. 2007). For information set `I`, action `a`, define counterfactual regret `R^T(I,a)` and update by **regret matching**:

```
σ^{T+1}(I,a) = R^{T,+}(I,a) / Σ_{a'} R^{T,+}(I,a')     (uniform if all regrets ≤ 0)
R^{T,+} = max(R^T, 0)
```

The **average** strategy `σ̄^T` (not the current one) converges to Nash; regret is bounded `R^T ≤ Δ·|I|·√(|A| T)`, giving an `O(1/√T)` exploitability rate (CFR+ with regret-matching+ and linear averaging is far faster empirically). MCCFR samples to scale; **Deep CFR** function-approximates regrets. This is the backbone of superhuman poker.

## 4. Multi-agent RL and self-play

For large POSGs (StarCraft, Dota, battle royale) we learn `π_θ(a | h)` over observation history `h` via policy-gradient / actor-critic (PPO is standard), trained by **self-play**. Core difficulties and remedies:

- **Nonstationarity:** every agent's update changes everyone else's environment → no stationary target, no general convergence guarantee in general-sum games.
- **Strategy cycles (rock-paper-scissors dynamics):** naive self-play can chase its tail. Remedies: **fictitious self-play / NFSP** (best-respond to the *average* policy; Heinrich & Silver 2016), **league training** (AlphaStar), and **PSRO** (Policy-Space Response Oracles; Lanctot et al. 2017) — an empirical-game meta-solver generalizing the double-oracle method.
- **Solution-concept ambiguity:** general-sum games have no single canonical equilibrium; **(coarse) correlated equilibria** and **NashConv/exploitability** are used as progress metrics rather than exact targets.

## 5. The objective is survival → risk-sensitivity is intrinsic

A subtle but important point that ties back to the chess-era "can we model risk?" question. Here risk is **not** an optional add-on — the objective *is* a tail event:

- **Placement reward is monotone in survival;** being eliminated is an absorbing 0. Maximizing expected placement already penalizes high-variance, "coin-flip" engagements, which is precisely why expectimax demotes the noisy kill.
- For explicit risk aversion, optimize a **risk-sensitive** criterion — e.g. **CVaR_α** (expected outcome in the worst `α`-tail) or an exponential utility `−(1/β)log E[e^{−βX}]`. Survival games are a natural home for CVaR-RL: you care most about *not dying*, i.e. the lower tail.
- **Opportunity cost** is again endogenous: `regret(pass) = EV(fight) − EV(disengage)` over the belief and chance distributions; no separate term needed, but unlike chess the `EV(fight)` carries the **third-party hazard** and the **shared-benefit externality**, which is what flips the sign so often.

```mermaid
flowchart LR
    subgraph Plan under uncertainty (one decision)
        B["belief b over hidden enemies"] --> EX["expectimax / expectiminimax<br/>average over chance (loot, hit, 3rd party)"]
        EX --> OC["opportunity cost = EV(fight) - EV(disengage)"]
        EX --> CV["risk-sensitive objective<br/>(CVaR: don't die)"]
    end
    subgraph Across many games (learning)
        Z["placement outcome"] --> L["multi-agent RL self-play<br/>PPO + league/PSRO/NFSP"]
        EQ["CFR (2p zero-sum subgames)<br/>regret matching -> Nash"] --> L
        L -. "policy pi_theta, value/belief nets" .-> B
    end
```

## 6. Edge cases, invariants, optimizations

- **Modes change the game:** solos vs squads (knock-vs-eliminate, reviving, leaving a knocked enemy as **bait**), ranked **Surge** mechanics that re-couple damage to survival, and **kingmaker/last-mover** effects near the end where a weak player can decide who wins.
- **Belief tractability:** exact Bayes is intractable; use **particle filters** or learned recurrent/transformer belief encoders; beware belief collapse and observation aliasing.
- **Invariant (expectimax):** returned value = true expectation of the modeled tree (linearity at chance, max at decision); all model error lives in `P(o|·)`, `b`, and opponent policies.
- **Stability tricks for self-play RL:** large replay/league pools, opponent sampling to cover the strategy space, reward shaping toward placement (not kills), and **centralized-training / decentralized-execution** (CTDE) for teams.

**Synthesis:** survival games generalize the *Minimax to AlphaZero* idea into the regime where **minimax fails** — replace worst-case MIN with **expectation** (expectimax), reason over **beliefs** under partial observability (POMDP), seek **unexploitable** play in imperfect-info subgames (CFR), and **learn** robust policies among many adaptive agents by **self-play** (multi-agent RL). Passing the nearby kill is the canonical correct move precisely because the objective is *expected survival*, the fight is *noisy and exposing*, and its benefit is *shared with every rival*.
