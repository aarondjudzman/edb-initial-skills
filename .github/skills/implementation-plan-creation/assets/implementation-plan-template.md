# Implementation Plan: <Title>

## Summary

<One paragraph describing the feature to implement, the delivery goal, and the overall execution approach.>

## Source Story

- Story file: <path to the story file or source text>
- Story title: <story title>

## Objectives

- <Primary implementation objective>
- <Secondary implementation objective>

## Scope

- In scope: <capabilities, changes, or deliverables included in this plan>
- Out of scope: <related items explicitly excluded from this plan>

## Assumptions

- <Assumption carried forward from incomplete or ambiguous requirements>

## Dependencies and Constraints

- Dependencies: <systems, teams, inputs, or prior work required>
- Constraints: <technical, operational, security, regulatory, or delivery constraints>

## Test Strategy

- Automated test surface: <existing test suite, harness, or note that test support is missing>
- Initial failing test target: <behavior that should fail before implementation>
- Passing criteria: <what must pass after implementation>
- Regression coverage: <related scenarios that should remain green>

## Workstreams

### 1. [ ] <Workstream name>

- Goal: <what this workstream achieves>
- Red:
  - [ ] <add or update a test that captures the intended behavior>
  - [ ] <run the targeted test and confirm it fails for the expected reason>
- Green:
  - [ ] <make the smallest implementation change needed to satisfy the failing test>
  - [ ] <rerun the targeted tests and confirm they pass>
- Refactor:
  - [ ] <clean up implementation or test code while keeping behavior unchanged>
  - [ ] <rerun targeted and regression tests>
- Output: <artifact, behavior change, or deliverable>

### 2. [ ] <Workstream name>

- Goal: <what this workstream achieves>
- Red:
  - [ ] <add or update a test that captures the intended behavior>
  - [ ] <run the targeted test and confirm it fails for the expected reason>
- Green:
  - [ ] <make the smallest implementation change needed to satisfy the failing test>
  - [ ] <rerun the targeted tests and confirm they pass>
- Refactor:
  - [ ] <clean up implementation or test code while keeping behavior unchanged>
  - [ ] <rerun targeted and regression tests>
- Output: <artifact, behavior change, or deliverable>

## Validation Plan

- <How to verify the feature behavior>
- <How to verify acceptance criteria are met>
- <How to verify regressions are not introduced>

## Risks

- <Implementation risk and why it matters>

## Open Questions

- <Question that must be answered before or during implementation>

## Suggested Execution Order

- [ ] Write or update the first failing test for the highest-priority behavior
- [ ] Implement the minimum code required to make the targeted tests pass
- [ ] Refactor safely and rerun targeted plus regression validation
