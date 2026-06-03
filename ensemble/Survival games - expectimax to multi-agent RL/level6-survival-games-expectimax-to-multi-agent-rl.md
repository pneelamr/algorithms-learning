# Survival games: expectimax to multi-agent RL — Level 6: Researcher / frontier

> **Example problems:** Battle royale (Fortnite / PUBG), poker, real-time strategy (StarCraft / Dota), partially-observable stochastic games  ·  **Type:** Stochastic + imperfect-info + multi-agent RL (ensemble)  ·  **Guarantee:** Exact only for small expectimax/POMDP; CFR → Nash in 2p zero-sum; large general-sum POSGs have no convergence/exploitability guarantee
> **Used for:** Expected-survival optimization under partial observability among many adaptive agents — and the research program to make it scalable, equilibrium-grounded, and risk-aware
> **Level 6 of 6** — researcher: SOTA, theoretical frontiers, open problems, references, links to related problems. See sibling files for other levels.

---

## 1. The lineage in one map

```
Greedy (take the nearest reward)
   │  add randomness  ->  expectation over chance
Expectimax / Expectiminimax  (Michie 1966; Ballard 1983 *-minimax; backgammon)
   │  add hidden state  ->  beliefs
POMDP planning  (Astrom 1965; Smallwood & Sondik 1973; PBVI/SARSOP)
   │  add many adaptive agents
Stochastic / Markov games (Shapley 1953; Littman 1994)
   ├── imperfect-info, zero-sum:  CFR (2007) -> DeepStack / Libratus / Pluribus / ReBeL
   └── large general-sum, learned:  self-play deep MARL
                                    OpenAI Five (Dota), AlphaStar (SC2),
                                    PSRO / league training; mean-field for N -> infinity
```

Two anchors. **(a) Equilibrium branch** — imperfect-information *solving* via regret minimization, which is provably sound in two-player zero-sum. **(b) Learning branch** — deep multi-agent RL with self-play for huge, general-sum, real-time games where exact solving is hopeless. Battle royale (≈100 agents, general-sum, partial obs, real-time) sits at the hard end of (b), and is where **mean-field** ideas become relevant.

## 2. State of the art

**Imperfect-information solving (the rigorous branch):**
- **DeepStack** (Moravčík et al. 2017) — *continual re-solving* with deep counterfactual value networks; first to beat pros at heads-up no-limit hold'em.
- **Libratus** (Brown & Sandholm 2017) and **Pluribus** (2019, 6-max, superhuman) — abstraction + CFR + **depth-limited subgame solving**; Pluribus showed multiplayer poker is tractable with self-play + limited search despite no equilibrium guarantee for >2 players.
- **ReBeL** (Brown et al. 2020) and **Player of Games** (Schmid et al. 2021) — *unify* the AlphaZero search+RL recipe with CFR over **public belief states**, a single algorithm spanning perfect- and imperfect-information games. This is the closest thing to a bridge from *Minimax to AlphaZero* into this ensemble's territory.

**Deep multi-agent RL (the scalable branch):**
- **OpenAI Five** (Berner et al. 2019) — PPO at massive scale beat Dota 2 world champions; long horizons, partial obs, team coordination via shared reward shaping.
- **AlphaStar** (Vinyals et al. 2019) — StarCraft II Grandmaster; **league training** (main agents, exploiters, league exploiters) to escape strategic cycles, a Nash-flavored population method.
- **Emergent complexity** — hide-and-seek tool use (Baker et al. 2019), Capture-the-Flag (Jaderberg et al. 2019): autocurricula from multi-agent self-play.
- **Population methods** — **PSRO** (Lanctot et al. 2017) and `α`-Rank (Omidshafiei et al. 2019) as empirical-game/meta-solvers; **NFSP** (Heinrich & Silver 2016) for approximate Nash via fictitious play.

**Battle royale specifically:** little peer-reviewed SOTA; commercial bots are largely behavior-tree/scripted with selective RL. Academically it is framed as a large-`N` POSG, pushing toward **mean-field games**.

## 3. Theoretical frontiers

