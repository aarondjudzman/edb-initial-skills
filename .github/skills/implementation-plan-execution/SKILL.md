---
name: implementation-plan-execution
description: "Use this skill when the user wants an existing implementation plan executed or implemented. Read the plan file from docs/implementation-plan/, carry out the coding work in the repository, update the plan's checkbox status as tasks are completed, and validate the implementation before marking steps done. Prefer a TDD workflow when automated tests are available: write or update a failing test first, implement the minimum change to pass it, then refactor safely. Use this when the user asks to implement a plan, execute a delivery plan, work through an engineering plan, build a feature from an existing implementation plan, or follow TDD/test-first development."
---

# Implementation Plan Execution

Use this skill to execute an existing implementation plan against the current repository.

Primary input location: `docs/implementation-plan/`

## Inputs this skill can work from

- An implementation plan file in `docs/implementation-plan/`
- A plan pasted directly into chat
- A request to implement a subset of a plan, such as one workstream or a few tasks
- Supporting context such as the linked story file, technical notes, or adjacent repository files

## Workflow

1. Read the implementation plan first.
   - Use the plan file as the execution checklist and source of truth for scope.
   - If the plan references a source story file, read that story before coding.
   - If the user asks to implement only part of the plan, limit work to that slice.
2. Determine the next execution slice.
   - Prefer the first unchecked item in `Suggested Execution Order`, then map it to the relevant unchecked workstream tasks.
   - If the plan is organized by workstreams only, start with the first unchecked workstream and its unchecked tasks.
   - Do not broaden scope beyond the selected slice unless the plan explicitly requires it.
3. Ground the change in the codebase.
   - Find the smallest concrete implementation surface that controls the requested behavior.
   - Read only enough nearby code to identify the owning abstraction, one falsifiable local hypothesis, and one cheap validation check.
   - Identify the narrowest existing automated test surface that can express the next behavior change.
   - If the plan is underspecified for safe implementation, stop and ask for clarification instead of guessing.
4. Execute in TDD order when feasible.
   - Start by writing or updating the smallest focused automated test that captures the current unchecked behavior.
   - Run that targeted test first and confirm it fails for the expected reason before changing production code.
   - Make the smallest useful code change for the current unchecked task.
   - Rerun the targeted test until it passes, then run the narrowest necessary regression checks.
   - Refactor only after the behavior is green, and rerun tests after refactoring.
   - If the repository does not provide a meaningful automated test surface for the slice, record that constraint and use the closest reliable validation path.
5. Validate before updating status.
   - Prefer focused tests over broader validation whenever a suitable automated test exists.
   - If validation fails, fix the same slice and rerun the validation before moving on.
   - Do not mark a task complete until the related tests or agreed validation checks succeed.
6. Update the implementation plan as execution progresses.
   - Change completed task checkboxes from `- [ ]` to `- [x]`.
   - Within each workstream, mark `Red`, `Green`, and `Refactor` subtasks complete only when that stage is genuinely finished.
   - Mark a workstream checkbox complete only when all tasks under it are complete.
   - Mark a `Suggested Execution Order` step complete only when that phase is actually finished.
   - If blocked, leave the checkbox unchecked and add a brief blocker note in the relevant section or under `Open Questions`.
7. Continue until the requested scope is finished.
   - If the user asked to execute the full plan, work through all unchecked items that can be completed safely in the current session.
   - If some items remain blocked or out of scope, report them clearly at the end.

## Execution rules

- Treat the implementation plan as the task tracker, not just reference material.
- Do not mark speculative or partially complete work as done.
- Preserve the plan's structure and edit only the status checkboxes and brief execution notes needed to reflect reality.
- Follow repository conventions, toolchains, and language choices already present in the codebase.
- When the plan is language-agnostic, infer the concrete implementation details from the repository rather than inventing a new stack.
- Respect dependencies and sequencing from the plan; do not skip ahead if earlier unchecked work is required.
- In TDD-capable codepaths, do not write production code before you have a failing test for the behavior you are about to change.
- Keep test changes as small and behavior-specific as the production changes they guard.

## Gotchas

- A task description in the plan may map to several repository changes. Complete the whole task before checking it off.
- Some tasks depend on unresolved open questions. Do not silently choose an arbitrary direction; flag the blocker.
- Validation is part of implementation. A code change without a confirming check is not complete.
- If repository reality differs from the plan, adapt locally but record the discrepancy in the plan or final response.
- A passing test added after production code is not TDD. The failure should be observed first whenever the repository supports it.
- Some work, such as one-time scaffolding or missing test infrastructure, may require establishing a test harness before feature-level red/green/refactor can begin.

## Final response

- Briefly summarize what was implemented.
- List the plan file that was updated.
- Call out which tasks or workstreams were marked complete.
- Mention any remaining unchecked items, blockers, or follow-up work.
