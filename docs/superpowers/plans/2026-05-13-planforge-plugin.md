# PlanForge Plugin Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking. Do not create commits while implementing this plan unless the human explicitly asks.

**Goal:** Create the first version of the PlanForge Codex plugin from the approved design spec.

**Architecture:** PlanForge is a small Codex plugin with four user-facing skills and three shared reference files. The skills follow Superpowers patterns for planning, execution, subagent dispatch, review, and verification, but encode the PlanForge simplifications: one final plan file by default, no automatic commits, optional branch creation only at execution start, orchestrator task review, and mandatory final review by a separate agent.

**Tech Stack:** Codex plugin manifest JSON, Codex `SKILL.md` files, Markdown shared references, PowerShell-compatible validation commands.

---

## File Structure

Create these files:

- `.codex-plugin/plugin.json`  
  Codex plugin manifest for `planforge`.

- `skills/_shared/plan-format.md`  
  Shared final-plan format used by planning and execution skills.

- `skills/_shared/subagent-contract.md`  
  Shared subagent dispatch and status contract adapted from Superpowers.

- `skills/_shared/final-review.md`  
  Shared mandatory final review workflow.

- `skills/brainstorm-plan/SKILL.md`  
  New feature / broad idea planning skill.

- `skills/brainstorm-fix/SKILL.md`  
  Focused bug/update planning skill.

- `skills/rework-plan/SKILL.md`  
  Existing plan refinement skill.

- `skills/execute-plan/SKILL.md`  
  Plan execution skill with subagent and inline modes.

Modify no existing source files except by creating the plugin files above.

## Task 1: Create Plugin Manifest

**Files:**

- Create: `.codex-plugin/plugin.json`

- [ ] **Step 1: Create the manifest directory and file**

Create `.codex-plugin/plugin.json` with this content:

```json
{
  "name": "planforge",
  "version": "0.1.0",
  "description": "A lightweight Superpowers-inspired planning and execution workflow for Codex.",
  "author": {
    "name": "PlanForge",
    "email": "planforge@example.com",
    "url": "https://github.com/"
  },
  "homepage": "https://github.com/",
  "repository": "https://github.com/",
  "license": "MIT",
  "keywords": [
    "planning",
    "brainstorming",
    "subagents",
    "code-review",
    "codex",
    "skills"
  ],
  "skills": "./skills/",
  "interface": {
    "displayName": "PlanForge",
    "shortDescription": "Lean planning and execution workflows for Codex",
    "longDescription": "PlanForge turns ideas, fixes, and existing plans into practical agent-readable implementation plans, then executes them inline or with subagents using a lighter Superpowers-inspired workflow.",
    "developerName": "PlanForge",
    "category": "Coding",
    "capabilities": [
      "Interactive",
      "Read",
      "Write"
    ],
    "defaultPrompt": [
      "Brainstorm this idea into a PlanForge plan.",
      "Create a brainstorm-fix plan for this bug.",
      "Execute this PlanForge plan."
    ],
    "brandColor": "#2563EB",
    "screenshots": []
  }
}
```

- [ ] **Step 2: Validate JSON**

Run:

```powershell
Get-Content .codex-plugin\plugin.json | ConvertFrom-Json | Out-Null
```

Expected: command exits successfully with no output.

## Task 2: Create Shared Plan Format Reference

**Files:**

- Create: `skills/_shared/plan-format.md`

- [ ] **Step 1: Create shared directory**

Create `skills/_shared/`.

- [ ] **Step 2: Write `plan-format.md`**

Create `skills/_shared/plan-format.md` with this content:

````markdown
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
````

- [ ] **Step 3: Review the reference**

Run:

```powershell
Get-Content skills\_shared\plan-format.md
```

Expected: file contains the required path, header, task shape, and no automatic commit instruction.

## Task 3: Create Shared Subagent and Final Review References

**Files:**

- Create: `skills/_shared/subagent-contract.md`
- Create: `skills/_shared/final-review.md`

- [ ] **Step 1: Write `subagent-contract.md`**

Create `skills/_shared/subagent-contract.md` with this content:

````markdown
# PlanForge Subagent Contract

Use this reference when executing a PlanForge plan in subagent mode.

