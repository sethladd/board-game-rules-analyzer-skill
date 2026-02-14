# Analysis Categories

Detailed descriptions of each analysis pass. Read this file when performing an analysis.

## Table of Contents

1. State Reachability
2. Action Legality
3. Resource Integrity
4. Turn Structure Consistency
5. Termination Guarantees
6. Trigger & Timing Conflicts
7. Information Consistency
8. Component Arithmetic
9. Edge Case Coverage
10. Cross-Rule Contradiction

---

## 1. State Reachability

Verify every game state the rules describe can actually be reached through legal play.

**Check for:**
- Setup produces a valid starting state (all required components placed, starting resources distributed)
- Every named phase, zone, or status is reachable from the starting state through some sequence of legal actions
- No "orphan states" exist (states referenced by rules but unreachable through play)
- No "trap states" exist (states reachable but with no legal exit, unless they are intentional end-game states)

**Example defect:** Rules say "When a player's influence reaches 0, they enter Exile." But no rule ever reduces influence below 1, making Exile unreachable.

**Example defect:** A card says "Move to the Shadow Realm" but no rule defines what happens in the Shadow Realm or how to leave it.

## 2. Action Legality

Verify every action a player can take is fully defined: preconditions, execution, and postconditions.

**Check for:**
- Every action has clear preconditions (when can you do it?)
- Every action has clear execution steps (what happens?)
- Every action has clear postconditions (what state results?)
- No action references undefined components, zones, or statuses
- No action has contradictory preconditions (e.g., "must have 0 gold" AND "spend 1 gold")
- Optional vs. mandatory actions are unambiguous ("may" vs. "must")
- The action space is closed: if the rules don't say you can do it, you can't

**Example defect:** "Discard a card to move 2 spaces." What if your hand is empty? Is this action still listed as available? Can you choose it and fail?

## 3. Resource Integrity

Verify all resources (tokens, cards, currency, health, etc.) are conserved correctly and can never reach impossible values.

**Check for:**
- Resources have defined bounds (minimum, maximum, or both)
- Every source and sink of each resource is accounted for
- No rule creates resources from nothing unintentionally (infinite resource loops)
- No rule destroys resources unintentionally (resource leaks)
- Supply limits are handled: what happens when tokens run out?
- Fractional/impossible resource values can't occur (e.g., "lose half your gold" when gold is odd)

**Example defect:** Players gain 2 gold per turn and can spend gold, but no rule caps gold. Token supply is 30 gold total for 4 players. Rules don't say what happens when the supply runs out.

## 4. Turn Structure Consistency

Verify the turn/round/phase structure is complete and unambiguous.

**Check for:**
- Turn order is fully defined (clockwise, variable, simultaneous, etc.)
- Every phase of a turn is defined with clear start/end boundaries
- Phases are ordered: no ambiguity about which phase comes next
- Skip conditions are defined (what if a player can't do anything in a phase?)
- No infinite sub-loops within a turn (action triggers action triggers action...)
- Player count edge cases (2-player, maximum player count, solo)

**Example defect:** "Draw Phase → Action Phase → Cleanup Phase." A card says "Take an extra Action Phase after your Cleanup Phase." Does this mean Draw → Action → Cleanup → Action → Cleanup? Or Draw → Action → Cleanup → Action? Does the extra Action Phase have its own Cleanup?

## 5. Termination Guarantees

Verify the game always ends in finite time.

**Check for:**
- At least one end condition exists
- End conditions are reachable from any mid-game state (the game can't stall)
- No stable equilibrium where no player can make progress toward any end condition
- Tiebreaker rules are defined for all plausible tie scenarios
- Tiebreakers are themselves deterministic (no infinite tiebreaker chains)
- Round limits or forced escalation prevent infinite stalling

**Example defect:** Game ends "when one player controls all territories." But two evenly matched players can trade territories back and forth indefinitely with no forced resolution.

## 6. Trigger & Timing Conflicts

Verify triggered effects (reactions, interrupts, "when..." clauses) have clear resolution order.

**Check for:**
- Simultaneous trigger resolution order is defined (priority system, player choice, active player first, etc.)
- Nested triggers are handled (trigger A causes trigger B — which resolves first?)
- Infinite trigger loops are impossible or have explicit break conditions
- "Replacement effects" (effects that modify other effects) have clear precedence
- "When" vs. "after" vs. "instead" timing is consistent
- Interrupt/response windows are defined (can you react to a reaction?)

**Example defect:** Card A: "When you take damage, deal 1 damage to the attacker." Card B: "When you take damage, deal 1 damage to the attacker." If Player 1 attacks Player 2, and both have these cards, an infinite damage loop occurs.

## 7. Information Consistency

Verify the information model (public, private, hidden) is consistent and doesn't create paradoxes.

**Check for:**
- Every game element has a defined visibility (public, private, hidden from all)
- Rules don't require players to act on information they can't access
- Hidden information can't be trivially deduced from public information (unless intended)
- Reveal conditions are defined (when does hidden information become public?)
- Memory requirements are reasonable (players shouldn't need to memorize extensive hidden state)

**Example defect:** "Choose an opponent's hidden card and discard it." How does the choosing player select a specific card they can't see? Is it random? Do they pick by position? The rules don't specify.

## 8. Component Arithmetic

Verify the physical components support all possible game states.

**Check for:**
- Token/card counts are sufficient for worst-case scenarios
- Deck size supports all draw requirements (what if the deck runs out?)
- Board spaces are sufficient for all pieces that could occupy them
- Stacking/overlap rules are defined for shared spaces
- Shuffle/reshuffle triggers are defined for depleted decks
- Component degradation is handled (damaged cards, lost tokens — optional but useful)

**Example defect:** The game includes 20 damage tokens. Maximum theoretical damage across all players is 48. Rules don't say what happens when damage tokens run out.

## 9. Edge Case Coverage

Verify rules handle boundary conditions and unusual-but-legal player behaviors.

**Check for:**
- Zero-quantity actions ("gain 0 gold" — does the gain trigger still fire?)
- Empty-hand/empty-deck conditions during draw effects
- First turn and last turn edge cases
- Self-targeting (can you target yourself with harmful effects?)
- Minimum/maximum player counts (especially 2-player and max-player)
- "Nothing happens" outcomes (you play a card but its effect is irrelevant)
- Voluntary vs. forced actions at boundaries (must you take a harmful action if it's the only option?)

**Example defect:** "At the start of your turn, you must pay 1 gold or lose 1 health." What if you have 0 gold AND 0 health? Do you die? Can you pay 0 gold (does that count as paying)? Is the game over?

## 10. Cross-Rule Contradiction

Verify no two rules directly contradict each other.

**Check for:**
- General rules vs. specific rules: is the override order clear? ("Specific beats general" stated?)
- Keyword definitions vs. card text: does any card contradict the keyword's definition?
- Setup rules vs. gameplay rules: does the setup state satisfy all gameplay invariants?
- Different sections saying different things about the same mechanic
- Numeric contradictions (one section says "3 gold", another says "2 gold" for the same action)
- Temporal contradictions ("do X before Y" in one place, "do Y before X" in another)

**Example defect:** Core rules: "You may hold a maximum of 7 cards." Card effect: "Draw cards until you have 10 in hand." Which rule wins? Is the card effect an exception? This must be stated explicitly.
