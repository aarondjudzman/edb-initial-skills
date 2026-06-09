---
name: user-story-creation
description: "Use this skill when the user wants software development user stories created from requirements, transcripts, technical documents, PRDs, meeting notes, tickets, discovery notes, or mixed source material. Turn raw product or engineering inputs into structured, implementation-ready user story markdown files with acceptance criteria, assumptions, dependencies, and open questions. Always write the output into the project's docs folder, even when the user only provides loose notes or partial requirements."
---

# User Story Creation

Use this skill to turn product, business, or technical source material into one or more structured software-delivery user stories.

Default output location: `docs/stories/`

## Inputs this skill can work from

- Functional requirements or feature briefs
- Meeting or interview transcripts
- Technical design documents
- PRDs, epics, tickets, RFCs, and discovery notes
- Mixed inputs spread across chat messages and files

## Workflow

1. Collect the source material.
   - Read the files or text the user provided.
   - If the request references source material but none is available, ask for it before drafting the story.
2. Extract the delivery signal.
   - Identify the user or actor.
   - Identify the goal or task they need to accomplish.
   - Identify the business value or outcome.
   - Capture constraints, non-functional requirements, dependencies, risks, and open questions.
3. Decide the story split.
   - Produce one story when the request describes one coherent piece of user value.
   - Split into multiple stories when the input contains separate actors, workflows, or independently shippable capabilities.
   - Keep stories vertical and testable. Do not combine unrelated work into a single story.
4. Draft the story file in `docs/stories/`.
   - File name format: `docs/stories/user-story-<short-kebab-case-title>.md`.
   - If generating multiple stories from one request, use distinct filenames with stable slugs.
5. Self-check before finishing.
   - Verify the story can be understood without rereading the source material.
   - Verify each acceptance criterion is testable.
   - Verify assumptions are separated from confirmed facts.
   - Verify missing information is listed under open questions rather than invented.

## Writing rules

- Prefer product language over implementation details in the story statement.
- Include implementation constraints only when they are explicit in the source material or necessary for delivery context.
- Do not invent actors, APIs, edge cases, or compliance requirements.
- When the source is ambiguous, state the ambiguity in `Open Questions`.
- When the source is highly technical, translate it into user value first, then preserve important technical constraints in `Technical Notes`.
- Acceptance criteria should be concrete, testable, and phrased so engineering and QA can validate them.
- Use `Given/When/Then` only when it improves clarity; otherwise short bullet criteria are acceptable.

## Output template

Read `assets/user-story-template.md` and adapt it to the request. Keep the section order unless a section is not applicable.

## Gotchas

- A transcript may contain opinions, brainstorming, and contradictions. Extract decisions and unresolved questions separately.
- Technical documents often describe solution design, not user value. Convert design details into the user problem being solved.
- If the request mixes business and engineering concerns, keep the story focused on user value and place the rest in `Technical Notes`, `Dependencies`, or `Open Questions`.
- If there is not enough information to write a defensible story, stop and ask for the missing context instead of filling gaps with guesses.

## Final response

- Write the story file or files into `docs/stories/`.
- Briefly tell the user which file or files were created.
- Mention any critical open questions that could block implementation.
