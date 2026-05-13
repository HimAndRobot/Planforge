# PlanForge Final Review

Final review is mandatory for both subagent mode and inline mode.

The final reviewer must be a separate agent focused only on review. The reviewer does not implement fixes.

The reviewer must verify the work independently. Do not trust the implementer report or orchestrator summary without checking the actual files and diff.

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

The reviewer should inspect the actual implementation, not only summaries:

- read the relevant code;
- inspect the diff when available;
- compare implementation to the plan line by line where practical;
- look for missing requirements;
- look for extra or unrequested behavior;
- look for misunderstood requirements;
- check tests and verification evidence.

## Reviewer Output

The reviewer should return findings ordered by severity:

- Critical: must fix before completion.
- Important: should fix before completion.
- Minor: optional cleanup or follow-up.

If there are no findings, the reviewer should say that clearly and mention residual risk.

For each issue:

- include file and line reference when possible;
- explain what is wrong;
- explain why it matters;
- suggest how to fix it when not obvious.

## Critical Rules

Do:

- categorize by actual severity;
- be specific;
- give a clear verdict;
- call out plan issues separately from implementation issues.

Do not:

- say "looks good" without checking;
- give feedback on code you did not read;
- mark nitpicks as Critical;
- be vague.

## After Review

The orchestrator fixes valid Critical and Important findings, decides whether Minor findings belong in scope, then reruns fresh verification before claiming completion.

Do not claim the work is complete until fresh verification supports that claim.
