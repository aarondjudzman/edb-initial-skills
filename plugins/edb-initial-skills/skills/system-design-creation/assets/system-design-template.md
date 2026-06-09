# System Design: <Title>

## Summary

<One paragraph describing the problem, the intended outcome, and the recommended design direction.>

## Source Inputs

- Request source: <files, notes, transcripts, tickets, or chat inputs used>
- Design scope: <what this document is solving>

## Problem Statement

- <Core problem or capability this design addresses>

## Requirements Snapshot

### Functional Requirements

- <Required behavior>
- <Required behavior>

### Non-Functional Requirements

- <Performance, reliability, security, compliance, operability, or scalability requirement>

### Constraints

- <Explicit technical, organizational, or delivery constraint>

## Assumptions

- <Assumption carried forward from incomplete input>

## Current State

- Existing system context: <what already exists and is relevant>
- Known gaps: <limitations or missing capabilities>
- Unknowns: <important areas that still need confirmation>

## Proposed Design

### Architecture Overview

<Short description of the recommended design and why it is the default choice.>

### Components and Responsibilities

- <Component or module>: <responsibility>
- <Component or module>: <responsibility>

### Data Model and Storage

- <Core entities, aggregates, schemas, or persistence approach>

### Interfaces and Integrations

- <Inbound interface or trigger>
- <Outbound dependency or integration>

### Key Flows

1. <Primary request or event flow>
2. <Secondary flow or exception path>

### Failure Handling and Observability

- <How errors are surfaced, retried, logged, or monitored>

### Security and Compliance

- <Auth, authz, data sensitivity, auditing, or compliance considerations>

## Alternatives Considered

- Recommended option: <default approach and why>
- Alternative: <meaningful alternative and why it was not chosen>

## TDD and Validation Strategy

- Test seams: <module boundaries, contracts, adapters, or use cases that are easy to test>
- First failing test candidates: <highest-value behaviors to validate first>
- Integration or contract coverage: <cross-boundary checks>
- End-to-end or operational validation: <workflow or runtime verification>
- Regression focus: <behaviors that must remain stable>

## Delivery Shape

- Slice 1: <smallest valuable increment>
- Slice 2: <next increment>
- Slice 3: <follow-up or scale hardening>

## Risks

- <Design risk and why it matters>

## Open Questions

- <Question that could materially change the design>

## Recommended Next Step

- <Best immediate next action after accepting this design>
