---
name: update-shortcut-story
description: "Update Shortcut stories with the REST API. Use for changing story fields, moving workflow state, reassigning owners, clearing nullable fields, or applying bulk add/remove updates safely."
argument-hint: "<story id + requested changes | bulk update request>"
---

# Update Shortcut Story

Use this skill to update one or more Shortcut stories with the official REST API and summarize the result.

## When to Use

- Update a known Shortcut story by ID.
- Move a story to another workflow state.
- Change story title, description, estimate, deadline, epic, iteration, owners, followers, labels, or archive state.
- Clear a nullable story field such as `estimate`, `deadline`, `epic_id`, `iteration_id`, or `project_id`.
- Apply one change across multiple stories when the user explicitly asks for a bulk update.

## Prerequisites

- Prefer the user's `SHORTCUT_API_TOKEN` environment variable. Do not ask the user to paste the token into chat unless they explicitly choose to.
- A local `.env` or `.env.local` file is acceptable if the shell loads it and exports `SHORTCUT_API_TOKEN` before any Shortcut API call. Do not assume the file is loaded automatically.
- Use the `Shortcut-Token` header, not the deprecated `token` query parameter.
- Base URL: `https://api.app.shortcut.com/api/v3`
- This skill mutates external state. If the target story or requested change is ambiguous, stop and clarify before sending a `PUT` request.

## Procedure

1. Validate auth first if the token has not been used in the current task or if previous calls failed.

   ```sh
   curl --fail-with-body -sS \
     -H "Content-Type: application/json" \
     -H "Shortcut-Token: $SHORTCUT_API_TOKEN" \
     "https://api.app.shortcut.com/api/v3/member"
   ```

   - If `SHORTCUT_API_TOKEN` is missing, stop and ask the user to set it or load the local `.env` file into the current shell session.
   - If the response is `401`, tell the user the token is missing or invalid before making more Shortcut calls.

2. Identify the exact target story or stories before updating.
   - Known story ID: use it directly.
   - No story ID but an exact title or operator query: resolve the target first with `GET /search/stories` or `POST /stories/search`.
   - If multiple plausible stories match, ask instead of guessing.
   - If the user actually wants to update a story comment or story task, use that resource's dedicated endpoint instead of `PUT /stories/{id}`.

3. Choose the narrowest update path.
   - Single story field update: `PUT /stories/{story-public-id}`.
   - Multiple stories, or additive/removal changes for owners, followers, labels, or custom fields: `PUT /stories/bulk`.
   - Story comment text: `PUT /stories/{story-public-id}/comments/{comment-public-id}`.
   - Story task fields: `PUT /stories/{story-public-id}/tasks/{task-public-id}`.

4. Resolve only the IDs you need.
   - `assign to me` or `request by me`: call `GET /member` and use the returned member ID.
   - Workflow state name like `To Do` or `In Progress`: call `GET /workflows` and match the exact state name.
   - Epic name: resolve with `GET /epics` only if one exact match is clear.
   - Iteration name: resolve with `GET /iterations` only if one exact match is clear.
   - Project change: resolve with `GET /projects` only when the user explicitly wants project reassignment.
   - Label name: resolve with `GET /labels` only when one exact match is clear.
   - If any member, label, epic, iteration, or workflow match is ambiguous, ask instead of guessing.

5. Read current story state before any change that depends on existing arrays.
   - Use `GET /stories/{story-public-id}` before single-story add/remove requests such as `add owner`, `remove label`, `append external link`, or `reorder sub-tasks`.
   - The single-story update endpoint treats list fields like `owner_ids`, `follower_ids`, `labels`, `external_links`, `file_ids`, `linked_file_ids`, and `sub_tasks` as the final desired state.
   - If the user wants additive or removal semantics and you do not want to rebuild final arrays yourself, prefer `PUT /stories/bulk` with one `story_id` and the relevant `*_add` or `*_remove` fields.

6. Build the smallest safe payload.
   - Start from [the minimal single-story template](./assets/story-update-template.json) or [the bulk delta template](./assets/stories-bulk-update-template.json).
   - Add only fields the user requested or you explicitly confirmed.
   - For scalar updates like `name`, `description`, `workflow_state_id`, `story_type`, `estimate`, or `deadline`, send only those fields.
   - Use `null` to clear nullable fields such as `estimate`, `deadline`, `epic_id`, `iteration_id`, `project_id`, `completed_at_override`, or `started_at_override`.
   - For single-story updates, omit list fields unless the user gave the final desired list or you computed it from current state.
   - `sub_tasks` on single-story update is a final ordered list, not an append-only field.
   - Prefer `workflow_state_id` for workflow moves. Omit `project_id` unless the user also intends a project reassignment.
   - Do not send contradictory ordering controls like `move_to` together with `after_id` or `before_id`.

7. Send the update request with the chosen endpoint.

   ```sh
   curl --fail-with-body -sS \
     -X PUT \
     -H "Content-Type: application/json" \
     -H "Shortcut-Token: $SHORTCUT_API_TOKEN" \
     -d '{"name":"Updated title"}' \
     "https://api.app.shortcut.com/api/v3/stories/123"
   ```

   - Bulk update uses `PUT /stories/bulk` with a top-level `story_ids` array.
   - For single-story additive/removal updates, bulk update with one story ID is acceptable when it avoids clobbering list fields.

8. Return a concise summary, not raw JSON by default.
   - Include story ID, name, story type, workflow state ID or resolved state name, owner IDs or names if resolved, estimate, epic/project/iteration IDs or names if resolved, archive state, updated timestamp, and `app_url` when present.
   - If multiple stories were updated, list each one briefly.
   - Call out any fields you intentionally left unchanged, any assumptions you made, and whether you used single-story or bulk update semantics.

## Decision Points

- If the user says `move story 123 to In Progress`, resolve the target state and use `PUT /stories/123` with `workflow_state_id`.
- If the user says `add alice as an owner to story 123`, either fetch the current story and send the final `owner_ids` list, or prefer `PUT /stories/bulk` with `story_ids` plus `owner_ids_add` to avoid overwriting other owners.
- If the user says `remove the backend label`, do not overwrite `labels` blindly on a single-story update. Rebuild the final list or use bulk `labels_remove`.
- If the user says `clear the estimate` or `remove the deadline`, send `null` for that field.
- If the user wants to edit a comment or task on the story rather than the story itself, switch to the dedicated comment or task endpoint.
- If the request sounds exploratory like `draft the Shortcut update`, confirm the final payload before sending the mutating request.

## Error Handling

- `401 Unauthorized`: invalid or missing token.
- `404`: wrong or inaccessible story, workflow state, epic, iteration, project, task, or comment ID.
- `400`: schema mismatch or incompatible ordering fields.
- `422`: invalid field shape, invalid UUID, malformed labels/custom fields, or unsupported enum value.
- `429`: rate limit hit; slow down and avoid unnecessary lookup calls.

## Completion Checks

- Auth succeeded against the intended Shortcut workspace.
- The target story or stories were resolved exactly.
- The endpoint matches the requested update style: single story, bulk story update, comment update, or task update.
- Any additive/removal update to list fields avoided clobbering unrelated existing values.
- The response status is successful and matches the intended update endpoint.
- The answer includes the updated story or stories with key fields and `app_url`, not just raw JSON.
- Any assumptions, defaults, or intentionally unchanged unresolved fields are called out.

## References

- [Shortcut API notes](./references/shortcut-api.md)
- [Minimal single-story payload template](./assets/story-update-template.json)
- [Bulk delta payload template](./assets/stories-bulk-update-template.json)
