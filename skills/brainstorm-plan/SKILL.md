---
name: brainstorm-plan
description: Use when the user wants to turn a new feature, behavior change, app, tool, plugin, or broad implementation idea into a final PlanForge implementation plan through a Superpowers-style brainstorming flow.
---

# Brainstorm Plan

Use this skill to turn ideas into fully formed PlanForge implementation plans through natural collaborative dialogue.

This skill preserves the Superpowers brainstorming behavior. The simplification is in the output: instead of writing a separate design doc and then invoking a separate writing-plans skill, PlanForge writes one final plan that contains both spec/design and implementation guidance.

```text
Superpowers: brainstorming -> design doc -> writing-plans -> implementation plan
PlanForge:   brainstorming -> final plan with spec/design + implementation guidance
```

<HARD-GATE>
Do NOT implement code, scaffold files, edit project code, or invoke execution until you have presented the design, received approval, and written the final PlanForge plan. This applies even when the task looks simple.
</HARD-GATE>

## Anti-Pattern: "This Is Too Simple To Need Brainstorming"

Every new feature, behavior change, tool, app, plugin, or broad implementation idea goes through this process. Small changes can have short designs, but they still need enough questioning to expose assumptions, constraints, and success criteria.

## Checklist

Complete these items in order:

1. **Explore project context** - check relevant files, docs, existing plans, and recent Git state when available.
2. **Assess scope** - if the request spans independent subsystems, decompose before planning.
3. **Ask clarifying questions** - one at a time, until purpose, constraints, success criteria, and boundaries are clear.
4. **Propose 2-3 approaches** - include trade-offs and a recommendation when more than one path is plausible.
5. **Present design** - section by section, scaled to complexity, and get user approval.
6. **Write final PlanForge plan** - save to `docs/planforge/plans/YYYY-MM-DD-topic.md`.
7. **Self-review the plan** - check placeholders, contradictions, scope, ambiguity, and execution compatibility.
8. **User review gate** - ask the user to review the written plan before execution.
9. **Execution handoff** - ask whether to execute now, and if so route to `execute-plan`.

## Understanding The Idea

- Inspect the current project first. Prefer concrete context over assumptions.
- If the project is too large for one plan, help decompose it into sub-projects and plan the first useful slice.
- Ask one question per message.
- Prefer multiple-choice questions when that makes answering easier; open-ended is fine when needed.
- Keep asking until you understand the user's purpose, constraints, success criteria, target users, existing system boundaries, and what would count as failure.
- Do not stop after one or two questions just because you can imagine an implementation.

## Exploring Approaches

- Propose 2-3 approaches when there is a real design choice.
- Lead with your recommendation and explain why.
- Include trade-offs in terms of complexity, risk, user experience, maintainability, and verification.
- If only one approach is sensible, say why and keep the design concise.

## Presenting The Design

When you understand what is being built, present the design in sections. Scale each section to the work.

Cover what matters for the task:

- behavior and user-visible outcome;
- architecture and components;
- data flow or state flow;
- integration points;
- error handling;
- testing and verification;
- rollout or migration concerns when relevant.

Ask whether each section looks right before moving on when the design is non-trivial. Be ready to revise when the user corrects the direction.

## Design For Isolation And Clarity

Break the system into small units with clear responsibilities and interfaces. For each unit, the plan should make clear:

- what it does;
- how it is used;
- what it depends on;
- how it can be tested.

Prefer boundaries that let an executing agent work task-by-task without holding the whole codebase in context.

## Existing Codebases

- Follow existing patterns before inventing new ones.
- If existing structure is tangled and affects the work, include targeted improvements in the plan.
- Do not propose unrelated refactors.
- If the user already approved prior design details in the conversation, do not restart the brainstorm; continue from that approval.

## Final Plan

Use `skills/_shared/plan-format.md`.

The plan must contain:

- spec/design;
- implementation tasks;
- files to inspect and likely files to modify;
- boundaries and non-goals;
- verification;
- final review instructions;
- risks and concerns;
- no automatic commit steps.

Unlike Superpowers `writing-plans`, PlanForge plans should not duplicate large file contents. Include exact snippets only when the exact text is essential. The plan should be robust enough to guide an agent, but implementation belongs to `execute-plan`.

## Plan Self-Review

Before handing the plan to the user:

1. Scan for placeholders such as TBD, TODO, "fill in", or vague requirements.
2. Check internal consistency between design and tasks.
3. Check scope: one coherent implementation plan, or decomposed into smaller plans.
4. Check ambiguity: pick a clear interpretation or ask the user.
5. Check execution compatibility with `execute-plan`.

Fix issues inline before presenting the plan path.

## User Review Gate

After writing the plan, ask the user to review it before execution. If they request changes, update the plan and self-review again.

Only after approval ask whether to execute now.

If the request is a bug, regression, failing test, correction, or localized update, use `brainstorm-fix` instead.
