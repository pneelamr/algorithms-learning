# Minimax to AlphaZero — Level 6: Researcher / frontier

> **Example problems:** Chess, Go, Checkers, two-player zero-sum games (and, at the frontier, imperfect-information and general games)  ·  **Type:** Adversarial search + RL (ensemble)  ·  **Guarantee:** Exact for full-depth alpha-beta; for learned-eval + search, only empirical — no general convergence/exploitability bound
> **Used for:** Long-horizon optimal play over greedy local gains, with self-play-learned evaluation — and the research program around making that sample-efficient, model-based, and theoretically understood
> **Level 6 of 6** — researcher: SOTA variants, theoretical frontiers, open problems, references, links to related problems. See sibling files for other levels.

---

## 1. The lineage in one map

```
Greedy / static eval
   │  add adversarial look-ahead
Minimax  (Shannon 1950; Turing's "Turochamp")
   │  add provably-equivalent pruning
Alpha-beta  (formalized Knuth & Moore 1975)  + quiescence/extensions (Berliner)
   │  branch A: hand-crafted eval, brute depth        branch B: learn the eval
Deep Blue 1997 (αβ + tuned eval, custom HW)       Samuel 1959 / TD-Gammon 1995 / TD-Leaf 1999
   │  fuse learned eval back into αβ                  │  replace αβ with MCTS + deep net, self-play
Stockfish + NNUE 2020 (αβ + learned eval)         AlphaGo 2016 → AlphaGo Zero → AlphaZero 2017/18 → MuZero 2020
```

Two modern attractors: **(B-into-A)** classical alpha-beta with a *learned* efficiently-updatable evaluation (**NNUE**, now in Stockfish — currently the strongest chess entity in TCEC-style conditions), and **(B)** search-light deep RL with **MCTS** (AlphaZero/Leela). They are converging: both are "deep look-ahead + a learned value," differing in search discipline (exhaustive αβ vs. selective PUCT-MCTS).

## 2. State of the art

- **AlphaZero (Silver et al. 2018).** Single network `(p, v)`, PUCT-MCTS, pure self-play from random init; mastered Go, chess, shogi with one algorithm. Demonstrated that *general* self-play RL + search beats domain-specialized αβ engines given hardware.
- **MuZero (Schrittwieser et al. 2020).** Drops the known-rules assumption: learns a **latent dynamics model** `(h, g, f)` and runs MCTS *in latent space*, optimizing only quantities relevant to value/policy/reward — extends the recipe to Atari and unknown-dynamics settings. Successors: **EfficientZero** (sample-efficient, human-level Atari in ~2h), **Sampled MuZero** (large/continuous action spaces), **Stochastic MuZero** (chance nodes).
- **NNUE (Nasu 2018; Stockfish 2020+).** Shallow, incrementally-updatable net evaluated millions of times/sec inside αβ — the dominant *engine-strength* paradigm, distinct from the MCTS branch.
- **Leela Chess Zero (Lc0).** Open-source AlphaZero-style chess; the practical research testbed for net architectures (residual → transformer) and search (e.g. *Gumbel MuZero/AlphaZero* for low-simulation policy improvement, Danihelka et al. 2022).

## 3. Theoretical frontiers

- **Why does self-play converge (when it does)?** AlphaZero is generalized policy iteration with an MCTS improvement operator, but with deep-net function approximation there is **no general convergence or optimality theorem**. Recent work analyzes MCTS as **regularized policy optimization** (Grill et al. 2020) — PUCT ≈ solving a KL-regularized policy update — giving principled low-simulation variants, but global guarantees remain open.
- **Bandits underneath the tree.** UCT (Kocsis & Szepesvári 2006) imported UCB regret analysis into trees; PUCT's finite-time guarantees in the deep-net regime are weaker than the tabular bandit theory it descends from.
- **Search as estimator.** Depth-`d` minimax with noisy leaf evals can exhibit **minimax pathology** (deeper search *worsening* decisions under independent eval noise; Nau, Beal/Pearl) — real games escape it via eval correlation and quiescence, but a crisp characterization of when search helps is incomplete.
- **The deadly triad** (bootstrapping + function approximation + off-policy) and divergence: empirically tamed by self-play on-policy data + search-improved targets, not solved.
- **Optimal alpha-beta ordering** `Θ(b^{d/2})` is the classic bound; **best-first** alternatives (SSS*, MTD(f), B*, proof-number search for endgames) trade memory/structure for fewer expansions.

