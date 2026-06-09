---
name: figma-file-outline-scan
description: "Use this skill when the user needs to identify the right page, frame, or node in a Figma file before deeper API fetches. Use the Figma REST API to scan file metadata and a shallow document outline, then return likely candidate nodes for implementation work without dumping the full file JSON."
---

# Figma File Outline Scan

Use this skill to identify the right Figma node to work from before fetching detailed subtree JSON.

Default outcome: a small candidate list of pages, frames, or node IDs suitable for downstream implementation work.

## Inputs this skill can work from

- A Figma file URL
- A Figma node URL
- A raw Figma `file_key`
- Optional user guidance such as a page name, frame name, feature name, or text visible in the design
- Optional token or scope notes when the user is troubleshooting API access

## Required input contract

Provide one of the following:

- `figma_file_url`
- `figma_node_url`
- `file_key`

Optional targeting hints:

- `page_name`
- `frame_name`
- `node_name_query`
- `feature_description`

## Workflow

1. Normalize the target.
   - Parse the Figma URL if the user provided one.
   - Extract `file_key` and, when present, `node_id`.
   - If the request already identifies a single node clearly, return that fact and skip broad scanning.
2. Confirm the cheapest viable API path.
   - Prefer `GET /v1/files/:key/meta` when only file-level metadata is needed.
   - Prefer `GET /v1/files/:key` with a shallow `depth` when the goal is to inspect the page and top-level frame outline.
   - Do not fetch the entire file tree unless the user explicitly asked for it and the scope is small.
3. Scan first, then narrow.
   - Use a shallow document read to identify likely pages and top-level frames.
   - Match candidate nodes using page names, frame names, nearby text, or user-supplied feature intent.
   - Keep the output compact and oriented around candidate node IDs, not raw JSON.
4. Rank candidate nodes.
   - Prefer exact frame-name matches first.
   - Next prefer candidates on the most relevant page or near relevant visible text.
   - If multiple candidates remain plausible, keep the list short and explain the discriminating difference.
5. Prepare downstream handoff.
   - Return the best candidate node IDs and enough context for a follow-on fetch.
   - Recommend `figma-node-context-fetch` when the user wants the actual implementation subtree.

## API guidance

- Primary endpoints:
  - `GET /v1/files/:key/meta`
  - `GET /v1/files/:key`
- Preferred query pattern:
  - use `depth=1` or `depth=2` to inspect pages and top-level objects without pulling the full file
- Use `ids` only when the target node is already known and the scan is really just a confirmation step.
- Treat this as a low-token discovery step. Avoid returning large node payloads.

## Output contract

Return a concise scan summary with:

- normalized `file_key`
- extracted `node_id` if the user already supplied one
- likely pages or frames
- a ranked list of candidate node IDs
- the reason each candidate is plausible
- the recommended next node to fetch, if one stands out
- any ambiguity or missing information that still blocks a precise pick

## Writing rules

- Prefer a compact outline over raw document dumps.
- Preserve uncertainty explicitly when multiple frames look plausible.
- Keep the scan result reusable by downstream skills.
- Do not infer responsive behavior, interaction logic, or implementation details from the outline alone.

## Gotchas

- Full-file JSON is usually too large and noisy for first-pass discovery.
- A file may contain repeated frame names across pages; always include the page context.
- A node URL can already contain the exact `node_id`; do not rescan the whole file if the user has already provided the target.
- Top-level canvases may appear in file responses even when `ids` are used. Do not mistake that quirk for a signal that the scan failed.
- If authentication fails, report the likely missing scope or expired token instead of pretending the file is empty.

## Final response

- Return the best candidate node or short candidate list.
- Include the normalized identifiers needed by downstream skills.
- State whether the next step should be `figma-node-context-fetch` or whether the user needs to disambiguate between candidates first.
