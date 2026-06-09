---
name: figma-design-token-extract
description: "Use this skill when the user needs a reduced, implementation-friendly design token and spec summary from a Figma node or subtree. Use targeted Figma REST API data to extract colors, typography, spacing, styles, assets, and component references without requiring MCP or dumping raw design JSON into the prompt."
---

# Figma Design Token Extract

Use this skill to convert a targeted Figma node or subtree into a compact design token and implementation-spec summary.

Default outcome: a reduced design-context artifact suitable for code generation, styling decisions, or implementation planning.

## Inputs this skill can work from

- A Figma node URL
- A `file_key` plus `node_id`
- Output from `figma-node-context-fetch`
- Output from `figma-file-outline-scan` when the target node is already unambiguous
- A user request to extract colors, typography, spacing, image fills, or reusable component references from a specific frame

## Required input contract

Provide one of the following:

- `figma_node_url`
- `file_key` and `node_id`
- `node_context` from `figma-node-context-fetch`

Optional controls:

- `depth`
- `include_styles`
- `include_image_fills`
- `include_component_refs`
- `output_format` such as `compact-markdown`, `json`, or `implementation-summary`
- `token_focus` such as colors, typography, spacing, assets, or all

## Workflow

1. Resolve or reuse node context.
   - If the user supplied `node_context`, reuse it instead of refetching.
   - Otherwise fetch the minimal subtree needed from Figma.
2. Extract only implementation-relevant design facts.
   - Identify colors, typography, spacing, sizing, corner radius, borders, shadows, layout behavior, and reusable component references.
   - Prefer style or component references when available over raw duplicated values.
3. Reduce noisy API fields.
   - Exclude irrelevant metadata such as editor bookkeeping, export noise, or large coordinate dumps unless specifically needed.
   - Convert repeated raw values into grouped token candidates.
4. Resolve linked resources when requested.
   - If image fills matter, use `GET /v1/files/:key/images` to resolve `imageRef` values.
   - If screenshots or visual exports are needed, recommend a separate image-export step instead of inflating the token summary.
5. Produce a coding-friendly output.
   - Organize the result as a concise design spec that a coding agent can apply.
   - Distinguish direct observations from inferred token names.

## API guidance

- Primary data source:
  - `GET /v1/files/:key/nodes`
- Supporting endpoint when needed:
  - `GET /v1/files/:key/images`
- Use upstream node context when available to avoid redundant API calls.
- Prefer extraction from a targeted subtree, not from an entire page or file.

## Output contract

Return a compact token/spec summary with:

- normalized `file_key`
- normalized `node_id`
- token groups actually extracted
- colors with raw values and any referenced styles
- typography details such as font family, size, weight, line height, and text role when visible
- spacing and sizing signals that matter for implementation
- radii, borders, shadows, and other relevant visual primitives
- component and style references that should drive reuse
- resolved image-fill links when requested
- explicit note of what was observed versus what was inferred or grouped

## Writing rules

- Prefer grouped, reusable token candidates over long per-node property dumps.
- Keep the output compact enough to feed directly into a coding task.
- If a value is ambiguous or mixed across the subtree, preserve that ambiguity instead of forcing a single token.
- Do not claim the extracted token summary is a complete design system unless the source actually supports that conclusion.

## Gotchas

- This skill should not require another skill's output, but it should reuse upstream context when available.
- A token summary is not a full implementation spec; interaction logic and state behavior are still outside the design payload.
- Repeated raw values do not automatically imply intentional design tokens.
- Image URLs returned by the API expire; present them as temporary references, not permanent assets.
- Styles and components may exist in the payload even when the visible subtree is small. Keep only what is relevant to implementation.

## Final response

- Return the reduced token/spec summary in the requested or most useful format.
- Mention whether the result came from direct fetch, reused node context, or both.
- Call out any missing inputs that would improve fidelity, such as a narrower node target or image-fill resolution.
