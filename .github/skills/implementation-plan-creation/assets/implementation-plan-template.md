# <Short, action-oriented description>

This ExecPlan is a living document. Keep `Progress`, `Surprises & Discoveries`, `Decision Log`, and `Outcomes & Retrospective` up to date as work proceeds.

Source material: <story file path, story title, or pasted source text>

## Purpose / Big Picture

Explain in a few sentences what someone gains after this change, why it matters, and how to see it working. State the user-visible behavior this plan will enable.

## Progress

Use timestamped checkboxes to reflect the actual current state of the work. Split partially completed work into done and remaining items rather than leaving ambiguous status.

- [ ] (<YYYY-MM-DD HH:MMZ>) Initial ExecPlan drafted.
- [ ] (<YYYY-MM-DD HH:MMZ>) First milestone implemented and validated.

## Surprises & Discoveries

Capture unexpected behaviors, bugs, or insights discovered during research or implementation. Include concise evidence when available.

- Observation: None yet.
  Evidence: N/A.

## Decision Log

Record each meaningful design or execution decision as it happens.

- Decision: Initial ExecPlan structure adopted.
  Rationale: Align the plan with the repository's execution-plan standard and keep it self-contained for a new contributor.
  Date/Author: <YYYY-MM-DD / author>

## Outcomes & Retrospective

Summarize what has been delivered, what remains, and lessons learned. Update this at major milestones or when the plan completes.

- Not started yet.

## Context and Orientation

Describe the relevant current state as if the reader knows nothing about this repository. Name the key files, modules, commands, and boundaries by full repository-relative path. Define non-obvious terms immediately. Include assumptions, dependencies, constraints, risks, open questions, and any source-story details needed so the plan remains self-contained.

## Plan of Work

Describe the intended sequence of edits and additions in prose. For each area of change, name the file or repository location to inspect or modify, what will change there, and why that change is needed. Keep this concrete enough that a novice can follow it without prior context.

## Milestones

### Milestone 1: <Short milestone title>

Describe the scope of this milestone, what will exist at the end that does not exist now, the commands that will be run, and the observable acceptance expected when it is complete.

### Milestone 2: <Short milestone title>

Describe the next increment in the same narrative style. Add or remove milestones to fit the work, but keep each one independently verifiable.

## Concrete Steps

State the exact commands to run, where to run them, and what a reader should expect to observe. When a command should fail before implementation, say so explicitly.

1.  From the repository root, run:

         <command>

    Expected result:

         <short output or failure signal>

2.  After making the change, rerun:

         <command>

    Expected result:

         <short passing output or behavior>

## Validation and Acceptance

Describe how to exercise the system and what observable behavior proves success. Phrase acceptance in terms of inputs and outputs, user-visible effects, or focused tests that fail before the change and pass after it.

## Idempotence and Recovery

Explain which steps are safe to repeat, what cleanup is required, and how to recover if a step fails partway through. If a step is risky or destructive, provide a safe fallback or rollback path.

## Artifacts and Notes

Include the most important transcripts, snippets, diffs, or notes as short indented examples. Keep them concise and focused on what proves progress or correctness.

        <example transcript or note>

## Interfaces and Dependencies

Name the modules, services, libraries, commands, and interfaces involved. Be explicit about any function signatures, data contracts, APIs, or file boundaries that must exist when the plan is complete.
