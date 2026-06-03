# Team objective FPS - cooperative–competitive multi-agent RL — Level 6: Researcher / frontier

> **Example problems:** Team objective FPS (Call of Duty multiplayer, Counter-Strike, Overwatch), capture-the-flag, Dota 2 / MOBAs, StarCraft II micro  ·  **Type:** Cooperative–competitive multi-agent RL (ensemble)  ·  **Guarantee:** Dec-POMDP optimal is NEXP-complete; team-zero-sum self-play has no general convergence/exploitability guarantee
> **Used for:** Objective-controlled team play under partial observability with respawns — and the research program to make multi-agent credit assignment, coordination, and human-compatibility work at scale
> **Level 6 of 6** — researcher: SOTA, theoretical frontiers, open problems, references, links. See sibling files for other levels.

---

## 1. The lineage in one map

```
Greedy individual kills (the anti-pattern)
   │  reward the team objective, not kills
Cooperative MARL (Dec-POMDP)  ── credit assignment ──┐
   │  add centralized training / decentralized exec   │
CTDE: VDN -> QMIX -> QTRAN/Weighted QMIX (value decomposition)
       MADDPG -> COMA -> MAPPO (centralized-critic policy gradient)
   │  add between-team competition
Self-play + populations: PSRO, league training, PBT
   │  scaled to real team games
DeepMind Capture-the-Flag (Quake III, 2019) · OpenAI Five (Dota 2, 2019) · AlphaStar (SC2, 2019)
```

Two coupled axes define the field: **cooperative** (within-team credit assignment + coordination under partial observation) and **competitive** (between-team equilibrium via self-play populations). Team objective FPS (CoD MP, CS, Overwatch) is the applied target; the published SOTA lives mostly in adjacent team games (CTF, Dota, SC2) and standardized benchmarks.

## 2. State of the art

- **DeepMind Capture-the-Flag** (Jaderberg et al., *Science* 2019): human-level Quake III Arena CTF from pixels, **population-based training** of a diverse league, a **two-timescale** optimization (fast RL + slow evolution of internal reward/hyperparameters), agents learning their *own* dense internal rewards aligned to the sparse win signal. A landmark for emergent teamwork and role specialization.
- **OpenAI Five** (Berner et al. 2019): Dota 2 (5v5) superhuman via large-scale PPO; **"team spirit"** hyperparameter annealed from 0→1 interpolating selfish→shared reward — the cleanest operationalization of the cooperative/competitive credit tension; surgery/long-horizon training at scale.
- **AlphaStar** (Vinyals et al. 2019): SC2 Grandmaster; **league training** (main, main-exploiters, league-exploiters) to approximate a Nash-like population and resist cycling.
- **Value-decomposition lineage:** VDN (Sunehag 2018) → **QMIX** (Rashid 2018) → QTRAN (Son 2019) → Weighted QMIX (Rashid 2020); **MAPPO** (Yu 2021) as a surprisingly strong simple baseline.
- **Benchmarks:** **SMAC** (StarCraft Multi-Agent Challenge, Samvelyan 2019), Google Research **Football** (Kurach 2020), **Hanabi** (Bard 2020, cooperative + theory-of-mind), **Overcooked** (Carroll 2019, human–AI coordination).

## 3. Theoretical frontiers

- **Credit assignment at scale:** counterfactual (COMA) and **Shapley-value** attributions (axiomatic fairness; exponential cost, sampled approximations) — principled credit for large teams with sparse objective rewards remains open.
- **Beyond monotonic IGM:** QMIX cannot represent non-monotonic value interactions; characterizing the exact representational/optimization trade-off (QTRAN, Weighted QMIX, QPLEX) is active.
- **Equilibrium in team-zero-sum / general-sum:** team-maximin and **correlated** equilibria, computational hardness (PPAD), and **equilibrium selection**; last-iterate convergence of self-play (optimistic/extragradient) for team settings.
- **Ad hoc teamwork & zero-shot coordination:** playing with *unseen* partners/humans — **other-play** (Hu et al. 2020), **off-belief learning** (Hanabi), human-compatible coordination (Overcooked). The selfish↔team reward dial (team spirit) is a blunt instrument; principled coordination is unsolved.
- **Reward specification / Goodhart:** "slaying off the objective" is proxy-reward gaming; potential-based shaping, preference/objective learning, and reward-model robustness are the defenses.
- **Scalability:** many agents → **mean-field MARL**, graph/attention factorizations; transfer across maps/modes/roles.

