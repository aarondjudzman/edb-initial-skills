---
name: read-shortcut-stories
description: "Read Shortcut stories through the Shortcut REST API. Use when you need to validate SHORTCUT_API_TOKEN, fetch a story by ID, search stories by text or filters, follow pagination, and summarize story details from Shortcut JSON."
argument-hint: "<story id | text query | filter description>"
---

# Read Shortcut Stories

Use this skill to retrieve Shortcut stories with the official REST API and turn the JSON into a concise answer.

## When to Use

- Read a known story by ID.
- Search for stories by text, owner, label, state, project, team, or date.
- Confirm whether an API token works before doing other Shortcut work.
- Summarize a set of Shortcut stories without opening the web UI.

## Prerequisites

- Prefer the user's `SHORTCUT_API_TOKEN` environment variable. Do not ask the user to paste the token into chat unless they explicitly choose to.
- A local `.env` or `.env.local` file is acceptable if the shell loads it and exports `SHORTCUT_API_TOKEN` before any Shortcut API call. Do not assume the file is loaded automatically.
- Use the `Shortcut-Token` header, not the deprecated `token` query parameter.
- Base URL: `https://api.app.shortcut.com/api/v3`
- Keep queries narrow and respect Shortcut's `200 requests/minute` rate limit.

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

2. Choose the narrowest retrieval path.
   - Known story ID: call `GET /stories/{story-public-id}`.
   - Natural-language or search-operator query: call `GET /search/stories`.
   - Structured filtering by owner, workflow state, group, project, dates, label, or archived state: call `POST /stories/search` using [the search template](./assets/stories-search-template.json).

3. Start with a small result set.
   - For search queries, prefer `detail=slim` and `page_size=10` or smaller.
   - Use `--data-urlencode` for the `query` string.
   - Only fetch more pages when the user asks or the first page is clearly insufficient.

4. Expand only the stories that matter.
   - After a search, extract candidate story IDs and fetch individual stories with `GET /stories/{id}` only for the top matches the user likely cares about.
   - Resolve related entities like members, workflows, projects, or epics only if the summary would otherwise be too ambiguous.

5. Return a concise summary, not raw JSON by default.
   - Include story ID, name, story type, workflow state or status, owner IDs or names if resolved, estimate, epic/project/iteration IDs or names if resolved, blocker state, updated timestamp, and `app_url` when present.
   - Call out ambiguity when a search returns multiple plausible matches.

## Retrieval Patterns

### Exact Story

```sh
curl --fail-with-body -sS \
  -H "Content-Type: application/json" \
  -H "Shortcut-Token: $SHORTCUT_API_TOKEN" \
  "https://api.app.shortcut.com/api/v3/stories/123"
```

### Search Stories by Query

```sh
curl --fail-with-body -sS --get \
  -H "Content-Type: application/json" \
  -H "Shortcut-Token: $SHORTCUT_API_TOKEN" \
  --data-urlencode 'query=owner:"alex" label:"backend" state:started' \
  --data 'detail=slim' \
  --data 'page_size=10' \
  "https://api.app.shortcut.com/api/v3/search/stories"
```

### Search Stories by Structured Filters

```sh
curl --fail-with-body -sS \
  -X POST \
  -H "Content-Type: application/json" \
  -H "Shortcut-Token: $SHORTCUT_API_TOKEN" \
  -d @./assets/stories-search-template.json \
  "https://api.app.shortcut.com/api/v3/stories/search"
```

## Decision Points

- If the user gives a numeric story ID, do not search first.
- If the user gives a fuzzy request like "my started bugs from this week," use `POST /stories/search`.
- If `GET /search/stories` returns many matches, stop after the first page and ask whether to narrow the query unless the user explicitly asked for a broad listing.
- If the user needs full descriptions or comments, fetch the exact stories after identifying candidates instead of returning a large `detail=full` search page.

## Error Handling

- `401 Unauthorized`: invalid or missing token.
- `404`: wrong story ID or resource not visible to the token.
- `429`: rate limit hit; slow down and narrow the query.
- `400` with `maximum-results-exceeded`: narrow the query or switch to `POST /stories/search` with explicit filters.
- `422`: invalid filter shape or unsupported value; inspect the request before retrying.

## Completion Checks

- The response status is successful and matches the intended endpoint.
- The answer includes the specific story or stories the user asked for, not just raw JSON.
- If pagination exists, state whether only the first page was inspected or more pages were followed.
- If some IDs were not resolved to names, say so instead of guessing.

## References

- [Shortcut API notes](./references/shortcut-api.md)
- [Structured search template](./assets/stories-search-template.json)