- **Many-agent scaling → mean-field games (MFG)** (Lasry & Lions 2007; Huang–Malhamé–Caines). As `N→∞`, model each agent as best-responding to the *population distribution*; **Mean-Field MARL** (Yang et al. 2018), MFG equilibria via fictitious play (Perrin et al. 2020). The natural formalism for ~100-player royales, but existence/uniqueness and learning-convergence under partial observation are open.
- **General-sum solution concepts:** no canonical equilibrium; **(coarse) correlated equilibria**, **NashConv/exploitability**, and `α`-Rank give partial footing. Equilibrium *selection* and computational hardness (PPAD for Nash) remain fundamental.
- **Self-play convergence:** general-sum self-play has no convergence guarantee; cycling, non-transitivity, and the need for population diversity (PSRO/league) are active theory. **Last-iterate** vs **average-iterate** convergence (optimistic/extragradient methods) is a live area.
- **Partial observability at scale:** public-belief-state methods (ReBeL/PoG) are elegant but blow up with many private states; scalable belief representation (learned, recurrent, transformer) lacks guarantees.
- **Risk-sensitive / survival objectives:** CVaR-RL and distributional RL (C51, QR-DQN, IQN) fit "don't die," but risk-sensitive *multi-agent* equilibria are poorly understood.
- **Expectimax pathology & search:** like minimax, deeper expectimax with noisy leaf models can mislead; `*`-minimax pruning and sparse-sampling planners (Kearns–Mansour–Ng sparse sampling; **POMCP**, Silver & Veness 2010, MCTS in belief space) are the scalable approximations.

## 4. The structural contrasts that make survival games hard (vs. chess)

1. **General-sum externality / free-riding:** eliminating an opponent is a public good; the war-of-attrition equilibrium often favors *waiting* — the formal reason "passing the kill" is rational. Connects to last-mover/kingmaker analysis.
2. **Information as a resource:** acting reveals state (third-party risk); optimal play values *staying unobserved*, linking to information-theoretic and deception-aware planning.
3. **Ad hoc teamwork & shifting alliances** (squads, temporary truces): zero-shot coordination (Hu et al. 2020, "other-play") and ad hoc teamwork are unsolved in general.

## 5. Open problems

1. Scalable, convergent learning for **large general-sum POSGs** (the literal battle-royale setting).
2. **Mean-field** methods under partial observability and heterogeneous agents.
3. **Sample efficiency** — OpenAI Five/AlphaStar used enormous compute; human-level data efficiency is far off (cf. EfficientZero in the perfect-info sibling).
4. **Equilibrium selection & evaluation** without a canonical solution concept; trustworthy exploitability estimates at scale.
5. **Risk-sensitive multi-agent equilibria** for survival objectives (CVaR + games).
6. **Modeling humans:** bounded-rational, deceptive, alliance-forming opponents (behavioral game theory, opponent modeling vs. unexploitable play trade-off).

## 6. Links to related problems

- **Perfect-information sibling:** [[minimax-to-alphazero]] — same "decline the greedy move for the long-horizon win," but minimax/α-β/MCTS instead of expectation+belief+equilibrium.
- **Game theory:** Nash / correlated / coarse-correlated equilibria, regret minimization (CFR), Shapley stochastic games, mean-field games, war of attrition.
- **Decision-theoretic planning:** POMDPs, belief MDPs, POMCP, sparse sampling, distributional & risk-sensitive RL (CVaR).
- **MARL machinery:** PPO/actor-critic, self-play, NFSP, PSRO/`α`-Rank, league training, CTDE, autocurricula.

## References (seminal → modern)

- Shapley, L. (1953). *Stochastic Games.*
- Åström, K. (1965); Smallwood, R. & Sondik, E. (1973). *Optimal control of POMDPs over a finite horizon* (PWLC / α-vectors).
- Ballard, B. (1983). *The *-minimax search procedure for trees containing chance nodes.*
- Littman, M. (1994). *Markov games as a framework for multi-agent RL.*
- Kearns, Mansour & Ng (2002). *Sparse sampling for large MDPs/POMDPs.*
- Zinkevich, Johanson, Bowling & Piccione (2007). *Regret Minimization in Games with Incomplete Information* (CFR).
- Silver, D. & Veness, J. (2010). *Monte-Carlo Planning in Large POMDPs* (POMCP).
- Heinrich, J. & Silver, D. (2016). *Deep Reinforcement Learning from Self-Play in Imperfect-Information Games* (NFSP).
- Moravčík et al. (2017). *DeepStack*; Brown & Sandholm (2017/2019). *Libratus / Pluribus.*
- Lanctot et al. (2017). *A Unified Game-Theoretic Approach to MARL* (PSRO); Omidshafiei et al. (2019). *α-Rank.*
- Lasry, J.-M. & Lions, P.-L. (2007). *Mean field games*; Yang et al. (2018). *Mean Field MARL.*
- Vinyals et al. (2019). *AlphaStar*; Berner et al. (2019). *OpenAI Five*; Baker et al. (2019). *Emergent Tool Use.*
- Brown et al. (2020). *ReBeL*; Schmid et al. (2021). *Player of Games.*
- Bellemare, Dabney & Munos (2017). *A Distributional Perspective on RL* (distributional/risk-sensitive).

*Uncertainty flags:* battle-royale-specific results are largely industrial/unpublished — the academic treatment is by analogy to POSG/MFG; convergence and exploitability claims for large general-sum self-play are empirical, not proven; equilibrium guarantees (CFR) hold only in the two-player zero-sum case.
