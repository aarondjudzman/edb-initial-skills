# Shortcut API Notes

## Base and Auth

- Base URL: `https://api.app.shortcut.com/api/v3`
- Auth header: `Shortcut-Token: $SHORTCUT_API_TOKEN`
- Always send `Content-Type: application/json` for JSON requests.
- Quick token check: `GET /member`
- Rate limit: `200 requests/minute`

## Story Reading Endpoints

- `GET /stories/{story-public-id}`: fetch one full story by numeric public ID.
- `GET /search/stories`: query-based story search with `query`, `detail`, `page_size`, and `next`.
- `POST /stories/search`: structured filter search for fields such as `archived`, `owner_ids`, `group_ids`, `project_ids`, `label_name`, `story_type`, `workflow_state_id`, `workflow_state_types`, `updated_at_start`, and `updated_at_end`.

## Search Guidance

- Prefer `detail=slim` for lists. Fetch exact stories afterward when you need full descriptions or comments.
- Use `--data-urlencode` for the `query` parameter on `GET /search/stories`.
- If a `next` token is returned from a search endpoint, reuse that exact path and query string for the next page.
- `POST /stories/search` is better for requests like "started bugs updated this week" than free-text search.

## Helpful Related Endpoints

- `GET /members/{member-public-id}`: resolve owner or requester IDs.
- `GET /workflows` or `GET /workflows/{workflow-public-id}`: resolve workflow and state IDs.
- `GET /projects/{project-public-id}`: resolve project IDs.
- `GET /epics/{epic-public-id}`: resolve epic IDs.

## Common Failures

- `401`: token missing or invalid.
- `404`: wrong ID or inaccessible resource.
- `429`: too many requests.
- `400 maximum-results-exceeded`: narrow the search.
- `422`: invalid request body or unsupported filter value.
