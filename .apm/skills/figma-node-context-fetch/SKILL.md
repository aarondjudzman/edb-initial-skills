---
name: figma-node-context-fetch
description: "Use this skill when the user already knows which Figma node should drive implementation and needs the exact subtree JSON for that node. Use the Figma REST API to fetch a bounded node subtree and summarize the design context needed for coding, without flooding the conversation with raw API output."
---

# Figma Node Context Fetch

Use this skill to fetch the exact Figma node subtree that should inform implementation work.

Default outcome: a targeted, implementation-relevant summary of a node subtree plus the normalized identifiers and raw-fetch facts needed by downstream steps.

## Inputs this skill can work from

- A Figma node URL
- A Figma file URL plus a known `node_id`
- A raw `file_key` and `node_id`
- Output from `figma-file-outline-scan`
- A user-provided request to fetch a specific frame, component, or subtree

## Required input contract

Provide one of the following:

- `figma_node_url`
- `file_key` and `node_id`
- `scan_result` from `figma-file-outline-scan` containing a single selected candidate

Optional controls:

- `depth`
- `include_geometry_paths`
- `include_plugin_data`
- `version`
- `reason_for_fetch` such as implementation, review, token extraction, or component mapping

## Workflow

1. Normalize the target node.
   - Parse `file_key` and `node_id` from the provided URL or structured input.
   - If the input came from `figma-file-outline-scan`, confirm that only one node is selected before fetching.
2. Fetch only the needed subtree.
   - Use `GET /v1/files/:key/nodes` with the specific `ids` value.
   - Apply `depth` when a bounded subtree is sufficient.
   - Only request `geometry=paths` when vector path detail is actually needed.
3. Extract implementation-relevant context.
   - Capture node names, types, hierarchy, layout clues, visible text, fills, strokes, effects, spacing-related properties, component instance references, and style references.
   - Note which parts of the subtree are reusable components versus one-off layout containers.
4. Reduce the payload.
   - Summarize the subtree in a way that a coding agent can use directly.
   - Avoid dumping raw API JSON unless the user explicitly asked for it.
5. Hand off cleanly.
   - Recommend `figma-design-token-extract` if the next step is token/spec generation.
   - Recommend a screenshot export if visual verification is needed.

## API guidance

- Primary endpoint:
  - `GET /v1/files/:key/nodes`
- Useful parameters:
  - `ids`
  - `depth`
  - `geometry=paths`
  - `plugin_data`
  - `version`
- Prefer this endpoint over `GET /v1/files/:key` once the target node is known.
- Treat null node values as a real possibility and report them clearly.

## Output contract

Return a concise node-context summary with:

- normalized `file_key`
- normalized `node_id`
- fetched node name and type
- subtree depth actually retrieved
- a compact hierarchy summary
- notable text content and labels
- layout and spacing signals relevant to implementation
- component and style references
- unresolved ambiguities or missing data
- recommended next step such as token extraction, component mapping, or screenshot export

## Writing rules

- Optimize for implementation context, not exhaustive design archival.
- Keep layout relationships clearer than absolute coordinates when both are available.
- Separate directly observed node facts from any interpretation.
- Preserve enough structure for downstream reuse.

## Gotchas

- Large subtrees can still overwhelm the prompt if `depth` is unbounded.
- A fetched subtree may include dependency components or style references outside the immediate visual frame.
- `geometry=paths` increases payload size and should be used only when vector fidelity matters.
- A node can exist but still be unsuitable for coding if it is just a wrapper frame with the real implementation detail nested deeper.
- REST API data captures design structure, not business logic, event handling, or state transitions.

## Final response

- Return the targeted subtree summary, not a full raw dump.
- Include the identifiers and fetch parameters used.
- State whether the subtree is ready for `figma-design-token-extract` or whether a narrower follow-up fetch would be better.