PlanForge keeps the Superpowers dispatch mechanics because they are reliable:

- The orchestrator reads the plan.
- The orchestrator extracts the current task and shared context.
- The orchestrator gives the implementer subagent the full task text and only the needed context.
- The implementer should not have to read the whole plan independently.
- The implementer reports a clear status.

## Implementer Prompt Requirements

Every implementer dispatch includes:

- plan name and goal;
- current task text;
- relevant design decisions;
- relevant boundaries;
- files to inspect;
- likely files to modify;
- verification expected for the task;
- Git policy: no commits, pushes, or PRs;
- status contract.

## Status Contract

Implementers must finish with one of:

- `DONE`: task completed and verified.
- `DONE_WITH_CONCERNS`: task completed, but there are concerns the orchestrator must read.
- `NEEDS_CONTEXT`: task cannot continue without more information.
- `BLOCKED`: task cannot be completed with the current plan or context.

## Orchestrator Handling

If status is `DONE`, review locally and continue if the task meets the plan.

If status is `DONE_WITH_CONCERNS`, read concerns before continuing. Fix small issues directly or send a short corrective follow-up.

If status is `NEEDS_CONTEXT`, provide missing context and redispatch.

If status is `BLOCKED`, choose one:

- provide better context;
- split the task smaller;
- use a stronger model if available;
- stop and ask the user if the blocker is genuinely ambiguous.

## Per-Task Review

PlanForge does not call separate formal review agents after every task by default.

The orchestrator reviews each task for:

- plan compliance;
- scope control;
- changed files;
- verification result;
- obvious code quality problems;
- no automatic commits.
````

- [ ] **Step 2: Write `final-review.md`**

Create `skills/_shared/final-review.md` with this content:

````markdown
# PlanForge Final Review

Final review is mandatory for both subagent mode and inline mode.

The final reviewer must be a separate agent focused only on review. The reviewer does not implement fixes.

## Reviewer Context

Provide the reviewer:

- the plan path and relevant plan text;
- implementation summary;
- changed file list;
- relevant diff;
- verification commands and results;
- known concerns or skipped checks.

## Review Scope

The reviewer checks:

- plan compliance;
- missing requirements;
- regressions;
- scope drift or overbuilding;
- tests and verification gaps;
- code quality risks;
- Git policy compliance.

## Reviewer Output

The reviewer should return findings ordered by severity:

- Critical: must fix before completion.
- Important: should fix before completion.
- Minor: optional cleanup or follow-up.

If there are no findings, the reviewer should say that clearly and mention residual risk.

## After Review

The orchestrator fixes valid Critical and Important findings, decides whether Minor findings belong in scope, then reruns fresh verification before claiming completion.
````

- [ ] **Step 3: Check shared references**

Run:

```powershell
rg -n "commit|final review|DONE_WITH_CONCERNS|BLOCKED" skills\_shared
```

Expected: output shows the no-commit policy, final review guidance, and status contract.

## Task 4: Create `brainstorm-plan` Skill

**Files:**

- Create: `skills/brainstorm-plan/SKILL.md`

- [ ] **Step 1: Create skill directory**

Create `skills/brainstorm-plan/`.

- [ ] **Step 2: Write `SKILL.md`**

Create `skills/brainstorm-plan/SKILL.md` with this content:

````markdown
---
name: brainstorm-plan
description: Use when the user wants to turn a new feature, behavior change, app, tool, plugin, or broad implementation idea into a final PlanForge implementation plan.
---

# Brainstorm Plan

Use this skill to turn a new idea into a final PlanForge plan.

This merges the useful parts of Superpowers brainstorming and writing-plans into one output:

```text
conversation -> approved design -> final implementation plan
```

Do not implement code during this skill. The output is a plan.

## Workflow

1. Inspect project context before asking detailed questions.
2. Ask one question at a time.
3. Keep questions concise and useful.
4. Propose alternatives only when there is a real tradeoff.
5. Present the design/spec in chat and get approval.
6. Write one final plan to `docs/planforge/plans/YYYY-MM-DD-topic.md`.
7. Ask whether the user wants to execute the plan now.

## Plan Requirements

Use `skills/_shared/plan-format.md`.

The plan must include:

