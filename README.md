# EDB Initial Skills

This repository packages the skills in `.github/skills/` as one APM-installable plugin.

The installable source lives under `.apm/skills/`, which is the canonical APM primitive layout.

## Direct Install

Install the plugin from a local checkout:

```sh
apm install . --target codex
```

From another repository, install the package directly:

```sh
apm install aarondjudzman/edb-initial-skills --target codex
```

## Marketplace Artifacts

The root `apm.yml` is also an APM marketplace authoring config that indexes this repository as one plugin.

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
