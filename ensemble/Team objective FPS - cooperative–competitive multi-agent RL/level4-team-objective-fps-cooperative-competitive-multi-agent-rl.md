# Team objective FPS - cooperative–competitive multi-agent RL — Level 4: Undergraduate CS

> **Example problems:** Team objective FPS (Call of Duty multiplayer, Counter-Strike, Overwatch), capture-the-flag, Dota 2 / MOBAs  ·  **Type:** Cooperative–competitive multi-agent RL (ensemble)  ·  **Guarantee:** None for the full game (Dec-POMDP planning is NEXP-complete); policies are learned approximations via self-play
> **Used for:** Coordinating a team to win an objective/score race over an enemy team, valuing objective control over greedy individual kills
> **Level 4 of 6** — undergrad CS: precise statement, pseudocode, Big-O, correctness intuition, control-flow diagram. See sibling files for other levels.

---

## Why a new model (vs. the other two ensembles)

| Axis | Minimax→AlphaZero (chess) | Survival games (battle royale) | **Team objective FPS** |
|---|---|---|---|
| Sides | 2 individuals | N free-for-all | **2 teams** (coalitions) |
| Sum | zero-sum | general-sum | **team zero-sum** (within-team cooperative) |
| Death | n/a | terminal (survival) | **respawn** (a setback, not terminal) |
| Objective | checkmate | survival / placement | **objective / score control** |
| Info | perfect | partial | partial |

The combination "cooperate **inside** a team, compete **between** teams, under partial observability, with respawns" is modeled as a **team Markov game** built from two pieces:

- **Within a team:** a **Dec-POMDP** (decentralized POMDP) — `n` agents share one reward, each sees only its own observation `o_i`, and must act jointly without communication at execution time.
- **Between teams:** a **Markov game** (Littman 1994) — the two teams' joint rewards are opposite (team zero-sum).

Because death is a respawn, the objective is **cumulative discounted team return** `Σ γ^t R_team(t)` (zone ticks, caps, score) — *not* a survival/terminal signal. This is why risk-averse "don't die" reasoning from the Survival ensemble drops out: dying to cap the point is just a small negative blip against a positive objective gain.

## The core idea: CTDE (centralized training, decentralized execution)

You can't solve a Dec-POMDP exactly (it's NEXP-complete), so you **learn** decentralized policies `π_i(a_i | o_i)` with the help of a **centralized critic** that, *only during training*, sees the global state and everyone's actions:

```
Decentralized actors (used in-game):   a_i ~ π_i(a_i | o_i ; θ_i)          # each sees only its own view
Centralized critic (training only):    Q(s, a_1, ..., a_n ; φ)            # sees everything
```

Two dominant families:

**1. Value decomposition (for cooperative teams) — e.g. QMIX.** Learn per-agent utilities `Q_i(o_i, a_i)` and combine them with a mixing network `f_mix` that is **monotonic** in each `Q_i`:

```
Q_tot(s, a) = f_mix( Q_1(o_1,a_1), ..., Q_n(o_n,a_n) ; s ),   with  ∂Q_tot/∂Q_i ≥ 0
```

Monotonicity guarantees `argmax_a Q_tot = (argmax_{a_1} Q_1, ..., argmax_{a_n} Q_n)` — so each agent can greedily pick its own action and still maximize the team value (the **IGM**, "individual-global-max", property). Train by the usual TD loss on `Q_tot`.

**2. Counterfactual policy gradient — COMA.** Use the centralized critic to compute a **counterfactual advantage** that isolates agent `i`'s contribution by marginalizing out its own action:

```
A_i = Q(s, a) − Σ_{a_i'} π_i(a_i' | o_i) · Q(s, (a_{-i}, a_i'))
```

`A_i > 0` means "agent `i`'s actual action beat its average alternative, holding teammates fixed" — directly solving **multi-agent credit assignment** (who actually helped). MADDPG (continuous actions) and MAPPO (PPO + centralized critic) are the other workhorses.

The **competitive** (between-team) part is handled by **self-play / league training**: teams are trained against current and past opponent teams so the learned policy isn't brittle to a single counter-strategy.

## Complexity & guarantees

- **Exact Dec-POMDP planning:** **NEXP-complete** (Bernstein et al. 2002) — doubly exponential; intractable beyond tiny instances. Hence learning, not solving.
- **Learned methods:** no optimality/convergence guarantee in general-sum / team-zero-sum settings; self-play can cycle (rock-paper-scissors strategies) — mitigated by leagues/populations.
- **CTDE correctness intuition:** the centralized critic sidesteps **nonstationarity** (from each agent's viewpoint the others are part of a changing environment) by conditioning on the *joint* action during training; QMIX's monotonic mixing makes decentralized greedy action selection consistent with the centralized argmax. Execution stays decentralized, so it's deployable per-agent on local views.

```mermaid
flowchart TD
    subgraph Training (centralized)
        S["global state s + joint actions a"] --> CR["centralized critic<br/>Q(s, a_1..a_n) / mixing net Q_tot"]
        CR --> CA["credit assignment<br/>(counterfactual advantage / value decomposition)"]
        CA --> UP["update each actor theta_i"]
    end
    subgraph Execution (decentralized)
        O1["agent 1 sees o_1"] --> P1["pi_1(a_1|o_1)"]
        O2["agent 2 sees o_2"] --> P2["pi_2(a_2|o_2)"]
        On["agent n sees o_n"] --> Pn["pi_n(a_n|o_n)"]
    end
    UP -. "trained policies deployed" .-> P1
    UP -. "trained policies deployed" .-> P2
    UP -. "trained policies deployed" .-> Pn
    P1 & P2 & Pn --> ENV["team acts; objective reward R_team"]
    ENV -. "self-play vs other/past teams (league)" .-> S
    ENV -. "R_team (objective, not kills)" .-> CR
```

**One-line takeaway:** model it as cooperate-within / compete-between (Dec-POMDP inside a Markov game), reward the **objective** (respawns make survival cheap), and learn decentralized policies with a **centralized critic** that handles **credit assignment** — which is exactly why a well-trained team passes the greedy kill and plays the point.
