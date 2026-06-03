# Adversarial game-tree search - minimax to AlphaZero — Level 2: ELI10

> **Example problems:** Chess, Go, Checkers, two-player zero-sum games  ·  **Type:** Adversarial search + RL (ensemble)  ·  **Guarantee:** Minimax is perfect against perfect play if you can look all the way to the end; the learned part has no promise
> **Used for:** Picking the move that *wins the game later* instead of the move that *grabs the most stuff right now* — and getting better every time you play
> **Level 2 of 6** — explain-like-I'm-10: the analogy expanded, a plain walk-through of the parts, and why it matters. See sibling files for other levels.

---

## The story

You're playing chess and you spot a free pawn. The **greedy** move is obvious: grab it. Greedy means *always do the best-looking thing right in front of you.* It's fast, but it has no idea what happens next — and in chess, "next" is where you win or lose.

A strong player (or a strong program) instead **thinks ahead**. This whole bundle of ideas — from old-school "think ahead" to modern "teach yourself" — is what we're calling **adversarial game-tree search** — the arc from **minimax to AlphaZero**. Here are its parts in plain words:

1. **Greedy (the thing we're beating).** Take the best immediate gain. Simple, but short-sighted — it'll happily grab a "poisoned" pawn that loses the game.

2. **Minimax (think ahead, expect a smart opponent).** You imagine your move, then your opponent's *meanest* reply, then your best answer to that, and so on, a few turns deep. You assume the opponent always plays their best. Then you pick the move that turns out best *after* all that back-and-forth — which might mean **skipping the free pawn** because grabbing it lets your opponent do something nasty.

3. **The horizon trap (and how to dodge it).** You can only imagine so many turns ahead — that edge is your "horizon." The danger: the bad surprise happens *one turn past where you stopped looking*, so you never see it. The fix is to **keep peeking a little further whenever the board is "noisy"** (pieces are being captured) until things calm down. That extra peek is called *quiescence* ("quiet") searching.

4. **An eval (a gut-feeling score).** You can't look all the way to the end of chess — there are too many possibilities. So at the edge of how far you looked, you give the position a **score**: who's ahead, whose king is safer, who has more room. The whole "think ahead" machine is only as smart as this gut-feeling score.

5. **Learning the gut feeling (the AlphaZero part).** Here's the leap. Instead of a human writing down the scoring rules, the program **plays itself millions of times** and slowly learns which positions *tend* to lead to wins. If "grab that pawn" lost it a thousand games, it learns to feel uneasy about that kind of position — *without anyone telling it why.* That's reinforcement learning: the reward (win/lose) at the very end teaches it about every move that led there.

## Why we care

Put those together and you get the arc from **Minimax** (1950s "look ahead and assume a smart opponent") to **AlphaZero** (2017 "look ahead *and* teach yourself the gut feeling from scratch"). It's the exact recipe behind computers beating world champions at chess, checkers, and Go — and the big lesson travels far beyond board games: **don't just grab the nearest reward; look ahead, respect your opponent, and learn from how things actually turn out.**
