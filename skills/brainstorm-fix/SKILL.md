---
name: brainstorm-fix
description: Use when the user reports a bug, regression, failing test, correction, or localized update and wants a focused PlanForge fix plan before implementation.
---

# Brainstorm Fix

Use this skill for focused fixes. It is a PlanForge variant of Superpowers brainstorming for problems where the user has likely already described the failure.

This is still a planning skill. Always generate a final `.md` plan. Do not directly fix code during this skill unless the user stops planning and explicitly asks for implementation.

## Hard Gate

Do not edit code during `brainstorm-fix`. First diagnose, clarify only what is needed, present the fix design, and write the final PlanForge fix plan.

## Workflow

1. **Read the report carefully** - capture the symptom, expected behavior, actual behavior, and any user-provided logs.
2. **Inspect project context first** - read relevant code, tests, recent errors, docs, and existing plans before asking broad questions.
3. **Diagnose before planning** - identify the most likely cause, affected boundaries, and uncertainty.
4. **Ask focused questions only when needed** - one at a time, only for blockers or ambiguous intended behavior.
5. **Present the fix design** - explain the cause, correction strategy, verification, and risk.
6. **Write final fix plan** - save to `docs/planforge/plans/YYYY-MM-DD-topic.md`.
7. **Self-review the plan** - check cause/strategy/test alignment and ambiguity.
8. **Ask whether to execute now** - route to `execute-plan` if approved.

## Investigation Rules

- Prefer evidence from code, tests, logs, and reproduction steps over guesses.
- If the symptom cannot be reproduced or confirmed, say what evidence is missing.
- If multiple causes are plausible, list them and narrow with targeted inspection or one question.
- If the request is actually a large feature or redesign, route to `brainstorm-plan`.

## Questions

Ask fewer questions than `brainstorm-plan`, but do not skip necessary clarification.

Good fix questions are about:

- expected behavior;
- reproduction steps;
- affected environment;
- acceptable compatibility trade-offs;
- whether a visible behavior change is intended.

Do not ask broad product-design questions unless the fix has become a feature.

## Fix Plan Requirements

Use `skills/_shared/plan-format.md`.

The fix plan must include:

- reported problem;
- expected vs actual behavior;
- diagnosis or suspected cause;
- evidence inspected;
- affected files and boundaries;
- correction strategy;
- regression test or verification strategy;
- risks and rollback concerns;
- small implementation tasks;
- no automatic commit steps.

## Plan Self-Review

Before handing the plan to the user:

1. Check that the diagnosis supports the proposed fix.
2. Check that verification would catch the original problem.
3. Check that the plan does not overbuild beyond the report.
4. Check for placeholders, contradictions, and ambiguous expected behavior.

Fix issues inline before presenting the plan path.
