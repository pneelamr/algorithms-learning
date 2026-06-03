# Team objective FPS - cooperative–competitive multi-agent RL — Level 5: Graduate

> **Example problems:** Team objective FPS (Call of Duty multiplayer, Counter-Strike, Overwatch), capture-the-flag, Dota 2 / MOBAs  ·  **Type:** Cooperative–competitive multi-agent RL (ensemble)  ·  **Guarantee:** Dec-POMDP optimal is NEXP-complete; team-zero-sum self-play has no convergence guarantee in general; value-decomposition methods are exact only under the IGM condition
> **Used for:** Coordinating a team to win an objective/score race, valuing objective control and credit-assigned teamplay over greedy individual kills
> **Level 5 of 6** — graduate: formal models, paradigm, bounds, credit assignment, the respawn/objective contrast, optimizations. See sibling files for other levels.

---

## 1. Formal setting: a team Markov game

Let two teams `B` (blue) and `R` (red), `|B|=|R|=n`. The game is a **partially observable Markov game** `(S, {A_i}, T, {R_i}, {Ω_i}, O, γ)` with team structure:

- **Within-team cooperation:** all agents in a team share one reward, `R_i = R_team(B)` for `i∈B`. With shared reward and per-agent observations, the within-team problem is a **Dec-POMDP** (Bernstein et al.): each agent acts on its own action-observation history `τ_i`, no execution-time communication.
- **Between-team competition:** `R_team(B) = −R_team(R)` (team zero-sum). The two cooperating teams play a **zero-sum Markov game** against each other (Littman 1994; Shapley 1953).

The objective is the **cumulative discounted team return** `J = E[Σ_t γ^t R_team(t)]`, where `R_team` measures **objective control** (zone ticks, captures, score), *not* survival. **Respawn consequence:** an agent's death is a bounded negative transient (downtime `d`), not an absorbing state — so, unlike the Survival ensemble's CVaR/“don’t-die” objective, the optimal policy here is **risk-neutral toward death** and will trade lives for objective expectation. The "slaying off the objective" pathology is precisely **reward misspecification / Goodhart**: optimizing a proxy (kills) correlated-but-not-identical to `R_team`.

**Solution concept.** A **team-maximin equilibrium**: `max_{π_B} min_{π_R} J(π_B, π_R)`, with intra-team play cooperative (potentially correlated). Exact computation is intractable (below); practice targets approximate equilibria via population methods.

## 2. Complexity

- **Finite-horizon Dec-POMDP (cooperative core): NEXP-complete** (Bernstein, Givan, Immerman & Zilberstein 2002) — doubly exponential, vs. PSPACE for single-agent POMDP. The blow-up is from decentralized information: agents cannot condition on a common belief.
- Adding the competitive outer game (team-zero-sum) does not reduce this. ⇒ exact solving is hopeless; the paradigm is **learned, decentralized approximation under CTDE**.

## 3. CTDE and value decomposition (cooperative axis)

**Centralized Training, Decentralized Execution:** learn with a centralized critic `Q(s, a_1..a_n)` (sees global state + joint actions, defeating nonstationarity), execute with decentralized `π_i(a_i|τ_i)`.

**The IGM (Individual-Global-Max) principle** — the condition under which decentralized greedy action selection is globally optimal:
`argmax_{a} Q_tot(s,a) = ( argmax_{a_i} Q_i(τ_i, a_i) )_{i=1}^n`.

- **VDN** (Sunehag et al. 2018): `Q_tot = Σ_i Q_i` (additive) — sufficient but restrictive.
- **QMIX** (Rashid et al. 2018): `Q_tot = f_mix(Q_1..Q_n; s)` with `∂f_mix/∂Q_i ≥ 0` (monotonic mixing via a hypernetwork conditioned on `s`). Monotonicity ⇒ IGM, while representing a richer class than VDN. Cannot represent non-monotonic value interactions (e.g. coordinated "miscoordination penalties").
- **QTRAN / Weighted QMIX:** relax monotonicity to cover the full IGM class at added complexity.

