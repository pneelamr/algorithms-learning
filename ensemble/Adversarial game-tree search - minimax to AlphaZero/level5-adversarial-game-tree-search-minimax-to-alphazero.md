# Adversarial game-tree search - minimax to AlphaZero — Level 5: Graduate

> **Example problems:** Chess, Go, Checkers, two-player zero-sum games  ·  **Type:** Adversarial search + RL (ensemble)  ·  **Guarantee:** Alpha-beta returns the exact minimax value; with depth limit + function-approximated eval, no convergence/optimality guarantee in general
> **Used for:** Choosing long-horizon optimal play over greedy local gains in adversarial games, and learning evaluations across games
> **Level 5 of 6** — graduate: formal definitions, paradigm, proof sketches/bounds, the risk/opportunity-cost analysis, RL update rules, edge cases, optimizations. See sibling files for other levels.

---

## 1. Formal setting

A finite **two-player zero-sum extensive-form game of perfect information**. States `S`, a turn function `τ: S → {MAX, MIN}`, action sets `A(s)`, deterministic transition `result`, terminal payoff `u: S_term → ℝ` to MAX (MIN gets `−u`). The **minimax value** satisfies the optimality (Bellman-style) recursion:

```
V(s) = u(s)                         s terminal
V(s) = max_{a∈A(s)} V(result(s,a))  τ(s) = MAX
V(s) = min_{a∈A(s)} V(result(s,a))  τ(s) = MIN
```

**Negamax identity.** With payoffs always expressed from the side-to-move's perspective, both cases collapse to
`V(s) = max_{a} −V(result(s,a))`, which is why engines implement a single `negamax` routine. This is the design paradigm: **adversarial dynamic programming on the game tree** with backed-up values.

