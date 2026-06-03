# Survival games: expectimax to multi-agent RL — Level 3: High-school student

> **Example problems:** Battle royale (Fortnite / PUBG / Call of Duty Warzone), poker, partially-observable many-player games  ·  **Type:** Stochastic + imperfect-info + multi-agent RL (ensemble)  ·  **Guarantee:** None; hidden info, randomness, and many agents preclude exact optimal play
> **Used for:** Choosing actions that maximize expected survival/placement under uncertainty, instead of greedily taking the nearest kill
> **Level 3 of 6** — high-school: a worked mini-example with real numbers, numbered steps, and intuition for how it works and where it differs from chess. See sibling files for other levels.

---

## A tiny worked example: should I take the kill?

You spot a slightly-weaker enemy nearby. Greedy says "I'd win that 1v1, go!" Let's actually compute it. We'll measure each outcome by a **survival score** = roughly how well-set-up you are to place high (0 = eliminated, 10 = great position). The key is that the future is **random**, so we use **expected value** (probability-weighted average), not certainty.

**Option A — push the kill.** You'd likely win the duel, but fighting is loud:
- 0.55 → you win cleanly, grab their loot → score **8**
- 0.35 → a **third party** hears the fight and pushes you while you're low → eliminated → score **0**
- 0.10 → you actually lose the 1v1 → eliminated → score **0**

`EV(A) = 0.55·8 + 0.35·0 + 0.10·0 = 4.4`

**Option B — pass, loot quietly, rotate to the safe zone.**
- 0.85 → reach the next circle with good supplies and position → score **7**
- 0.15 → caught while rotating (storm or another player) → score **2**

`EV(B) = 0.85·7 + 0.15·2 = 5.95 + 0.30 = 6.25`

**`EV(B) = 6.25 > EV(A) = 4.4` → pass the kill.** Notice *why* greedy was wrong: it only looked at "I'd win the duel" (the 0.55 branch) and ignored the **chance node** — the 35% third-party disaster. That 35% is exactly the cost chess doesn't have: fighting *reveals you* and invites others in.

## The procedure (expectimax)

This is **expectimax** — minimax's cousin for games with randomness:

1. **Build a tree** of your choices and the random events that follow each.
2. **Your nodes (you choose):** take the **maximum**-value action.
3. **Chance nodes (the game/randomness chooses):** take the **probability-weighted average** of the outcomes — *not* the worst case. (Whether a shot hits, whether a third party shows, what loot drops.)
4. **Pick** the action at the root with the best expected value.

The one-line difference from chess: at the opponent/uncertainty step, chess minimax assumes the **worst** (a single smart adversary); survival games **average over** what's likely, because much of "what happens next" is luck and crowd behavior, not one perfect enemy.

## What you actually optimize (the bigger goal)

Not kills — **placement / survival**. Concretely you maximize expected survival score by managing:
- **Resources** (health, shields, ammo, materials) — don't burn them cheaply.
- **Position vs. the shrinking safe zone** — rotating early and holding high ground is usually worth more than a kill.
- **Information** — staying unseen *is* an advantage; fighting spends it.

## Where it gets harder than the example (and what fixes it)

- **You can't see the enemies.** Real battle royales hide most players. So you don't have exact probabilities — you keep a **belief** (a best-guess map of where enemies likely are) and average over *that*. (Formalized at Level 4 as a POMDP.)
- **Opponents adapt.** If you always rotate the same way, smart players camp that route and punish you. A fixed plan is **exploitable**, so you want a strategy that's hard to read — and you can't hand-write it.
- **The fix — learn by self-play.** Let the AI play **millions** of matches against copies of itself (**multi-agent reinforcement learning**): actions that lead to surviving get reinforced, actions that get you killed fade away. Over time it learns *when* the kill is worth it and when to pass — including the messy stuff (third-party risk, bluffing, positioning) no formula captures. That self-play-learned policy, sitting on top of "average over the uncertainty," is the jump from **expectimax** to **multi-agent RL**.

**Intuition to keep:** average over the randomness, plan against your best guess of hidden enemies, remember the goal is to *survive to the end*, and let real win/lose outcomes teach you when a fight is worth its noise and risk.
