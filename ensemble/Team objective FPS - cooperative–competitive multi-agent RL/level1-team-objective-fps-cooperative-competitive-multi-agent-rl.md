# Team objective FPS - cooperative–competitive multi-agent RL — Level 1: ELI5

> **Example problems:** Team objective FPS (Call of Duty multiplayer, Counter-Strike, Overwatch), capture-the-flag, Dota 2 / MOBAs  ·  **Type:** Cooperative–competitive multi-agent RL (ensemble)  ·  **Guarantee:** None — two teams, hidden enemies, and players who keep respawning make a perfect plan impossible
> **Used for:** Helping your *team* win the objective, instead of running off to tag the most enemies yourself
> **Level 1 of 6** — explain-like-I'm-5: one analogy, no jargon. See sibling files for other levels.

---

Imagine a playground game of **capture-the-flag with teams**. Your team doesn't win by tagging the most kids — you win by **holding the flag** (or standing on the special spot). So sometimes the smart thing is to *run right past* a kid you could tag and go stand on the spot, because that's what actually wins.

Two things make it different from hide-and-seek: you have **teammates** you must work *with* (one guards while another grabs the flag), and if you get tagged you just **wait a few seconds and come back** — so it's okay to take a risk for the team. A computer learns to play this way by having whole **teams of itself practice against other teams** thousands of times, until each player learns its job and they win *together*.
