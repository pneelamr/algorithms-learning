# Survival games: expectimax to multi-agent RL — Level 1: ELI5

> **Example problems:** Battle royale (Fortnite / PUBG / Call of Duty Warzone), poker, hide-and-seek-style many-player games  ·  **Type:** Stochastic + imperfect-info + multi-agent RL (ensemble)  ·  **Guarantee:** None — too much hidden, too random, too many players for a perfect plan
> **Used for:** Picking actions that give you the best chance of *being the last one standing*, instead of grabbing the nearest target right now
> **Level 1 of 6** — explain-like-I'm-5: one analogy, no jargon. See sibling files for other levels.

---

Imagine a giant game of tag where the goal is to be the **very last person not tagged** — and there are a hundred kids, lots of hiding spots, and you can't see where everyone is. You *could* run over and tag the kid next to you… but if you do, you make a big noise, everyone turns to look, and three other kids come gang up on *you*.

So the clever player often **doesn't** tag the nearby kid. Instead they grab the best toys, sneak to a safe spot, and wait — because winning isn't about tagging the most kids, it's about **being the last one left**. And a computer gets good at this by playing the game a zillion times against copies of itself until it learns when to hide, when to grab stuff, and when a fight is actually worth it.
