# Shortcut API Notes

## Base and Auth

- Base URL: `https://api.app.shortcut.com/api/v3`
- Auth header: `Shortcut-Token: $SHORTCUT_API_TOKEN`
- Always send `Content-Type: application/json` for JSON requests.
- Quick token check: `GET /member`
- Rate limit: `200 requests/minute`
- Story updates mutate external state, so confirm intent when the requested target or change is incomplete or ambiguous.

## Story Update Endpoints

- `PUT /stories/{story-public-id}`: update one story.
- `PUT /stories/bulk`: update one or more stories, including additive/removal operations for some list fields.
- `PUT /stories/{story-public-id}/comments/{comment-public-id}`: update one story comment's text.
- `PUT /stories/{story-public-id}/tasks/{task-public-id}`: update one story task.

## Single Story Update Rules

- Use `PUT /stories/{story-public-id}` for scalar or replacement-style changes like `name`, `description`, `workflow_state_id`, `story_type`, `estimate`, `deadline`, `epic_id`, `iteration_id`, `project_id`, or `archived`.
- Single-story list fields such as `owner_ids`, `follower_ids`, `labels`, `external_links`, `file_ids`, `linked_file_ids`, and `sub_tasks` represent the final desired state.
- `sub_tasks` is not append-only; omitted sub-tasks are unlinked.
- Use `null` to clear nullable fields like `estimate`, `deadline`, `epic_id`, `iteration_id`, `project_id`, `completed_at_override`, or `started_at_override`.
- Prefer `workflow_state_id` for state moves and omit `project_id` unless the project assignment should also change.
- Do not combine contradictory ordering controls such as `move_to` with `after_id` or `before_id`.

## Bulk Update Rules

- `PUT /stories/bulk` requires a top-level `story_ids` array.
- Bulk update supports delta fields like `owner_ids_add`, `owner_ids_remove`, `follower_ids_add`, `follower_ids_remove`, `labels_add`, `labels_remove`, `custom_fields_add`, and `custom_fields_remove`.
- Bulk update is useful even for a single story when the request is phrased as `add/remove` and you want to avoid rebuilding a full final list.
- Bulk update also supports shared scalar changes like `workflow_state_id`, `estimate`, `deadline`, `epic_id`, `iteration_id`, `project_id`, `story_type`, and `archived`.

## Lookup Endpoints

- `GET /stories/{story-public-id}`: fetch current story state before rebuilding list fields.
- `GET /search/stories`: resolve a story from an exact text query.
- `POST /stories/search`: resolve stories from structured filters.
- `GET /member`: resolve the authenticated member for `owner_ids` or `requested_by_id`.
- `GET /workflows`: resolve workflow and workflow state IDs by name.
- `GET /epics`: resolve epic IDs.
- `GET /iterations`: resolve iteration IDs.
- `GET /projects`: resolve project IDs when project reassignment is explicit.
- `GET /labels`: resolve label IDs or exact label names.

## Related Nested Update Endpoints

- `PUT /stories/{story-public-id}/comments/{comment-public-id}` only updates comment text.
- `PUT /stories/{story-public-id}/tasks/{task-public-id}` updates task fields like `description`, `complete`, and `owner_ids`.
- `PUT /story-links/{story-link-public-id}` updates the relationship between two stories.

## Common Failures

- `400`: schema mismatch or conflicting ordering fields.
- `401`: token missing or invalid.
- `404`: referenced story, workflow state, epic, iteration, project, task, or comment is wrong or not visible.
- `422`: unsupported enum, invalid UUID, malformed labels or custom fields, or invalid replacement list shape.
- `429`: too many requests.