- spec/design;
- implementation tasks;
- boundaries;
- verification;
- final review instructions;
- no automatic commit steps.

## Style

Be lighter than full Superpowers when the scope is clear. Do not restart the conversation after the user has already approved the design.

If the request is a bug, regression, failing test, or localized correction, use `brainstorm-fix` instead.
````

- [ ] **Step 3: Validate frontmatter**

Run:

```powershell
Get-Content skills\brainstorm-plan\SKILL.md -TotalCount 5
```

Expected: output includes `name: brainstorm-plan` and a description.

## Task 5: Create `brainstorm-fix` Skill

**Files:**

- Create: `skills/brainstorm-fix/SKILL.md`

- [ ] **Step 1: Create skill directory**

Create `skills/brainstorm-fix/`.

- [ ] **Step 2: Write `SKILL.md`**

Create `skills/brainstorm-fix/SKILL.md` with this content:

````markdown
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
````

- [ ] **Step 3: Validate frontmatter**

Run:

```powershell
Get-Content skills\brainstorm-fix\SKILL.md -TotalCount 5
```

Expected: output includes `name: brainstorm-fix` and a description.

## Task 6: Create `rework-plan` Skill

**Files:**

- Create: `skills/rework-plan/SKILL.md`

- [ ] **Step 1: Create skill directory**

Create `skills/rework-plan/`.

- [ ] **Step 2: Write `SKILL.md`**

Create `skills/rework-plan/SKILL.md` with this content:

````markdown
---
name: rework-plan
description: Use when the user wants to revise, refine, extend, shrink, or restructure an existing PlanForge plan.
---

# Rework Plan

Use this skill to update an existing PlanForge plan without restarting brainstorming.

## Workflow

1. Read the existing plan file.
2. Identify the requested change.
3. Inspect project context only if the change depends on current code.
4. Preserve completed checklist state unless the user asks to reset it.
5. Update the same plan by default.
6. Create a new plan only if the new direction materially forks from the old plan.

## Compatibility Rules

Use `skills/_shared/plan-format.md`.

The reworked plan must remain consumable by `execute-plan`.

Do not remove:

- agentic worker header;
- implementation tasks;
- verification checklist;
- final review instructions;
- no-commit Git policy.

## Output

After editing, summarize:

- what changed;
- whether checklist state was preserved;
- whether the plan is ready to execute.
````

- [ ] **Step 3: Validate frontmatter**

Run:

```powershell
Get-Content skills\rework-plan\SKILL.md -TotalCount 5
```

Expected: output includes `name: rework-plan` and a description.

## Task 7: Create `execute-plan` Skill

**Files:**

- Create: `skills/execute-plan/SKILL.md`

- [ ] **Step 1: Create skill directory**

Create `skills/execute-plan/`.

- [ ] **Step 2: Write `SKILL.md`**

Create `skills/execute-plan/SKILL.md` with this content:

````markdown
---
name: execute-plan
description: Use when the user wants to implement a PlanForge plan in either subagent mode or inline mode, with optional branch creation and mandatory final review.
---

# Execute Plan

Use this skill to implement an existing PlanForge plan.

Read these shared references as needed:

- `skills/_shared/plan-format.md`
- `skills/_shared/subagent-contract.md`
- `skills/_shared/final-review.md`

## Initial Flow

1. Read the plan.
2. Extract tasks and shared context.
3. Ask once: should I create a branch for this task?
4. If yes, create the branch and continue there.
5. If no, continue on the current branch.
6. Ask execution mode:
   - Subagent mode
   - Inline mode

## Git Policy

- Never commit automatically.
- Never push automatically.
- Never create a PR automatically.
- Never ask to commit at the end.
- Only create a branch if the user approves at execution start.

## Subagent Mode

Use the Superpowers dispatch pattern:

- Orchestrator reads the plan.
- Orchestrator curates task context.
- Implementer subagent receives full task text and needed context.
- Implementer reports `DONE`, `DONE_WITH_CONCERNS`, `NEEDS_CONTEXT`, or `BLOCKED`.

Per task:

1. Dispatch one implementer subagent.
2. Handle `NEEDS_CONTEXT` or `BLOCKED`.
3. Review the result locally as orchestrator.
4. Fix small issues directly or send a short corrective follow-up.
5. Update checklist status.
6. Continue to the next task.

