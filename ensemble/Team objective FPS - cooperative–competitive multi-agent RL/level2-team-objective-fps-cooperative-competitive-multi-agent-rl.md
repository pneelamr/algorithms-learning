# Team objective FPS - cooperative–competitive multi-agent RL — Level 2: ELI10

> **Example problems:** Team objective FPS (Call of Duty multiplayer, Counter-Strike, Overwatch), capture-the-flag, Dota 2 / MOBAs  ·  **Type:** Cooperative–competitive multi-agent RL (ensemble)  ·  **Guarantee:** None — two teams, partial information, and respawns make exact optimal play impossible
> **Used for:** Helping your team win the objective/score race, instead of chasing personal kills
> **Level 2 of 6** — explain-like-I'm-10: the analogy expanded, a plain walk-through of the parts, and why it matters. See sibling files for other levels.

---

## The story

In a team objective shooter — think Call of Duty's Domination or Hardpoint, or Counter-Strike — the **greedy** habit is "see enemy, get kill." But the scoreboard that decides the match isn't your kill count; it's whether your **team** holds the objective. You can top the kills chart and still *lose* — that mistake is so common it has a name: **"slaying off the objective."**

This is a *different* world from battle royale (our Survival ensemble), and two differences flip the whole strategy:

1. **You respawn.** Dying isn't game-over — you're back in a few seconds. So trading your life to capture the point is often a *great* deal. (In a battle royale, dying ends your game, so you play scared; here you can play bold *for the objective*.)
2. **It's two teams, not a free-for-all.** Helping your side is purely good — there's no "you just helped 99 rivals" problem. But it means you must **coordinate**: cover each other, focus the same enemy, split jobs (one pushes, one watches the flank).

So the thing you optimize is **team objective control**, and the hard part is getting several players who each see only their own little slice of the map to act like one smart team. That bundle is **Team objective FPS: cooperative–competitive multi-agent RL**. Its parts in plain words:

- **Greedy kill-seeking (the trap).** Maximizing *your* kills is a tempting fake goal that often loses the match.
- **Team value.** Score the *situation* by "are we winning the objective?", not "how many kills do I have?"
- **Coordination.** Players need roles and timing — who caps, who defends, who distracts — even though each only sees part of the map.
- **Learning together, acting alone (the AI part).** Train a whole team at once with a "coach" that can see everything during practice, but at game time each player decides using only what *it* sees. (Grown-ups call this *centralized training, decentralized execution*.)
- **Figuring out who helped.** When the team wins, which players' choices actually mattered? Sorting that out (**credit assignment**) is how each player learns the right habits.

## Why we care

The "don't grab the greedy reward, play for the bigger goal" lesson is the same one from chess and battle royale — but here the bigger goal is **the objective**, the win is a **team** effort, and **respawns** mean you can spend your life freely *for that objective*. This exact recipe is how AIs reached human level at team Capture-the-Flag and beat the world's best at Dota 2 — as coordinated teams, not lone heroes.
