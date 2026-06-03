# Team objective FPS - cooperative–competitive multi-agent RL — Level 3: High-school student

> **Example problems:** Team objective FPS (Call of Duty multiplayer, Counter-Strike, Overwatch), capture-the-flag, Dota 2 / MOBAs  ·  **Type:** Cooperative–competitive multi-agent RL (ensemble)  ·  **Guarantee:** None; a two-team, partially-observable game with respawns has no exact optimal solution
> **Used for:** Coordinating a team to win the objective/score race, valuing objective control and teamplay over greedy individual kills
> **Level 3 of 6** — high-school: a worked mini-example with numbers, numbered steps, and intuition for how it works and where it differs from the other ensembles. See sibling files for other levels.

---

## A tiny worked example: chase the kill, or cap the point?

It's a Domination match (hold zones A/B/C; you score while you hold the majority). You're near a low-health enemy, *away* from the open point B. We'll score each choice by your team's **win probability**.

**Option A — chase the kill (off the objective).**
- 0.70 → you get the kill (enemy gone for ~8 s) — but while you were away, the enemy caps **B**, so your team holds only **1 of 3** points.
- 0.30 → you whiff and they escape.
- Either way your team is losing the zone race → win prob ≈ **0.40**.

**Option B — ignore the enemy, capture B.**
- You cap **B**; your team now holds **2 of 3** and scores faster → win prob ≈ **0.62**.
- Risk: the enemy you ignored kills you right after. But you **respawn in ~7 s**, and **B stays captured** → win prob only dips to ≈ **0.58**.

`EV(A) ≈ 0.40` vs `EV(B) ≈ 0.58–0.62` → **pass the kill, take the point.**

Notice the punchline: you reach the *same* "pass the kill" conclusion as in battle royale — but for a **different reason**. In the Survival ensemble you passed because *dying ends your game* (survival). Here you pass because **the objective is what scores**, and **respawning means dying for the point is cheap**. Greedy was wrong because it optimized *kills* (a fake goal) instead of *objective control* (the real one) — classic "slaying off the objective."

## The procedure (cooperative–competitive multi-agent RL)

You can't write the perfect team plan by hand, so you **learn** it. The setup:

1. **Two teams; each player sees only its own view** (partial information — you don't know where all enemies are).
2. **Team reward = the objective** (zone ticks / score / flag caps), **shared by everyone on the team** — not personal kills.
3. **Train with a "coach" that sees everything** (a centralized critic during practice), while **each player acts on only its own view** at game time. This is *centralized training, decentralized execution* (CTDE).
4. **Credit assignment:** after a win, work out *which* players' actions actually helped — e.g. ask "would we still have won if this player had done nothing?" Players whose choices mattered get reinforced.
5. **Self-play / leagues:** teams play against other teams, including older versions of themselves, so they don't just learn to beat one specific style.

## Where greedy goes wrong (and the recurring lesson)

- **Optimizing the wrong number.** Reward kills and you get kill-chasers who lose; reward the *objective* and you get teamplay. Picking the right thing to reward is the whole game (mess it up and you get "slaying off the objective").
- **Selfish vs. team.** A player hogging kills can starve the team. Good systems blend "help yourself" and "help the team" — real AIs literally tune a **team-spirit** dial from selfish toward team-first as training goes on.
- **Coordination is hard with partial views.** Five players each seeing a sliver must still move as one. That's why the *centralized coach + learned habits* approach beats hand-written rules.

**Intuition to keep:** the scoreboard is the **team objective**, not your kills; **respawns** make trading your life for the point a good deal; and a team of agents learns to cooperate by training together with a coach that sees all, then each acting on what it alone sees. Pass the greedy kill — win the point.
