# Shortcut API Notes

## Base and Auth

- Base URL: `https://api.app.shortcut.com/api/v3`
- Auth header: `Shortcut-Token: $SHORTCUT_API_TOKEN`
- Always send `Content-Type: application/json` for JSON requests.
- Quick token check: `GET /member`
- Rate limit: `200 requests/minute`
- Story creation mutates external state, so confirm intent when the user's request is incomplete or ambiguous.

## Story Creation Endpoints

- `POST /stories`: create one story. Requires exactly one of `workflow_state_id` or `project_id`.
- `POST /stories/bulk`: create multiple stories with a top-level `stories` array.
- `POST /stories/from-template`: create one story from a known `story_template_id` plus optional overrides.
- `POST /files`: upload local files after story creation using the new story's `story_id`.
- `POST /story-links`: create a relationship such as `blocks`, `duplicates`, or `relates to` after both story IDs are known.

## Minimal Fields

- `name`: required title.
- `story_type`: set explicitly to `feature`, `bug`, or `chore`.
- `workflow_state_id`: recommended placement field.
- Optional fields commonly worth using: `description`, `owner_ids`, `requested_by_id`, `estimate`, `epic_id`, `iteration_id`, `deadline`, `labels`, `tasks`, `sub_tasks`, and `external_links`.

## Lookup Endpoints

- `GET /member`: resolve the authenticated member for `owner_ids` or `requested_by_id`.
- `GET /workflows`: resolve workflow and workflow state IDs by name.
- `GET /epics`: resolve epic IDs when the user gives an epic name.
- `GET /projects`: resolve project IDs only if the user explicitly wants project-based placement.
- `GET /entity-templates`: resolve story template IDs by exact template name.

## Payload Rules

- Do not send both `workflow_state_id` and `project_id`.
- Prefer `workflow_state_id` because Shortcut is sunsetting projects.
- Send the smallest payload possible and add optional fields only when the user requested them.
- `owner_ids` and `requested_by_id` must be UUIDs.
- Create the story before uploading local files.
- Use `sub_tasks` only when the workspace supports subtasks and the user explicitly asked for them.

## Common Failures

- `400`: schema mismatch or invalid placement field combination.
- `401`: token missing or invalid.
- `404`: referenced workflow, epic, project, template, or story is wrong or not visible.
- `422`: unsupported enum, invalid UUID, or malformed body.
- `429`: too many requests.
