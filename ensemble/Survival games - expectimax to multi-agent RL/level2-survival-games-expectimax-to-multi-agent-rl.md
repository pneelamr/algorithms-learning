# Survival games: expectimax to multi-agent RL — Level 2: ELI10

> **Example problems:** Battle royale (Fortnite / PUBG), poker, hide-and-seek-style many-player games  ·  **Type:** Stochastic + imperfect-info + multi-agent RL (ensemble)  ·  **Guarantee:** None — hidden information, randomness, and many players rule out a perfect plan
> **Used for:** Picking actions that maximize your chance of being the last one standing, instead of grabbing the nearest target right now
> **Level 2 of 6** — explain-like-I'm-10: the analogy expanded, a plain walk-through of the parts, and why it matters. See sibling files for other levels.

---

## The story

In a battle royale (think Fortnite), the obvious **greedy** move is: see an enemy nearby, go for the kill. But here's the twist that makes survival games different from chess — **the goal was never the kill.** You win by being the **last one alive** (your *placement*), and you can win a whole match with **zero** eliminations.

So you constantly ask: "Is this fight worth it?" Often the answer is **no**, and you *pass* the kill. Three reasons:

1. **Third parties.** The second you start shooting, the noise tells *everyone* where you are. While you're busy (and now low on health and ammo), a fresh third player rushes in and finishes you. You'd be trading one kill for becoming someone else's kill.
2. **You're helping your enemies.** There are ~100 players. Removing one helps *all 99 others*, not just you — but *you* paid the whole price (ammo, healing, getting noticed). You did everyone else a favor.
3. **Supplies and safe ground matter more.** Your real resources are health, shields, ammo, building materials, and **good position relative to the safe zone** (the map shrinks, and you must keep moving inside it). Wasting those on a pointless kill leaves you weak for the fights that actually decide the game.

This whole bundle — from "average over the randomness" to "teach yourself by playing" — is what we're calling **Survival games: expectimax to multi-agent RL**. Its parts in plain words:

- **Expectimax (handle the randomness).** Unlike chess, lots of stuff is *chance*: where good loot lands, whether your shot hits, whether a third party shows up. Instead of assuming the single worst case, you **average over what's likely to happen** and pick the action with the best *average* outcome.
- **Belief / hidden information.** You can't see most players. So you keep a **best guess** of where enemies probably are, and plan against that guess instead of a known board.
- **Many adaptive players.** It's not one opponent — it's a crowd that *also* learns. A predictable plan gets punished, so you want a style that's **hard to exploit.**
- **Learning by self-play (multi-agent RL).** The computer plays *millions* of matches against copies of itself, keeping what survives and dropping what dies, until it just "knows" when to fight, hide, loot, or rotate.

## Why we care

The intuition is the same one from chess — *don't grab the nearby reward; play for the long-horizon win* — but survival games add two hard truths: the greedy move **reveals you and helps your rivals**, and the real goal (**survive to the end**) was never about kills at all. This same thinking runs poker, real-world competition, and how AIs learned to beat pros at Dota 2 and StarCraft.
