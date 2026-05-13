---
name: brainstorm-fix
description: Use when the user reports a bug, regression, failing test, correction, or localized update and wants a focused PlanForge fix plan before implementation.
---

# Brainstorm Fix

Use this skill for focused fixes.

This is still a planning skill. Always generate a final `.md` plan. Do not directly fix code during this skill unless the user stops planning and explicitly asks for implementation.

## Workflow

1. Treat the user's report as the main problem statement.
2. Inspect relevant code, tests, logs, and errors first.
3. Ask questions only when blocked or when intended behavior is ambiguous.
4. Produce a concise diagnosis.
5. Write one final fix plan to `docs/planforge/plans/YYYY-MM-DD-topic.md`.
6. Ask whether the user wants to execute the plan now.

## Plan Requirements

Use `skills/_shared/plan-format.md`.

The fix plan must include:

- reported problem;
- observed or suspected cause;
- affected files;
- correction strategy;
- verification or regression test strategy;
- risks;
- small implementation tasks;
- no automatic commit steps.

## Routing

If the request is actually a large feature or open-ended redesign, switch to `brainstorm-plan`.
