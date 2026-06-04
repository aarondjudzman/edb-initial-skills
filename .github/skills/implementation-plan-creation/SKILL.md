---
name: implementation-plan-creation
description: "Use this skill when the user wants an implementation plan created from a story file or user story markdown. Turn the story into a structured, programming-language-agnostic implementation plan that a coding agent or LLM can execute. Prefer a TDD-friendly, test-first plan when the repository supports automated tests, and write the output into docs/implementation-plan/, especially when the user asks for a delivery plan, execution plan, engineering breakdown, technical approach, build steps, or a TDD plan for a feature."
---

# Implementation Plan Creation

Use this skill to transform a story file into an implementation-ready plan for software delivery.

Default output location: `docs/implementation-plan/`

## Inputs this skill can work from

- A story file in `docs/stories/`
- A user story pasted directly into chat
- Multiple related story files that need one coordinated implementation plan
- Supporting context such as technical notes, architecture documents, or constraints referenced alongside the story

## Workflow

1. Read the source story.
   - Use the provided story file as the source of truth.
   - If the request refers to a story but does not provide the file or text, ask for the missing story before drafting the plan.
2. Extract implementation intent.
   - Identify the feature goal, user value, acceptance criteria, constraints, dependencies, and open questions.
   - Separate confirmed requirements from assumptions.
3. Define the execution shape.
   - Break the work into coherent implementation phases or workstreams.
   - Keep the plan programming-language-agnostic unless the story explicitly requires a specific stack or technology.
   - Focus on what needs to be built, changed, validated, and coordinated, not on a particular syntax or framework.
   - When the repository supports automated tests, structure the plan around a TDD loop: add or update tests first, confirm they fail for the right reason, implement the minimum change to pass them, then refactor safely.
4. Draft the implementation plan in `docs/implementation-plan/`.
   - Use `assets/implementation-plan-template.md` as the default structure.
   - File name format: `docs/implementation-plan/implementation-plan-<short-kebab-case-title>.md`.
   - If one request covers multiple stories, either produce one coordinated plan or multiple plan files, depending on whether the delivery work is tightly coupled.
   - Include markdown checkboxes for each workstream and each implementation step so progress can be tracked during execution.
   - For each workstream, include explicit test-first steps and completion criteria for red, green, and refactor where that workflow is feasible.
5. Self-check before finishing.
   - Verify every acceptance criterion from the story is represented by planned work or explicit validation.
   - Verify the plan is actionable for a coding agent or LLM without restating the entire story.
   - Verify assumptions, risks, and unknowns are explicit.
   - Verify tasks are ordered so dependencies are clear.
   - Verify each workstream and step has an unchecked checkbox by default unless the user explicitly asked to pre-mark status.
   - Verify tests are introduced before production-code changes unless the work is genuinely non-testable infrastructure or research, in which case call that out explicitly.

## Writing rules

- Keep the plan implementation-oriented, not product-oriented.
- Stay language-agnostic by default. Describe responsibilities, data flow, interfaces, validation, and sequencing without prescribing language-specific syntax.
- Only mention technologies, frameworks, or services when they are explicitly present in the story or required context.
- Do not invent repository structure, APIs, database schemas, or deployment constraints.
- If the story is underspecified, preserve the gap under `Open Questions` or `Assumptions` instead of guessing.
- Prefer grouped workstreams with clear outcomes over long undifferentiated task lists.
- Include validation steps so a coding agent can confirm completion.
- Use markdown task-list syntax (`- [ ]`) for workstreams and actionable steps so the plan can double as an execution tracker.
- Prefer test-first steps over implementation-first steps when automated testing is available.
- Make the test strategy explicit: what behavior will be tested first, what failure should appear before implementation, and what regression checks should pass before the task is considered done.

## Output template

Read `assets/implementation-plan-template.md` and adapt it to the request. Keep the section order unless a section is clearly not applicable.

## Gotchas

- A story may describe user value but omit implementation constraints. Surface the missing constraints as questions instead of filling them in.
- Acceptance criteria often imply validation or edge-case work that is not stated as engineering tasks. Make that work explicit.
- Do not collapse discovery work, implementation work, and verification work into one step. Keep them separate when sequencing matters.
- If the story spans multiple subsystems, call out integration boundaries and coordination points explicitly.
- If the story is too large for one safe implementation pass, split the plan into phases or milestones.
- TDD is not just "write tests somewhere in the plan." The sequence matters: red first, then green, then refactor.
- If the repository has no meaningful automated test surface for the feature, say so explicitly and describe the closest safe validation strategy.

## Final response

- Write the implementation plan file or files into `docs/implementation-plan/`.
- Briefly tell the user which file or files were created.
- Mention any critical open questions, risks, or dependencies that could block implementation.
