---
name: system-design-creation
description: "Use this skill when the user wants a system design, technical design, solution architecture, high-level design, low-level design, or architecture proposal created from requirements, stories, tickets, PRDs, meeting notes, RFCs, code references, or mixed source material. Turn the input into a maintainable, implementation-oriented design with clear boundaries, interfaces, tradeoffs, risks, and a TDD-friendly validation strategy. Prefer the simplest design that satisfies the requirements, separate confirmed facts from assumptions, and write the output into docs/system-design/."
---

# System Design Creation

Use this skill to turn product, engineering, or discovery inputs into a system design document that is maintainable, implementation-oriented, and ready to guide test-first delivery.

Default output location: `docs/system-design/`

## Inputs this skill can work from

- User stories or story files
- PRDs, epics, tickets, RFCs, and technical briefs
- Meeting notes, transcripts, and discovery documents
- Existing architecture notes or system constraints
- Relevant code references, repository structure, or interface definitions
- Mixed inputs spread across chat messages and files

## Workflow

1. Collect and normalize the source material.
   - Read the files and chat context the user provided.
   - If the request references requirements that are not available, ask for the missing source before drafting the design.
   - When multiple inputs overlap or conflict, preserve the conflict explicitly instead of guessing.
2. Extract the design drivers.
   - Identify the problem to solve, user or system goals, functional requirements, non-functional requirements, constraints, dependencies, and operational concerns.
   - Separate confirmed requirements from assumptions and open questions.
   - Identify which requirements drive architecture decisions versus implementation details.
3. Establish current-state context.
   - Inspect nearby repository artifacts only as needed to understand the existing system shape.
   - Distinguish clearly between what already exists, what is unknown, and what is being proposed.
   - Do not invent current architecture, schemas, interfaces, or infrastructure.
4. Design the solution.
   - Start from the simplest design that can satisfy the requirements safely.
   - Prefer clear module boundaries, explicit responsibilities, and low-coupling interfaces.
   - Optimize for maintainability: make ownership, extension points, failure handling, and operational visibility easy to reason about.
   - When multiple viable approaches exist, recommend one default approach and summarize meaningful alternatives with tradeoffs.
5. Make the design TDD-friendly.
   - Identify test seams, contract boundaries, and validation layers early.
   - Describe how the design supports unit, integration, contract, and end-to-end testing where applicable.
   - Call out the highest-risk or highest-value behaviors that should be validated first in a red-green-refactor loop.
   - Prefer incremental delivery slices that can be verified independently.
6. Draft the design file in `docs/system-design/`.
   - Use `assets/system-design-template.md` as the default structure.
   - File name format: `docs/system-design/system-design-<short-kebab-case-title>.md`.
   - Produce one design document per coherent problem. Split into multiple files only when the request spans independent systems or delivery tracks.
7. Self-check before finishing.
   - Verify every explicit requirement is represented in the proposed design, assumptions, or open questions.
   - Verify the recommended design is maintainable and not over-engineered for the stated needs.
   - Verify the design contains a concrete validation and test strategy, not just general advice to "add tests."
   - Verify tradeoffs, risks, and unknowns are explicit.

## Writing rules

- Keep the document design-oriented, not backlog-oriented. Explain structure, responsibilities, interfaces, data flow, and validation.
- Stay programming-language-agnostic by default unless the user or repository context requires specific technology choices.
- Prefer a recommended default design over a long menu of equal options.
- Optimize for maintainability: simple abstractions, cohesive modules, clear contracts, minimal incidental complexity, and explicit operational concerns.
- Include enough implementation guidance to make the design actionable, but do not collapse the design into a step-by-step implementation plan.
- Make TDD support explicit by identifying test seams, first-failing-test candidates, and regression boundaries.
- Do not invent requirements, repository structure, production traffic, compliance obligations, or platform constraints.
- If the requirements are underspecified, preserve the gap under `Assumptions` or `Open Questions` instead of filling it with guesses.

## Output template

Read `assets/system-design-template.md` and adapt it to the request. Keep the section order unless a section is clearly not applicable.

## Gotchas

- Source material often mixes desired outcomes with proposed solutions. Preserve the difference so the design is driven by requirements, not by unexamined implementation suggestions.
- Avoid speculative abstractions. If an extension point or subsystem is not justified by the requirements, leave it out.
- A maintainable design is not the same as a minimal paragraph. Be explicit about boundaries, dependencies, and failure modes where they matter.
- TDD friendliness is not just a note that tests should exist. Show how the design creates testable seams and safe incremental slices.
- If the request spans multiple subsystems, call out integration boundaries and ownership clearly.
- If repository context is sparse, state what is unknown rather than inferring a current architecture.

## Final response

- Write the design file or files into `docs/system-design/`.
- Briefly tell the user which file or files were created.
- Mention any critical assumptions, tradeoffs, or open questions that could change the design materially.