## 4. Multi-agent credit assignment (the heart of the matter)

"Who contributed to the win?" is the central difficulty. Approaches:

- **Difference rewards / counterfactual baseline — COMA** (Foerster et al. 2018): advantage
  `A_i(s,a) = Q(s,a) − Σ_{a_i'} π_i(a_i'|τ_i) Q(s, (a_{-i}, a_i'))`,
  marginalizing agent `i`'s action with teammates fixed — a low-variance, agent-specific credit signal. Equivalent in spirit to **aristocrat/difference-utility** rewards (Wolpert & Tumer).
- **Value decomposition** (above) assigns credit implicitly through the learned `Q_i`.
- **Shapley-value credit** (recent): axiomatic fair attribution of the team return to agents; principled but expensive (exponential coalitions, approximated by sampling).

## 5. Policy-gradient workhorses & the competitive axis

- **MADDPG** (Lowe et al. 2017): per-agent deterministic policies + per-agent centralized critics; handles mixed coop/competitive and continuous actions.
- **MAPPO** (Yu et al. 2021): PPO with a centralized value function; a strong, simple baseline that often matches value-decomposition methods on cooperative benchmarks.
- **Competitive/between-team learning:** **self-play** with **league / population** training to avoid strategy cycles and exploitability — **PSRO** (Lanctot et al. 2017), AlphaStar-style leagues, **Population-Based Training** (Jaderberg et al.). The "team spirit" interpolation (OpenAI Five) anneals each agent's reward from selfish (`R_i`) toward fully shared (`R_team`), explicitly trading off the cooperative–competitive tension during training.

```mermaid
flowchart LR
    subgraph Cooperative axis (within team)
        Q1["Q_1(tau_1,a_1)"] --> MIX["monotonic mixing f_mix(.;s)"]
        Q2["Q_2(tau_2,a_2)"] --> MIX
        Qn["Q_n(tau_n,a_n)"] --> MIX
        MIX --> QT["Q_tot (IGM-consistent)"]
        QT --> CR["credit: COMA counterfactual / value decomposition"]
    end
    subgraph Competitive axis (between teams)
        SP["self-play vs past/other teams"] --> LG["league / PSRO / PBT"]
        LG --> ES["approximate team-maximin"]
    end
    CR --> POL["decentralized policies pi_i(a_i|tau_i)"]
    ES --> POL
    POL -. "objective reward R_team (respawns => risk-neutral to death)" .-> Q1
```

## 6. Edge cases, invariants, optimizations

- **Mode structure changes the reward, not the machinery:** TDM (kills *are* the objective — greedy nearly aligns), Domination/Hardpoint (zone-control reward), Kill-Confirmed (kills gated by tag pickup), and **Search & Destroy** as the **hybrid bridge** — one life per round (round-terminal, so survival/elimination logic from [[survival-games-expectimax-to-multi-agent-rl]] re-enters) inside a 2-team objective game.
- **Nonstationarity & moving targets:** simultaneous learners; CTDE + on-policy correction + population diversity are the standard mitigations.
- **Partial observability:** recurrent/transformer per-agent encoders over `τ_i`; beware observation aliasing and belief collapse.
- **Invariant (QMIX):** monotone mixing ⇒ decentralized greedy = centralized greedy (IGM); violating monotonicity breaks deployability.
- **Reward shaping discipline:** shape toward `R_team` (objective), not kills, to avoid Goodharting; potential-based shaping preserves optimal policies.

**Synthesis:** this ensemble is the **cooperative–competitive** corner of the family: cooperate within a team (Dec-POMDP, NEXP-hard ⇒ CTDE with value decomposition / counterfactual credit) and compete between teams (zero-sum Markov game ⇒ self-play leagues). Because the objective is **score/objective control** and **respawns** make death cheap, the optimal policy is risk-neutral toward dying and **passes greedy kills to play the objective** — the same long-horizon lesson as [[adversarial-game-tree-search-minimax-to-alphazero]] and [[survival-games-expectimax-to-multi-agent-rl]], reached through team credit assignment rather than search or survival.
