# PlanForge Plan Format

PlanForge planning skills write final implementation plans to:

```text
docs/planforge/plans/YYYY-MM-DD-topic.md
```

One final plan file is the default. It contains both spec/design and implementation guidance.

## Required Header

Every plan starts with:

```markdown
# [Plan Name]

> **For agentic workers:** This plan is the source of truth. Execute task-by-task using subagent mode or inline mode. Do not create commits. Ask once before creating a branch during execution. Keep checklist status updated.

**Mode:** brainstorm-plan | brainstorm-fix | rework-plan
**Goal:** [One sentence]
**Architecture:** [Short implementation approach]
**Tech Stack:** [Project technologies]
```

## Required Sections

Include these sections:

- Product/spec summary
- Design decisions
- Boundaries and non-goals
- Implementation tasks
- Verification checklist
- Final review instructions
- Risks and concerns

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

## Writing Rules

- Explain intent, decisions, constraints, and verification clearly.
- Do not prewrite large file contents unless exact text is essential.
- Do not include automatic commit steps.
- Keep tasks small enough for either a subagent or the inline executor.
- Write plans for agents and humans: clear, concrete, and bounded.
