# PlanForge Plan Format

PlanForge planning skills write final implementation plans to:

```text
docs/planforge/plans/YYYY-MM-DD-topic.md
```

One final plan file is the default. It contains both spec/design and implementation guidance.

## Required Header

Every plan starts with:

```markdown
# [Plan Name] Implementation Plan

> **For agentic workers:** This plan is the source of truth. Execute task-by-task using PlanForge `execute-plan` in subagent mode or inline mode. Do not create commits. Ask once before creating a branch during execution. Do not use the `codex/` branch prefix unless the user explicitly asks for it. Keep checklist status updated.

**Mode:** brainstorm-plan | brainstorm-fix | rework-plan
**Goal:** [One sentence]
**Architecture:** [2-3 sentences about the approach]
**Tech Stack:** [Project technologies]

---
```

## Required Sections

Include these sections:

- Product/spec summary
- Design decisions
- Architecture and components
- Data or state flow when relevant
- Error handling
- Boundaries and non-goals
- Implementation tasks
- Verification checklist
- Final review instructions
- Risks and concerns

## File Structure Section

Before task decomposition, map expected files:

```markdown
## File Structure

- Create: `path/to/file` - responsibility.
- Modify: `path/to/file` - responsibility.
- Inspect: `path/to/file` - why it matters.
```

Use likely files when exact paths require implementation-time discovery.

## Task Shape

Each task uses this structure:

```markdown
### Task N: Task Name

**Purpose:** Why this task exists.

**Context for agent:** What the implementer needs to know before editing.

**Files to inspect:**
- `path/to/file`

**Likely files to modify:**
- `path/to/file`

**Boundaries:**
- What not to change.
- What existing behavior must be preserved.

**Expected result:**
- Observable result of the task.

**Verification:**
- Command or manual check.
- Expected passing signal.

**Review notes:**
- What the orchestrator should inspect after the task.

**Status contract:** The implementer must report `DONE`, `DONE_WITH_CONCERNS`, `NEEDS_CONTEXT`, or `BLOCKED`.

- [ ] Step 1: ...
- [ ] Step 2: ...
- [ ] Step 3: ...
```

The task shape above is a template only. Final plans must replace every placeholder and ellipsis with concrete content.

## Writing Rules

- Explain intent, decisions, constraints, and verification clearly.
- Do not prewrite large file contents unless exact text is essential.
- Do not include automatic commit steps.
- Keep tasks small enough for either a subagent or the inline executor.
- Prefer TDD or regression-test-first task ordering when practical.
- Include exact commands and expected results when known.
- If commands are unknown, instruct the executor to discover project-standard checks before completion.
- Never leave `TBD`, `TODO`, `...`, "fill in", "implement later", "add appropriate handling", or similar placeholder text in a final plan.
- Do not write "similar to Task N"; repeat the relevant detail so tasks can be executed independently.
- Do not write vague steps such as "write tests" without saying what behavior should be tested.
- Write plans for agents and humans: clear, concrete, and bounded.

## Self-Review Rules

Before presenting a plan:

- Check spec coverage.
- Scan for placeholders.
- Check type and naming consistency.
- Check execution order.
- Check verification coverage.
- Check compatibility with `execute-plan`.
