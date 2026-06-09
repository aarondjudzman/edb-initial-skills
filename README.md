# EDB Initial Skills

This repository packages the skills in `.github/skills/` as one APM-installable plugin.

## Direct Install

Install the plugin from a local checkout:

```sh
apm install ./plugins/edb-initial-skills --target codex
```

## Marketplace Artifacts

The root `apm.yml` is an APM marketplace authoring config that indexes the local plugin under `plugins/edb-initial-skills`.

Regenerate marketplace artifacts after changing plugin metadata:

```sh
apm pack --marketplace=claude,codex
```

This writes:

- `.claude-plugin/marketplace.json`
- `.agents/plugins/marketplace.json`

## Included Skills

- `create-shortcut-story`
- `figma-design-token-extract`
- `figma-file-outline-scan`
- `figma-node-context-fetch`
- `implementation-plan-creation`
- `implementation-plan-execution`
- `read-shortcut-stories`
- `system-design-creation`
- `tdd`
- `update-shortcut-story`
- `user-story-creation`
