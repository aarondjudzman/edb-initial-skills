---
name: create-shortcut-story
description: "Create Shortcut stories with the REST API. Use for filing a new feature, bug, or chore; assigning owners; setting workflow state, epic, labels, tasks, or templates; and returning the created story summary."
argument-hint: "<story request | story details>"
---

# Create Shortcut Story

Use this skill to create one or more Shortcut stories with the official REST API and summarize the result.

## When to Use

- Create a new Shortcut story from natural language.
- File a bug, chore, or feature in Shortcut.
- Create a story with owners, labels, tasks, epic, iteration, or deadline.
- Create multiple stories only when the user explicitly asks for bulk creation.
- Create a story from a known Shortcut story template.

## Prerequisites

- Prefer the user's `SHORTCUT_API_TOKEN` environment variable. Do not ask the user to paste the token into chat unless they explicitly choose to.
- A local `.env` or `.env.local` file is acceptable if the shell loads it and exports `SHORTCUT_API_TOKEN` before any Shortcut API call. Do not assume the file is loaded automatically.
- Use the `Shortcut-Token` header, not the deprecated `token` query parameter.
- Base URL: `https://api.app.shortcut.com/api/v3`
- This skill mutates external state. If the request is incomplete or intent is ambiguous, stop and clarify before sending a `POST` request.

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

2. Collect the minimum creation inputs before posting.
   - Require a story name.
   - Require exactly one placement field: `workflow_state_id` or `project_id`.
   - Prefer `workflow_state_id`; Shortcut recommends it because projects are being sunset.
   - If story type is not specified, default to `feature` and say that you made that assumption.
   - If owner or requester is not specified, omit those fields unless the user explicitly says `assign to me`, `request by me`, or gives concrete member IDs.
   - If description, tasks, labels, epic, iteration, deadline, estimate, external links, or subtasks are unclear, omit them instead of guessing.

3. Resolve only the IDs you need.
   - `assign to me` or `request by me`: call `GET /member` and use the returned member ID.
   - Workflow state name like `To Do` or `In Progress`: call `GET /workflows` and match the exact state name. If multiple matches are plausible, ask.
   - Exact epic ID: use it directly. Epic name only if you can resolve it unambiguously with `GET /epics`.
   - Exact project ID: use it directly. Only fall back to `project_id` when the user explicitly wants project placement and no workflow state is given.
   - Exact story template ID: use it directly. Template name only if you can resolve one exact match with `GET /entity-templates`.
   - If the user names a label, iteration, or member and you cannot resolve it confidently from current context, ask instead of guessing.

4. Build the smallest valid payload.
   - Start from [the minimal payload template](./assets/story-create-template.json).
   - Add only fields the user requested or you explicitly confirmed.
   - A normal minimal payload is `name`, `story_type`, and `workflow_state_id`.
   - Do not send both `workflow_state_id` and `project_id`; Shortcut rejects that request.
   - Use `owner_ids` and `requested_by_id` only when you have exact UUIDs.
   - For subtasks, use `sub_tasks` only when the user explicitly asks.
   - For multiple stories, use `POST /stories/bulk` only when the user explicitly asks for multiple items.
   - For template-based creation, use `POST /stories/from-template` only when `story_template_id` is known.

5. Create the story with the narrowest matching endpoint.

   ```sh
   curl --fail-with-body -sS \
     -X POST \
     -H "Content-Type: application/json" \
     -H "Shortcut-Token: $SHORTCUT_API_TOKEN" \
     -d '{"name":"Example story","story_type":"feature","workflow_state_id":123}' \
     "https://api.app.shortcut.com/api/v3/stories"
   ```

   - Bulk create: `POST /stories/bulk` with a `stories` array.
   - Template create: `POST /stories/from-template` with `story_template_id` plus any overrides.
   - If the user wants file attachments from local paths, create the story first, then upload files separately to `POST /files` using the new `story_id`.

6. Return a concise summary, not raw JSON by default.
   - Include story ID, name, story type, workflow state ID or resolved state name, owner IDs or names if resolved, estimate, epic/project/iteration IDs or names if resolved, blocker state, updated timestamp, and `app_url` when present.
   - If multiple stories were created, list each one briefly.
   - Call out any fields you intentionally omitted or any assumptions you made.

## Decision Points

- If the user gives a fully specified request with clear intent to create, do not ask extra questions.
- If the user gives a title but no placement field, ask for the target workflow state instead of guessing.
- If the user gives both `workflow_state_id` and `project_id`, remove `project_id` unless the user explicitly insists on project-based placement.
- If the user asks to `use the template` but only provides a vague name, resolve it only if one exact template match exists; otherwise ask for a narrower name or the template ID.
- If the user asks for an exploratory or tentative action like `draft a Shortcut story`, confirm the final payload before sending the mutating request.
- If the user wants a blocker relationship to another story, create the story first if needed, then add the relationship with `POST /story-links` unless both story IDs are already known up front.

## Error Handling

- `401 Unauthorized`: invalid or missing token.
- `404`: wrong or inaccessible workflow, epic, project, template, or story ID.
- `400`: schema mismatch, or both/neither `workflow_state_id` and `project_id` provided.
- `422`: invalid field shape or unsupported value; inspect the response body and repair the payload before retrying.
- `429`: rate limit hit; slow down and avoid unnecessary lookup calls.

## Completion Checks

- Auth succeeded against the intended Shortcut workspace.
- The request payload includes a story name and exactly one placement field.
- Any resolved IDs were exact matches or explicitly confirmed by the user.
- The response status is successful and matches the intended creation endpoint.
- The answer includes the created story or stories with key fields and `app_url`, not just raw JSON.
- Any assumptions, defaults, or omitted optional fields are called out.

## References

- [Shortcut API notes](./references/shortcut-api.md)
- [Minimal payload template](./assets/story-create-template.json)
