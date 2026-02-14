# Board Game Logic Analyzer

A [Claude Code skill](https://docs.anthropic.com/en/docs/claude-code/skills) that performs critical logic analysis on board game rules. It finds inconsistencies, impossible states, broken flows, and exploitable loopholes in your rulebook.

## What It Does

Give it your board game rules (text, PDF, images, or any format) and it will systematically stress-test them across 10 analysis categories:

1. **State Reachability** — Can every game state actually be reached?
2. **Action Legality** — Are all actions well-defined with clear preconditions?
3. **Resource Integrity** — Do resources balance? Can they go negative or overflow?
4. **Turn Structure Consistency** — Is the turn/phase sequence unambiguous?
5. **Termination Guarantees** — Does the game always end?
6. **Trigger & Timing Conflicts** — Do simultaneous effects resolve cleanly?
7. **Information Consistency** — Is hidden/public info handled correctly?
8. **Component Arithmetic** — Do the physical pieces add up?
9. **Edge Case Coverage** — What happens at player-count extremes, empty decks, etc.?
10. **Cross-Rule Contradiction** — Do any rules conflict with each other?

Every defect found is classified by severity (Critical, High, Medium, Low) and includes a concrete suggested fix with draft rule text.

## Installation

Copy `SKILL.md` and the `references/` directory into your project's `.claude/skills/` directory, or install it from the skill registry.

## Usage

Provide your board game rules to Claude Code and ask it to analyze them:

- "Analyze these rules for logical issues"
- "Does this game work?"
- "Are there holes in these rules?"
- "Find edge cases in my combat system"

## License

BSD 3-Clause License. See [LICENSE](LICENSE) for details.
