# Defect Severity Classification

Use this reference to classify each defect found during analysis.

## Severity Levels

### CRITICAL — Game-Breaking

The game cannot proceed or produces a logically impossible state.

**Indicators:**
- Game enters an unrecoverable state (no legal moves for any player)
- Infinite loop with no exit condition
- Required component doesn't exist
- Contradictory mandatory rules (must do X AND must not do X simultaneously)
- Game literally cannot end

**Action required:** Must fix before the game is playable.

**Example:** "Each player must play a card on their turn" + "If your hand is empty, do nothing on your turn" + no way to draw cards after setup hand is exhausted = game halts.

### HIGH — Major Logical Flaw

The game can technically continue, but a significant rule interaction is broken or exploitable.

**Indicators:**
- Infinite resource generation loop
- Dominant strategy that trivializes the game
- Rules can be interpreted two contradictory ways, leading to different game outcomes
- A game mechanism never triggers despite being prominently featured
- End condition is theoretically unreachable in common scenarios

**Action required:** Must fix for a fair and functional game.

**Example:** Scoring says "gain 1 VP per territory controlled at end of round" but territory control is only checked at end of game. Rounds don't technically have a "territory check" — so do you score per round or not?

### MEDIUM — Ambiguity or Gap

Rules leave a situation unresolved. Players must house-rule or guess.

**Indicators:**
- Missing edge case handling (playable but awkward when encountered)
- Timing ambiguity that rarely matters but could matter
- Undefined interaction between two specific cards/abilities
- Vague language open to reasonable but different interpretations
- Component shortage possible in uncommon but plausible scenarios

**Action required:** Should fix before publishing. Playtesters can work around it.

**Example:** "Discard a card to gain its value in gold." No definition of "value" — is it the printed cost, the VP value, or something else?

### LOW — Minor Clarity Issue

The intent is clear to most readers, but the wording could be tighter.

**Indicators:**
- Redundant rules that could confuse (same rule stated differently in two places)
- Inconsistent terminology (sometimes "discard pile" sometimes "graveyard")
- Unnecessary forward references that make first read harder
- Implicit assumptions that experienced gamers will fill in but new players may not

**Action required:** Polish before publication. Not a functional issue.

**Example:** Setup says "each player takes a player board" but the component list calls them "faction mats."

## Report Format

For each defect, provide:

```
### [SEVERITY] [Short title]

**Category:** [Which of the 10 analysis categories]
**Location:** [Section/page/rule reference in the source document]
**The problem:** [Clear description of the defect]
**Why it matters:** [What goes wrong during play]
**Suggested fix:** [Concrete recommendation — one or more options]
```

Group defects by severity (CRITICAL first), then by category within each severity level.
