# Minimax to AlphaZero — Level 3: High-school student

> **Example problems:** Chess, Go, Checkers, two-player zero-sum games  ·  **Type:** Adversarial search + RL (ensemble)  ·  **Guarantee:** Minimax is optimal against optimal play at full depth; the learned/self-play components have no guarantee
> **Used for:** Picking the move that wins the game later instead of the move that grabs the most material now — and learning better evaluations across games
> **Level 3 of 6** — high-school: a worked mini-example with real numbers, numbered steps, and intuition for how it works and where it breaks. See sibling files for other levels.

---

## A tiny worked example: the poisoned pawn

It's your move (you're **MAX** — bigger score is better for you). We score positions in "pawn points": **+** good for you, **−** good for your opponent (**MIN**). You're looking just **2 plies** ahead (your move, then their reply), and you score the positions you reach.

You have two candidate moves:

- **Move A — grab the free pawn.** Feels great: +1 material immediately. But it opens a line. Your opponent now has two replies:
  - reply A1 → position scores **+1** (you're simply up a pawn)
  - reply A2 → a fork that wins your queen → **−8**
- **Move B — a quiet developing move.** No material grabbed. Opponent's replies:
  - reply B1 → **+2**
  - reply B2 → **+3**

Now back the scores up the tree. Your opponent is MIN, so at *their* turn they pick the move that's **worst for you (smallest number)**:

```
            YOU (MAX) to move
           /                 \
     Move A                   Move B
   (grab pawn)             (quiet move)
     /     \                 /     \
  +1       -8             +2       +3      <- scores after opponent's reply
   \       /               \       /
   MIN picks -8            MIN picks +2    <- opponent plays their best (worst for you)
```

- Move A's real value = **−8** (the opponent will choose the queen fork, not the friendly +1).
- Move B's real value = **+2** (the opponent picks +2 over +3).

**Greedy** looks only at the immediate board and grabs the pawn → it plays **A** and loses a queen. **Minimax** plays **B** (+2 beats −8). The pawn was *poisoned*, and looking one move deeper revealed it.

## The procedure (minimax)

1. **Build the game tree** down to some depth *d* (here d = 2), alternating MAX and MIN levels.
2. **Score the leaves** at the bottom with an *evaluation function* (material, king safety, space…).
3. **Back the scores up:** at a MAX level take the **maximum** of the children; at a MIN level take the **minimum**.
4. **Choose** the move at the root with the best backed-up score.

That's it. Minimax = "assume your opponent always answers with their best, and pick the move that's best for you *after* that."

## Where it goes wrong — and the fixes

- **The horizon effect.** Suppose you'd only looked **1 ply** ahead. You'd never see the −8 queen fork (it happens on ply 2), so you'd pick A and blunder. Real danger always seems to lurk *just past where you stopped looking.* **Fix:** **quiescence search** — whenever the position is "loud" (captures, checks in progress), don't stop at the depth limit; keep searching the captures until the board is *quiet*, then score. This stops the engine from freezing the count in the middle of a trade.

- **It's expensive.** A tree with branching factor *b* and depth *d* has about *b^d* leaves — for chess, b ≈ 35, so it explodes fast. **Fix (preview of Level 4):** *alpha-beta pruning* skips branches that can't change the answer, roughly square-rooting the work.

- **The eval is just a guess.** Steps 2–4 are only as good as the leaf scores. Hand-written rules ("a pawn = 1, a knight = 3…") are crude. **Fix (the AlphaZero leap):** **learn** the eval. Let the program **play itself thousands of times**; whenever a kind of position kept leading to losses, nudge its learned score *down*. The final win/loss/draw signal flows backward to teach every position on the way there. Over millions of self-play games it grows a gut-feel evaluation far better than anything humans hand-coded — and *that*, bolted onto the same look-ahead search, is what took us from 1950s **Minimax** to 2017 **AlphaZero**.

**Intuition to keep:** look ahead, assume a smart opponent, peek further when things are noisy, and let real outcomes teach your sense of "good position." The greedy grab is a trap; the win lives a few moves past it.
