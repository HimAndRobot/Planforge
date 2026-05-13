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

Do not pass the whole conversation history when a focused work product is enough.

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

Do not claim the work is complete until fresh verification supports that claim.
