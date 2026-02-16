---
name: mvp-mindset
description: Enforces MVP-first development approach. Prefer simple solutions, avoid unnecessary abstractions, and optimize for speed of delivery. Use when making any code changes, implementing features, fixing bugs, or refactoring.
---

# MVP Mindset

## Core Principles

### 1. Prefer Simple Solutions

- Choose the most straightforward implementation that satisfies the requirement.
- Use built-in language features and existing project utilities before reaching for libraries.
- Write flat, linear code over deeply nested or layered structures.
- If a solution feels complex, step back and ask: "What is the simplest thing that could work?"

### 2. Avoid Abstractions Unless Necessary

- Do not create interfaces, base classes, or wrappers until there is a clear, immediate need.
- Do not generalize for hypothetical future use cases — solve today's problem.
- Inline logic is acceptable when it is used in only one place.
- Extract a function or module only when duplication actually exists (rule of three).
- Avoid design patterns for their own sake — patterns should reduce complexity, not add it.

### 3. Optimize for Speed of Delivery

- Favor working code now over perfect code later.
- Skip gold-plating: if a feature works and meets the requirement, move on.
- Use hardcoded values or simple config when a dynamic system is overkill.
- Prefer fewer files over many small files — reduce indirection.
- When choosing between two approaches of similar quality, pick the faster one to implement.

## Decision Checklist

Before applying any change, verify:

- [ ] Is this the simplest implementation that meets the requirement?
- [ ] Am I introducing an abstraction? If so — is there a concrete, immediate reason?
- [ ] Could this be done with less code, fewer files, or fewer moving parts?
- [ ] Am I solving a real problem or a hypothetical one?
