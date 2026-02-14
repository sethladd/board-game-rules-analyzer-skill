---
name: board-game-logic-analyzer
description: >
  Perform critical logic analysis on board game rules to find inconsistencies, impossible states,
  broken flows, and exploitable loopholes. Use when a user provides board game rules (in any format:
  text, document, PDF, pasted rules, or rulebook) and wants them checked for logical soundness.
  Triggers on requests involving: analyzing board game rules for errors, finding rule inconsistencies,
  checking game logic, verifying rule completeness, finding broken interactions, stress-testing rules,
  auditing a rulebook, finding edge cases in game rules, or any request to validate that a board game's
  rules are internally consistent and produce correct game states. Also triggers when a user asks
  "does this game work?" or "are there holes in these rules?" or "what's broken in my rules?"
---

# Board Game Logic Analyzer

Systematically analyze board game rules for logical defects: contradictions, unreachable states, undefined interactions, infinite loops, missing edge cases, and exploitable loopholes.

This is a *critical logic analysis* — treat the rules as a formal system and stress-test them like a program. The goal is to find every way the game can break, stall, or produce ambiguous outcomes.

## Workflow

Analyzing a rulebook involves these steps:

1. **Ingest rules** — Read the complete rules document
2. **Build the mental model** — Identify components, actions, states, transitions, and win conditions
3. **Run 10 analysis passes** — Check each category from [analysis-categories.md](references/analysis-categories.md)
4. **Classify defects** — Assign severity per [defect-severity.md](references/defect-severity.md)
5. **Produce the report** — Output a structured analysis with a summary, defect list, and state machine sketch

Before beginning, read these references:

- **[references/analysis-categories.md](references/analysis-categories.md)** — The 10 analysis categories and what to check in each
- **[references/defect-severity.md](references/defect-severity.md)** — Severity levels and the report format for each defect
- **[references/example-analysis.md](references/example-analysis.md)** — A complete worked example for calibration on depth, tone, and format

## Step 1: Ingest Rules

Accept rules in any form: pasted text, uploaded PDF, DOCX, images of rulebook pages, or markdown. If the rules are in a file, read the file. If the rules span multiple files or pages, read them all before starting analysis.

If the rules are incomplete (e.g., missing setup, missing end condition), note this explicitly. Analyze what exists and flag the gaps.

## Step 2: Build the Mental Model

Before running analysis passes, extract and organize:

- **Components:** All physical/digital pieces (cards, tokens, boards, dice, tracks, etc.)
- **Players:** Count range, asymmetry, roles
- **Resources:** Anything gained, spent, tracked (health, gold, VP, cards in hand, etc.)
- **Zones:** Named locations where components exist (hand, deck, discard, board spaces, etc.)
- **Actions:** Everything a player can do, with preconditions
- **Phases/turns:** The sequence structure of play
- **Triggers:** "When..." and "If..." clauses that fire conditionally
- **Win/end conditions:** How the game ends and who wins

Do not include this model in the output report — it is internal scaffolding. But use it to power every subsequent analysis pass.

## Step 3: Run Analysis Passes

Run all 10 passes from [analysis-categories.md](references/analysis-categories.md):

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

For each pass, actively try to break the game. Think adversarially: "If I were a rules lawyer trying to exploit this, what would I argue?" and "If I were a confused new player, what would I misunderstand?"

**Do not skip passes.** Even if a category seems irrelevant, confirm it and move on. Many defects hide in categories that seem inapplicable at first glance.

## Step 4: Classify and Report

Use the severity levels and report format from [defect-severity.md](references/defect-severity.md).

### Report Structure

```
# Rules Logic Analysis: [Game Name]

## Summary
[1-2 paragraphs: game overview, overall assessment, defect counts by severity.
State whether the game is playable as written, playable with house rules, or unplayable.]

## Defects

### Critical
[Defects that break the game. Use the format from defect-severity.md.]

### High
[Major logical flaws.]

### Medium
[Ambiguities and gaps.]

### Low
[Clarity issues.]

## State Machine
[ASCII diagram showing the turn/phase/game flow with
annotations for where defects occur. Mark unresolved
transitions.]

## Clean Passes
[List any of the 10 categories that found no defects.
One line each with brief reasoning.]
```

If no defects are found at a given severity level, omit that subsection.

### Tone

Be precise, direct, and constructive. Every defect must include a concrete suggested fix — never just point out a problem without offering a solution. Fixes should be worded as draft rule text the designer could adopt or adapt.

Assume the designer is competent and wants honest feedback. Don't soften findings. A critical defect is critical.

## Special Cases

**Partial rules:** If the user provides only part of a game (e.g., just the combat system), analyze what's provided. Flag assumptions about the missing context. Run all 10 passes but note which passes can't be fully evaluated.

**Rules + questions:** If the user asks specific questions about their rules ("Does this combo work?", "Can a player get stuck here?"), answer those questions directly first, then offer the full analysis.

**Comparison:** If the user provides two versions of the rules, diff them and analyze whether the changes introduce or resolve defects.

**Live iteration:** After delivering the report, be ready for follow-up. The designer will likely propose fixes — re-analyze the patched rules and confirm whether the fix resolves the defect without introducing new ones.
