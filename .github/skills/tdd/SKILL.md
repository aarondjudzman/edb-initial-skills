---
name: tdd
description: "Use this skill when the user wants strict test-driven development: red/green/refactor, test-first implementation, behavior-focused tests, or small vertical slices for a feature or bug fix."
---

# Test-Driven Development

Use this skill to execute work in a true TDD loop: one behavior, one failing test, the minimum code to pass, then refactor while green.

Default outcome: a small, behavior-scoped change implemented through vertical slices with focused validation and tests that remain stable through refactors.

## Inputs this skill can work from

- A feature request or bug report
- An implementation plan that calls for test-first work
- A failing test that needs the smallest production fix
- A request for red/green/refactor, test-first, or behavior-driven implementation

## Required working assumptions

Before writing production code:

- Confirm the public behavior that needs to change
- Identify the narrowest test surface that can express that behavior
- Prefer user-facing or caller-facing interfaces over internal helpers
- Use repository language, terminology, and patterns already present in the codebase

If the repository does not provide a meaningful automated test surface for the slice, say so explicitly and use the closest reliable validation path instead of pretending the work is TDD.

## Workflow

1. Plan the slice.
   - Identify the smallest concrete behavior worth proving first.
   - Clarify the interface and expected outcome before writing tests.
   - Prefer a single vertical slice over broad upfront test scaffolding.
   - Use the project glossary and neighboring tests so names match the codebase.
2. Write one failing test.
   - Add the smallest focused test that demonstrates the behavior is currently missing or broken.
   - Verify the test fails for the expected reason before changing production code.
   - If the failure is ambiguous, tighten the test before proceeding.
3. Write the minimum code to pass.
   - Change only the code needed for the current failing test.
   - Do not add future-facing behavior, generalized abstractions, or speculative branches.
   - Rerun the same focused test until it passes.
4. Refactor while green.
   - Remove duplication and improve names only after the behavior is passing.
   - Keep tests pointed at public behavior during refactors.
   - Rerun focused validation after each meaningful refactor step.
5. Repeat one slice at a time.
   - Add the next most important behavior only after the previous slice is green.
   - Let each passing slice inform the next test instead of prebuilding an entire suite.

## Execution rules

- Work in vertical slices, not horizontal phases.
- Do not write all tests first and all implementation later.
- A test must describe observable behavior, not internal mechanics.
- Prefer integration-style tests that exercise real code paths through a public interface.
- Only mock true system boundaries such as external APIs, time, randomness, or infrastructure seams.
- Do not refactor while a slice is red.
- After the first substantive edit, run the narrowest focused validation before broadening scope.

## Per-slice checklist

- [ ] The test describes behavior rather than implementation details
- [ ] The test uses a public interface or an approved boundary seam
- [ ] The failure was observed before production code changed
- [ ] The code change is minimal for the current behavior
- [ ] No speculative features were added
- [ ] Validation was rerun after refactoring

## Supporting guidance

- See [tests.md](./tests.md) for behavior-focused test patterns.
- See [mocking.md](./mocking.md) for when mocking is appropriate.
- See [interface-design.md](./interface-design.md) for designing seams that are naturally testable.
- See [deep-modules.md](./deep-modules.md) for keeping interfaces small and implementations deep.
- See [refactoring.md](./refactoring.md) for common cleanup targets after green.

## Gotchas

- Adding passing tests after the production change is validation, not TDD.
- Bulk test writing usually locks in imagined behavior and weak assertions.
- Tests that assert call counts, private helpers, or internal collaborator wiring are fragile by default.
- Reaching through storage layers or internal state to verify outcomes often means the test is bypassing the public contract.
- Sometimes the first TDD task is establishing a usable harness or boundary seam. Treat that as its own slice.

## Final response

- Summarize the behavior implemented or fixed.
- Mention the focused tests or validations that were used.
- Call out any places where strict TDD was not possible and why.
- Note remaining uncovered behaviors, risks, or follow-up slices.