Do not call separate formal review agents after every task by default.

## Inline Mode

The current agent executes the plan directly.

Per task:

1. Execute the task step by step.
2. Review locally against purpose, boundaries, expected result, and verification.
3. Fix issues before moving on.
4. Update checklist status.

## Final Review

Final review is mandatory in both modes.

Use a separate review agent focused only on review. Provide:

- plan text;
- implementation summary;
- changed file list;
- relevant diff;
- verification commands and results;
- known concerns.

Fix valid Critical and Important findings, decide whether Minor findings belong in scope, then rerun fresh verification before claiming completion.

## Completion

Final response must state:

- what changed;
- what verification ran;
- what passed;
- what failed or could not run;
- remaining risk.
````

- [ ] **Step 3: Validate frontmatter**

Run:

```powershell
Get-Content skills\execute-plan\SKILL.md -TotalCount 5
```

Expected: output includes `name: execute-plan` and a description.

## Task 8: Validate Plugin Structure and Policy

**Files:**

- Modify if needed: `.codex-plugin/plugin.json`
- Modify if needed: `skills/**/*.md`

- [ ] **Step 1: List created files**

Run:

```powershell
Get-ChildItem -Recurse -File .codex-plugin, skills | Select-Object FullName
```

Expected: output includes the manifest, four `SKILL.md` files, and three shared references.

- [ ] **Step 2: Validate manifest JSON**

Run:

```powershell
Get-Content .codex-plugin\plugin.json | ConvertFrom-Json | Out-Null
```

Expected: command exits successfully with no output.

- [ ] **Step 3: Confirm skill frontmatter**

Run:

```powershell
rg -n "^name:|^description:" skills
```

Expected: each user-facing `SKILL.md` has both `name` and `description`.

- [ ] **Step 4: Confirm no automatic commit workflow**

Run:

```powershell
rg -n "git commit|commit automatically|automatic commit|push automatically|create a PR automatically" skills
```

Expected: any matches are policy statements forbidding automatic commits, pushes, or PRs. There must be no instruction telling the agent to commit.

- [ ] **Step 5: Confirm PlanForge path**

Run:

```powershell
rg -n "docs/planforge/plans" skills
```

Expected: planning skills and shared plan format reference the default PlanForge plan path.

## Task 9: Review Against Approved Spec

**Files:**

- Inspect: `docs/superpowers/specs/2026-05-13-planforge-design.md`
- Inspect: all created plugin files
- Modify if needed: created plugin files

- [ ] **Step 1: Compare plugin files to spec goals**

Check that the created plugin supports:

- `brainstorm-plan`;
- `brainstorm-fix`;
- `rework-plan`;
- `execute-plan`;
- one final plan file by default;
- subagent and inline execution modes;
- no automatic commits;
- optional branch creation only at execution start;
- mandatory final separate review.

- [ ] **Step 2: Fix gaps**

If a created file contradicts the spec, edit the file to match the spec.

- [ ] **Step 3: Final status check**

Run:

```powershell
git status --short
```

Expected: only intended PlanForge plugin files, Superpowers spec, and this implementation plan are changed or untracked.

## Verification Checklist

- [ ] `.codex-plugin/plugin.json` exists and parses as JSON.
- [ ] `skills/brainstorm-plan/SKILL.md` exists.
- [ ] `skills/brainstorm-fix/SKILL.md` exists.
- [ ] `skills/rework-plan/SKILL.md` exists.
- [ ] `skills/execute-plan/SKILL.md` exists.
- [ ] `skills/_shared/plan-format.md` exists.
- [ ] `skills/_shared/subagent-contract.md` exists.
- [ ] `skills/_shared/final-review.md` exists.
- [ ] Skill descriptions are clear enough for Codex triggering.
- [ ] No skill instructs automatic commits.
- [ ] `execute-plan` asks branch and execution mode.
- [ ] Final review is mandatory and separate.

## Notes for Execution

This plan intentionally omits commit steps. That matches the user's preference for this project and the PlanForge Git policy being encoded in the plugin.

After implementation, the human can choose whether to commit, push, and publish the plugin repository to GitHub.
