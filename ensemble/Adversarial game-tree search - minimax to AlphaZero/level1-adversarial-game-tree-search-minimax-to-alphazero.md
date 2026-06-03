# Adversarial game-tree search - minimax to AlphaZero — Level 1: ELI5

> **Example problems:** Chess, Go, Checkers, two-player zero-sum games  ·  **Type:** Adversarial search + RL (ensemble)  ·  **Guarantee:** Minimax is perfect against perfect play if you can look all the way to the end; the "learning" part has no promise
> **Used for:** Picking the move that *wins the game later* instead of the move that *grabs the most stuff right now* — and getting better every time you play
> **Level 1 of 6** — explain-like-I'm-5: one analogy, no jargon. See sibling files for other levels.

---

Imagine you're playing a game and you *could* grab your friend's game piece right now. But a clever player stops and imagines a few turns into the future: "If I grab it, what mean thing can my friend do back? And then what would I do?" Sometimes you find out that **waiting and doing a smarter move wins the whole game**, even though grabbing the piece felt good for one second.

So a really good game-playing robot does two things: it **imagines lots of future turns and picks the move that wins** (not just the move that grabs the most right now), and after every game it **remembers what worked and what didn't**, so it plays a little better the next time. That mix — looking ahead *and* learning from playing — is how computers got so good they can beat the best people in the world at chess and Go.
