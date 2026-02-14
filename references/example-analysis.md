# Example Analysis

A worked example showing the analysis process and output format. Read this for calibration on depth and tone.

## Source Rules (abbreviated)

> **Dragon's Hoard** (2-4 players, 45 min)
>
> **Goal:** Be the first player to collect 10 treasure tokens.
>
> **Setup:** Shuffle the dungeon deck. Each player draws 5 cards. Place 30 treasure tokens in the supply. Each player starts with 3 health.
>
> **Turn structure:**
> 1. Draw 1 card
> 2. Play up to 2 cards
> 3. Discard down to 7 cards
>
> **Card types:**
> - **Monster:** Play to attack another player. They lose health equal to the monster's power. If their health reaches 0, they lose all their treasure.
> - **Shield:** Play in response to an attack to prevent damage.
> - **Treasure Map:** Play to gain 2 treasure tokens from the supply.
> - **Heal:** Play to restore 2 health.
> - **Dragon:** Play to steal 3 treasure from any opponent.
>
> **When a player reaches 0 health:** They lose all treasure tokens (return to supply), discard their hand, draw 5 new cards, and reset health to 3.
>
> **Game end:** The first player to have 10 treasure tokens at the end of their turn wins.

---

## Analysis Output

### Summary

Analyzed the rules for **Dragon's Hoard** (2-4 players). Found **2 critical**, **2 high**, **3 medium**, and **1 low** severity defects across 6 of 10 analysis categories.

The game has a fundamentally playable core loop, but two critical issues around termination and trigger timing need resolution before the rules work as written.

---

### CRITICAL — Treasure stalemate prevents game end

**Category:** Termination Guarantees
**Location:** Game end condition + Dragon card
**The problem:** The game ends when a player has 10 treasure at end of turn. The Dragon card steals 3 treasure. With 4 players and 30 treasure tokens total, if players continuously steal treasure from the leader, no one can sustain 10 treasure. The game has no round limit or alternative end condition.
**Why it matters:** In a 4-player game with experienced players, the leader will always be targeted by Dragon cards, creating a stable equilibrium where no one reaches 10. The game never ends.
**Suggested fix:** Add a secondary end condition: "If the dungeon deck is exhausted a second time, the game ends immediately. The player with the most treasure wins." Alternatively, add escalation: "Each time the deck is reshuffled, the treasure required to win decreases by 2 (minimum 4)."

### CRITICAL — Shield timing undefined

**Category:** Trigger & Timing Conflicts
**Location:** Shield card description
**The problem:** Shield says "Play in response to an attack." But the turn structure only allows playing cards during step 2 of your own turn. There is no defined reaction window during other players' turns.
**Why it matters:** As written, Shield cards are unplayable. Players can never use them because they can only play cards on their own turn, but attacks happen on opponents' turns.
**Suggested fix:** Add a reaction rule: "When you are attacked, before damage is dealt, you may play one Shield card from your hand, even though it is not your turn. This does not count toward your 2-card play limit."

---

### HIGH — Infinite treasure from empty supply

**Category:** Resource Integrity
**Location:** Treasure Map card + supply
**The problem:** Treasure Map says "gain 2 treasure tokens from the supply." No rule says what happens when the supply is empty. If the 30 tokens are all distributed among players, Treasure Maps do nothing — but can Dragon cards still steal treasure? If so, treasure still circulates but new treasure can't enter. If tokens are returned to supply on knockout, the system works, but this depends on knockout frequency.
**Why it matters:** In late game, Treasure Maps may become dead cards, distorting the card balance. Players may not realize this until mid-game.
**Suggested fix:** Add: "If the supply has fewer treasure tokens than required, gain as many as available." Also clarify the knockout rule already returns tokens to supply, so this is the recirculation mechanism.

### HIGH — Dragon targeting at 0 treasure

**Category:** Edge Case Coverage
**Location:** Dragon card
**The problem:** Dragon says "steal 3 treasure from any opponent." What if the targeted opponent has fewer than 3 treasure? Do you steal what they have, or is the card unplayable against that opponent?
**Why it matters:** This comes up frequently in actual play and leads to table arguments. It also affects strategy — can you play Dragon on a player with 0 treasure (to waste a card and a play action for no effect)?
**Suggested fix:** Add: "Steal up to 3 treasure from any opponent (take as many as they have, up to 3)."

---

### MEDIUM — Health maximum undefined

**Category:** Resource Integrity
**Location:** Heal card + setup
**The problem:** Players start with 3 health. Heal restores 2 health. No maximum health is stated. Can a player reach 5, 7, 99 health?
**Why it matters:** Unbounded health makes knockout (and its treasure-reset penalty) nearly impossible after a few Heal cards, which indirectly affects the treasure economy.
**Suggested fix:** Add: "A player's health can never exceed 5." Or: "Heal up to your starting health (3)."

### MEDIUM — Deck exhaustion unaddressed

**Category:** Component Arithmetic
**Location:** Turn structure (draw step)
**The problem:** "Draw 1 card" at start of turn. No rule says what happens when the dungeon deck runs out. With 4 players drawing every turn and playing 2 cards, the deck depletes quickly.
**Why it matters:** Game halts because players can't perform the mandatory draw step.
**Suggested fix:** Add: "When the dungeon deck is empty, shuffle the discard pile to form a new dungeon deck. If both the deck and discard pile are empty, skip the draw step."

### MEDIUM — Knockout during your own turn

**Category:** Turn Structure Consistency
**Location:** Monster card + knockout rule
**The problem:** Can a player be knocked out during their own turn? If you play a Monster card and a triggered effect (not defined, but possible in expansions) damages you to 0 health, do you finish your turn? Do you discard and redraw mid-turn?
**Why it matters:** Currently unlikely with base cards, but the rules framework doesn't handle it, creating fragility for future cards.
**Suggested fix:** Add: "If you are reduced to 0 health during your turn, complete the current action, then resolve the knockout. Your turn ends immediately after knockout."

---

### LOW — Inconsistent component naming

**Category:** Cross-Rule Contradiction
**Location:** Setup vs. card descriptions
**The problem:** Setup says "dungeon deck" but card types section just says "cards." The treasure tokens are called "treasure tokens" in setup but just "treasure" on card descriptions.
**Why it matters:** Minor confusion for first-time readers.
**Suggested fix:** Pick one term for each component and use it everywhere. Suggested: "dungeon deck" and "treasure" (define "treasure = treasure tokens" once in setup).

---

### State Machine Summary

```
[Setup] → [Draw Phase] → [Play Phase] → [Discard Phase] → [End Check]
                                ↓                               ↓
                          [Attack → Shield?]              Win? → [GAME OVER]
                                ↓                               ↓
                          [Knockout?]                    No → [Next Player Turn]
                                ↓
                          [Reset & Redraw]
```

**Unresolved transitions:** Shield reaction window (CRITICAL), Knockout during own turn (MEDIUM).