## 4. The structural fingerprint (why it's its own ensemble)

1. **Cooperate-within / compete-between** simultaneously — neither pure cooperation (Dec-POMDP) nor pure competition (zero-sum game), but their composition.
2. **Respawns ⇒ objective, not survival.** Death is a bounded transient; the optimal policy is risk-neutral toward dying and spends lives for objective expectation — the **opposite** stance to the Survival ensemble's CVaR "don't die."
3. **Two coalitions ⇒ no free-rider externality.** Unlike the ~100-player battle royale, helping your side is unambiguously good; the hard problem moves from *externalities* to *intra-team credit assignment and coordination*.

## 5. Open problems

1. Scalable, low-variance **credit assignment** for large teams under sparse objective rewards.
2. **Zero-shot / ad hoc coordination** with unseen agents and **humans** (human-AI teaming in live CoD/CS lobbies).
3. **Equilibrium selection & evaluation** in team-zero-sum/general-sum without a canonical solution concept; trustworthy exploitability at scale.
4. **Sample efficiency** — CTF/Dota/SC2 used enormous compute; human-data-efficient team RL is far off.
5. **Robustness to reward misspecification** (anti-Goodhart) and emergent collusion/communication.
6. **Cross-mode/transfer** — one agent across TDM, Domination, Hardpoint, S&D, and maps.

## 6. Links to related problems

- **Family siblings:** [[minimax-to-alphazero]] (2-player zero-sum, perfect info — the search/value-learning cousin) and [[survival-games-expectimax-to-multi-agent-rl]] (N-player free-for-all survival — Search & Destroy is the **hybrid bridge** between that ensemble and this one).
- **Cooperative MARL:** Dec-POMDP, CTDE, value decomposition (VDN/QMIX/QTRAN), COMA, MAPPO, MADDPG.
- **Competitive learning:** self-play, PSRO/α-Rank, league training, population-based training.
- **Coordination theory:** ad hoc teamwork, zero-shot coordination (other-play, off-belief learning), emergent communication, theory of mind (Hanabi).
- **Game theory:** team-maximin / correlated equilibria, difference rewards / Shapley credit, mean-field games.

## References (seminal → modern)

- Shapley, L. (1953). *Stochastic Games.*
- Littman, M. (1994). *Markov games as a framework for multi-agent RL.*
- Bernstein, Givan, Immerman & Zilberstein (2002). *The complexity of decentralized control of Markov decision processes* (Dec-POMDP NEXP-completeness).
- Wolpert, D. & Tumer, K. (2002). *Optimal payoff functions for members of collectives* (difference rewards).
- Lowe et al. (2017). *Multi-Agent Actor-Critic for Mixed Cooperative-Competitive Environments* (MADDPG).
- Foerster et al. (2018). *Counterfactual Multi-Agent Policy Gradients* (COMA).
- Sunehag et al. (2018). *Value-Decomposition Networks* (VDN); Rashid et al. (2018). *QMIX*; Son et al. (2019). *QTRAN.*
- Jaderberg et al. (2019). *Human-level performance in 3D multiplayer games with population-based RL* (Capture-the-Flag, *Science*).
- Berner et al. (2019). *Dota 2 with Large Scale Deep RL* (OpenAI Five); Vinyals et al. (2019). *Grandmaster level in StarCraft II* (AlphaStar).
- Samvelyan et al. (2019). *The StarCraft Multi-Agent Challenge* (SMAC); Carroll et al. (2019). *On the Utility of Learning about Humans for Human-AI Coordination* (Overcooked).
- Hu et al. (2020). *“Other-Play” for Zero-Shot Coordination*; Bard et al. (2020). *The Hanabi Challenge.*
- Yu et al. (2021). *The Surprising Effectiveness of MAPPO in Cooperative Multi-Agent Games.*

*Uncertainty flags:* commercial CoD/CS/Overwatch bots are largely undocumented (scripted + selective RL); the rigorous results are from adjacent team games and benchmarks, applied here by structural analogy. Convergence/exploitability claims for team self-play are empirical, not proven; equilibrium guarantees do not generally hold for >2-coalition or general-sum settings.