**von Neumann minimax theorem (1928).** For a finite zero-sum game, `max_x min_y x^⊤ M y = min_y max_x x^⊤ M y`: the game has a well-defined value, and (allowing mixed strategies) optimal strategies exist. Perfect-information games like chess have an optimal value attainable in **pure** strategies — so deterministic minimax play is optimal in principle. (Zermelo 1913: chess is strictly determined — a forced win for one side or a draw with best play exists; we just can't compute which.)

## 2. Alpha-beta: optimality and the node bound

Alpha-beta carries a window `[α, β]` = `(`best MAX can already force, best MIN can already force`)`.

**Correctness (soundness).** Claim: `alphabeta(s, α, β)` returns `V(s)` whenever `α < V(s) < β`; otherwise it returns a *bound* on the correct side (a "fail-soft" value) that is still sufficient for the parent's decision. Proof sketch by induction on subtree height: a child is pruned only after the running value `v` has reached a point where `α ≥ β`; at a MAX node this means MIN (the parent) already has a reply guaranteeing `≤ α`, so no value discovered in the remaining children can raise the parent's chosen minimum — the pruned subtree is decision-irrelevant. Hence the *root choice* is identical to full minimax. ∎(sketch)

**Node-count bound (Knuth & Moore 1975).** With an **optimal move ordering**, the number of leaves examined to depth `d` is `Θ(b^{⌈d/2⌉} + b^{⌊d/2⌋}) = Θ(b^{d/2})`. Interpretation: only one child must be fully searched at nodes off the principal variation; the rest need a single refutation. This square-roots the effective branching factor (`√b ≈ 5.9` for chess), so the *same* budget buys ~twice the depth. Worst-case (adversarial ordering) remains `Θ(b^d)`. Real engines approach the optimal regime with **iterative deepening + transposition tables + killer/history heuristics** for ordering.

## 3. The horizon effect, quiescence, and search–eval decomposition

Depth-limited search computes `V_d(s)`, the value of the depth-`d` tree with leaves scored by `eval`. The error decomposes as

```
V(s) − V_d(s)  =  (search error from cutting at depth d)  +  (eval error at the leaves).
```

- **Horizon effect (Berliner).** A decisive event at depth `d+1` is invisible; worse, a losing side can *push* the bad event past the horizon with delaying checks, producing systematically optimistic `V_d`. Pure depth-`d` search is not a consistent estimator of `V` over tactical states.
- **Quiescence search** restores consistency on the tactical subclass: extend only `tactical_moves` (captures, promotions, checks) with a **stand-pat** lower bound (`eval(s)` as the "decline to continue capturing" option) until a quiet state, then apply `eval`. This evaluates only *quiet* leaves, where a static `eval` is a far better proxy for `V`. Selective extensions (singular extensions, check extensions) and forward pruning (null-move `R`-reductions, late-move reductions, futility/razoring) further reshape the effective tree at the cost of soundness.

## 4. Risk and opportunity cost — answering "can we model the penalty?"

A recurring question: where is the *risk of declining the greedy capture* and the *opportunity cost* of the piece you passed up?

- **Opportunity cost is endogenous to minimax.** Let `a_grab` be the capture and `a_plan` the chosen quiet move. Then `regret(decline) = V_d(result(s, a_grab)) − V_d(result(s, a_plan))`. If the search depth is adequate, minimax *already* prefers `a_plan` iff this regret is ≤ 0. There is no separate penalty term to add — opportunity cost **is** the difference of backed-up subtree values. A too-shallow search can misjudge it, but the remedy is depth/quiescence, not a new term.
- **Risk attitude is exogenous and tunable, not learned.** Standard search optimizes *expected/extremal* value and is risk-neutral. You can inject a fixed attitude:
  - **Contempt factor** — bias the draw score to play sharper/safer vs. a given opponent.
  - **Minimax-regret objective** — choose `argmin_a max_{ω} [V*(s,ω) − V(s,a,ω)]` over uncertainty set `ω` (model/opponent error). This *directly* encodes "minimize worst-case lost opportunity," but it is a **search objective**, fixed in advance, with **no cross-game learning**.
- **Learning the risk across games is the RL part (Section 5).** The "do better *next time*" requirement is exactly temporal-difference / self-play value learning: the eventual loss is propagated back to lower the learned value of the position where you declined — so the *penalty becomes a weight update*, persisted into `eval`.

```mermaid
flowchart LR
    subgraph Within one game (no learning)
        S1["state s"] --> M["minimax/alpha-beta to depth d"]
        M --> OC["opportunity cost = V_d(grab) - V_d(plan)"]
        M --> QS["quiescence fixes horizon/tactics"]
        M --> RA["fixed risk attitude:<br/>contempt / minimax-regret"]
    end
    subgraph Across games (the learning)
        Z["terminal result z (win/draw/loss)"] --> TD["TD / Monte Carlo backup"]
        TD --> W["update theta: eval/policy"]
        W -. "better eval next game" .-> S1
    end
```

## 5. The learning layer: TD-Leaf, then AlphaZero self-play

### 5a. Temporal-difference learning of a parametric eval
Let `V_θ(s)` be a parametric evaluation. Define the TD error `δ_t = V_θ(s_{t+1}) − V_θ(s_t)` (terminal target = `z`). The **semi-gradient TD(λ)** update is

```
θ ← θ + α · δ_t · e_t,     e_t = γλ·e_{t-1} + ∇_θ V_θ(s_t)   (eligibility trace)
```

It is *bootstrapping* (target depends on current estimate) and *semi-gradient* (we don't differentiate the target). **TD-Leaf(λ)** (Baxter, Tridgell & Weeks 1999) adapts this to search: it applies the TD update to the **principal-variation leaf** value returned by alpha-beta rather than the root static eval — i.e. it learns the eval *that the search actually consults*. TD-Gammon (Tesauro 1995) was the landmark precursor in backgammon.

### 5b. AlphaZero: search as a policy-improvement operator
A single network `f_θ(s) = (p, v)` outputs a policy prior `p` and value `v ∈ [−1,1]`. Moves are chosen by **MCTS** using the **PUCT** selection rule:

```
a* = argmax_a [ Q(s,a) + c_puct · P(s,a) · √(Σ_b N(s,b)) / (1 + N(s,a)) ]
```

`Q` = mean backed-up value of action `a`, `N` = visit count, `P(s,a)=p_a`. MCTS run from `s` yields an improved policy `π` (normalized visit counts) — provably a **policy-improvement operator** over the raw network policy. Self-play generates trajectories with terminal outcome `z`; the network is trained by

```
L(θ) = (z − v)^2  −  π^⊤ log p  +  c‖θ‖^2
        \____value____/  \__policy CE__/   \_reg_/
```

This is **generalized policy iteration**: search improves the policy (`π`), the network distills `(π, z)` (improvement → new `p, v`), repeat. No opening book, no human games, no hand-crafted eval. The value head is precisely the *learned risk/gut-feeling* that earlier levels gestured at — calibrated by millions of self-play outcomes, and MCTS's averaging over rollouts captures "the opponent has tricks" as **lower mean value of fragile lines**, where minimax would have assumed the single best line.

## 6. Edge cases, invariants, optimizations

- **Transpositions / graph not tree.** Zobrist-hashed **transposition tables** memoize `V_d(s)` and supply move-ordering hints; beware *graph-history interaction* (repetition/50-move rules make value path-dependent).
- **Invariant (negamax/αβ):** the returned value is correct within `[α, β]` or a valid bound outside it; the root principal variation is the played line.
- **The deadly triad.** TD with *bootstrapping + function approximation + off-policy* updates can diverge (Sutton & Barto). AlphaZero stays empirically stable via on-policy-ish self-play, large replay buffers, and search-improved targets, but **no general convergence guarantee** exists with deep nets — the headline caveat of the whole learned-eval enterprise.
- **Optimizations:** iterative deepening, aspiration windows, principal variation search (PVS/NegaScout), null-move pruning, late-move reductions, and (NNUE-style) incrementally-updatable evaluations.

**Synthesis:** the ensemble is *adversarial DP* (minimax) made tractable by *provably-equivalent pruning* (alpha-beta) and *consistency-restoring extensions* (quiescence), scored by an `eval` that progresses from hand-crafted, to TD-learned, to **self-play-learned with search-improved targets** (AlphaZero). Opportunity cost is intrinsic to the backed-up values; risk attitude is a fixed objective choice; learning the risk *for next time* is the reinforcement-learning layer.