## 4. Imperfect information & generalization (where minimax breaks)

Plain minimax assumes perfect information; it does **not** extend to games like poker, where you must reason about hidden state and avoid being exploited. The parallel research lineage:

- **Counterfactual Regret Minimization (CFR)** (Zinkevich et al. 2007) and variants (CFR+, MCCFR, **Deep CFR** Brown et al. 2019) converge to **Nash equilibrium** in two-player zero-sum imperfect-info games — the right solution concept there is *unexploitability*, not minimax-to-a-leaf.
- **Libratus/Pluribus** (Brown & Sandholm 2017/2019): superhuman heads-up and 6-max poker via CFR + **depth-limited subgame solving** — a different "look-ahead + learned values" instantiation.
- **ReBeL** (Brown et al. 2020) and **Player of Games** (Schmid et al. 2021) **unify** the AlphaZero search-and-RL idea with CFR over belief/public states — a candidate single algorithm spanning perfect and imperfect information. This is arguably the live frontier of "the Minimax-to-AlphaZero idea, generalized."

## 5. Open problems

1. **Provable guarantees for deep self-play:** convergence, sample complexity, and exploitability bounds for AlphaZero/MuZero-class methods.
2. **Sample efficiency / compute:** matching AlphaZero strength at orders-of-magnitude less self-play (EfficientZero is a step; far from human data efficiency).
3. **Model error in MuZero:** how learned-latent-model bias interacts with deep search; guarantees under wrong models.
4. **Search–learning trade-off:** when is more search worse (pathology), and the optimal allocation between net capacity, simulations, and data (cf. *scaling laws for board games*, Jones 2021).
5. **Generalization & transfer:** zero-shot to new games/rules; world-model agents (DreamerV3, Hafner 2023) as the continuous-control cousin of MuZero.
6. **Interpretability of learned value:** what concepts the value head encodes (McGrath et al. 2022 found human chess concepts emerge inside AlphaZero) — and whether that supports verification.

## 6. Links to related problems

- **Reinforcement learning core:** generalized policy iteration, TD(λ), the deadly triad, policy-gradient/actor-critic (the value/policy heads).
- **Bandits & exploration:** UCB → UCT → PUCT; Gumbel-based planning.
- **Game theory:** Nash equilibrium, regret minimization (CFR), minimax theorem, Zermelo's theorem.
- **Heuristic search & DP:** A*/IDA*, branch-and-bound (alpha-beta *is* adversarial branch-and-bound), proof-number search.
- **Model-based RL & planning:** MuZero ↔ Dreamer/world models; MCTS as differentiable-ish planning.

## References (seminal → modern)

- Zermelo, E. (1913). On an application of set theory to chess.
- von Neumann, J. (1928). *Zur Theorie der Gesellschaftsspiele* (minimax theorem).
- Shannon, C. (1950). *Programming a Computer for Playing Chess.*
- Samuel, A. (1959). *Some Studies in Machine Learning Using the Game of Checkers.*
- Knuth, D. & Moore, R. (1975). *An Analysis of Alpha-Beta Pruning.*
- Pearl, J. (1984). *Heuristics.* (minimax pathology, SSS*, scout)
- Tesauro, G. (1995). *Temporal Difference Learning and TD-Gammon.*
- Kocsis, L. & Szepesvári, C. (2006). *Bandit-based Monte-Carlo Planning* (UCT).
- Baxter, Tridgell & Weeks (1999). *TDLeaf(λ).*
- Zinkevich et al. (2007). *Regret Minimization in Games with Incomplete Information* (CFR).
- Silver et al. (2016). *Mastering the game of Go…* (AlphaGo); (2017) AlphaGo Zero; (2018) *A general RL algorithm…* (AlphaZero).
- Schrittwieser et al. (2020). *Mastering Atari, Go, chess and shogi by planning with a learned model* (MuZero).
- Brown & Sandholm (2017/2019). *Libratus/Pluribus*; Brown et al. (2020). *ReBeL.*
- Grill et al. (2020). *Monte-Carlo Tree Search as Regularized Policy Optimization.*
- Sutton, R. & Barto, A. (2018). *Reinforcement Learning: An Introduction* (2nd ed.).

*Uncertainty flags:* exact "strongest engine" status (Stockfish-NNUE vs. Lc0) shifts with hardware/time-control and TCEC seasons; convergence/exploitability claims for deep self-play are empirical, not proven; dates are first major publication, not necessarily first preprint.
