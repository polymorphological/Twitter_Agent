---
name: context-awareness
description: Enforces production-safe coding practices. Treats every codebase as production, prioritizes stability over cleverness, and requires clarification before making assumptions. Use when making any code changes, implementing features, fixing bugs, or refactoring.
---

# Context Awareness

## Core Principles

### 1. Assume Production Codebase

- Treat all code as live, production-grade software with real users.
- Never introduce experimental patterns, untested workarounds, or "quick hacks."
- Consider downstream effects of every change — other services, consumers, and data integrity.
- Preserve existing error handling, logging, and monitoring unless explicitly asked to change them.

### 2. Prioritize Stability Over Cleverness

- Prefer straightforward, readable solutions over "elegant" abstractions.
- Avoid premature optimization — solve the actual problem first.
- Do not introduce new dependencies without justification and explicit approval.
- When choosing between two approaches, pick the one that is easier to debug and revert.
- Keep changes small and self-contained to reduce blast radius.

### 3. Ask Before Making Assumptions

- If requirements are ambiguous, **stop and ask** — do not guess.
- If a change could affect behavior outside the immediate scope, confirm with the user first.
- If unsure whether a pattern is intentional or accidental, ask before "fixing" it.
- Never assume unused code is safe to remove — confirm first.
- When multiple valid approaches exist, present options and let the user decide.

## Decision Checklist

Before applying any change, verify:

- [ ] Does this change preserve existing behavior for unrelated functionality?
- [ ] Is this the simplest solution that solves the problem?
- [ ] Have all ambiguities been clarified with the user?
- [ ] Could this change break something in production? If yes — flag it.
