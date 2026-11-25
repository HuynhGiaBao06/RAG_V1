# Project Instructions

## Scope and Language

Write project documentation, checkpoints, and skills in English. Respond to the user in their preferred language.

Before substantial work, determine the task, work mode, read scope, write scope, target, objective, constraints, and validation requirements. Supported modes are `READ_ONLY`, `REVIEW`, `PLAN_ONLY`, `AUTO_IMPLEMENT`, and `VERIFY_ONLY`.

Missing work mode defaults to `READ_ONLY`. Missing write scope means no file modification. Implementation is permitted only when the user explicitly grants `AUTO_IMPLEMENT`, and only files listed in the approved Write Scope may be modified. Findings and recommendations do not authorize changes.

The user creates and connects the Git repository. Git initialization, commits, remote changes, and pushes require task-specific authorization.

---

## Execution Authorization Gate

`AUTO_IMPLEMENT` and an approved Write Scope authorize only the listed file modifications. They do not authorize command execution through Git, WSL, network access, or sandbox escalation.

Before every direct or indirect invocation of `git`, `git.exe`, `wsl`, or `wsl.exe`, obtain explicit user approval for that exact invocation. This requirement applies to read-only commands, subprocesses, shell wrappers, scripts, aliases, validation commands, and commands suggested by a workflow or skill.

Approval for one invocation does not authorize another invocation. Never request, propose, create, or rely on a reusable `allow` rule for Git or WSL. A persistent command rule for these executables may only use `prompt` or `forbidden`.

Before requesting sandbox escalation, state the exact command or file operation, exact targets, why escalation is required, and whether the request is one-time or reusable. Sandbox escalation requires separate explicit approval and is never implied by `AUTO_IMPLEMENT`.

If Git or WSL authorization is absent, use filesystem inspection that does not invoke them and report the corresponding repository or runtime state as unverified.

---

## Source of Truth

Use project documents, implementation, tests, and current runtime evidence as the source of truth.

Priority:

1. Current user task
2. Approved project planning / requirements
3. Architecture / methodology documents
4. Current implementation
5. Tests and runtime evidence
6. Generic best practices

Generic best practices must not override explicit project decisions.

---

## Scope Control

Do not expand the project unless explicitly requested.

Do not automatically add new:

- models, datasets, or features;
- metrics or statistical tests;
- XAI or fairness methods;
- experiments;
- dependencies;
- pipeline stages.

Optional improvements must be reported separately, not implemented.

---

## Context Loading

Read this file at session start. Read only the architecture sections and source files relevant to the current task. Use the session-handoff skill when resuming from a checkpoint or preparing a handoff. Read the selected task record rather than the whole handoff directory.

The architecture source is [docs/architecture.md](docs/architecture.md). The shared handoff procedure is [.agent/workflow.md](.agent/workflow.md). Skills are entry points to that procedure.

### Repository code map

When `docs/code-map/` exists, use its index and the relevant component page for routing, then read current source and approved architecture before making claims. Check `status.json` before relying on map freshness. If the state is not `CURRENT`, follow the project-local `repo-code-map` skill; unavailable Git evidence remains `UNVERIFIED` and does not stop independent filesystem work. Refresh map artifacts only when the active task authorizes every affected path.

---

## Architecture Control

Treat the existing architecture as a constraint.

Do not redesign architecture, add abstractions, reorganize modules, or refactor unrelated components unless explicitly required.

Prefer the smallest change that satisfies the task.

Keep extract and compose independent. External SDKs belong in adapters. Application services accept typed options and injected dependencies. Keep Document and PagePlan separate. Add shared code only for demonstrated shared requirements.

Preserve unrelated edits. Review shared contract changes before dependent work proceeds. Use scoped branches or worktrees once the user has established Git.

---

## File and Component Scope

Only work on files, sections, notebook cells, functions, or components included in the current task scope.

For `AUTO_IMPLEMENT`, only files listed in Write Scope may be modified.

If another file is required, report why it is needed instead of modifying it without authorization.

---

## Notebook Rules

Unless explicitly authorized:

- do not execute cells;
- do not restart the kernel;
- do not Run All;
- do not modify unrelated cells or outputs.

Static inspection is not runtime validation.

---

## Validation

Use validation proportional to the task:

- relevant tests;
- `git diff`;
- schema/data checks;
- source-to-document comparison;
- notebook execution only when authorized.

Record actual commands and outcomes. Distinguish implemented, planned, failed, and unverified work. Check changed files, relevant tests, documentation consistency, and scope before claiming completion. Never mark a task complete solely because code or output files exist.

Do not claim broader validation than what was actually performed.

---

## Required Task Report

Every substantial task must report the actual project state, not only actions performed.

### Task

Briefly state what was requested.

### Work Mode

State the effective mode: `READ_ONLY`, `REVIEW`, `PLAN_ONLY`, `AUTO_IMPLEMENT`, or `VERIFY_ONLY`.

### Work Done

State:

- files read;
- files modified;
- implementation or review performed;
- tests or checks executed.

### Current State

Report the current state relevant to the task:

- data status;
- code status;
- notebook or pipeline status;
- test status;
- completed versus incomplete work.

Do not omit known failures, missing data, unresolved checks, or partially implemented components.

### Technical Findings

Report important technical findings, including when relevant:

- data quality issues;
- schema or feature issues;
- code defects;
- architecture concerns;
- data leakage risk;
- validation gaps;
- test failures;
- statistical or ML methodology concerns;
- inconsistencies between documentation and implementation.

Separate confirmed findings from assumptions or recommendations.

### Problems / Blockers

Explicitly list anything preventing reliable completion.

If none:

`None`

### What Needs to Happen Next

Provide the next concrete technical step based on the current project state.

Prefer actionable statements such as:

- implement X;
- validate Y;
- fix Z;
- run test A;
- investigate data issue B;
- complete section C.

Do not end with vague statements such as:

- "continue development";
- "consider improvements";
- "further testing may be useful".

### Hallucination / Overclaim Check

Confirm that:

- reported files were actually inspected;
- reported tests were actually run;
- implemented work is not confused with planned work;
- conclusions do not exceed available evidence.

Use `PASS`, `ISSUES FOUND`, or `INCOMPLETE`, with a brief explanation for any issue.

### Scope Check

Confirm whether all actions stayed inside the authorized task scope.

Use `PASS` or `SCOPE DEVIATION DETECTED`.

### Unverified Items

List anything that could not be verified. If none, state `None`.

### Final Status

Use one:

- `COMPLETE`
- `PARTIAL`
- `BLOCKED`
- `REVIEW ONLY`

`COMPLETE` is allowed only when the requested work and required validation are actually finished.
