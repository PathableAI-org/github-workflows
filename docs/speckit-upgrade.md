# Spec Kit upgrade

Reusable workflow that refreshes managed [Spec Kit](https://github.com/github/spec-kit) project files in the calling repository and opens a pull request when updates are available.

Upstream reference: [Spec Kit upgrade](https://github.github.com/spec-kit/upgrade.html)

## What it does

1. Resolves a Spec Kit release tag (input or latest stable via the GitHub API)
2. Installs `specify-cli` with that tag via `uv`
3. Runs `specify integration upgrade` for each key in `.specify/integration.json` → `installed_integrations`
4. Runs `specify extension update`
5. Opens a PR committing only the configured managed paths (default: `.specify/` and `.agents/skills/`)
6. Writes a step summary and PR body describing the upgrade and managed vs gitignored paths

## Prerequisites

The calling repository must already be a Spec Kit project with:

- `.specify/integration.json` containing `version` and `installed_integrations`

## Required permissions

Callers must grant:

```yaml
permissions:
  contents: write
  pull-requests: write
```

Pass `secrets: inherit` so the reusable workflow can use `GITHUB_TOKEN` for the GitHub API and PR creation.

## Inputs

| Input | Default | Description |
|-------|---------|-------------|
| `specify_version` | `""` | Spec Kit release tag (e.g. `v1.0.5`). Empty means latest stable. |
| `script` | `sh` | Passed to `specify integration upgrade --script` |
| `pr_branch` | `automation/speckit-upgrade` | Branch for the upgrade PR |
| `commit_message` | `chore: upgrade Spec Kit project files` | Commit message |
| `add_paths` | `.specify/` and `.agents/skills/` | Newline-separated paths for create-pull-request |
| `pr_title_prefix` | `chore: upgrade Spec Kit to` | PR title prefix; project version is appended |

## Outputs

| Output | Description |
|--------|-------------|
| `before_version` | Project version from `.specify/integration.json` before upgrade |
| `after_version` | Project version after upgrade |
| `speckit_tag` | Resolved Spec Kit CLI release tag |

## Limitations

- Does **not** update local or source presets outside what `specify` upgrades in managed paths
- Gitignored agent folders (e.g. `.claude/`, `.cursor/skills/`) may be refreshed on the runner but are **not** committed unless you change `add_paths`
- After merging, run `specify integration upgrade <key>` locally if you rely on those integrations
- Schedule and dispatch triggers belong in the **calling** repository, not this reusable workflow

## Caller example

```yaml
name: Upgrade Spec Kit
on:
  schedule:
    - cron: "0 14 * * 1"
  workflow_dispatch:
    inputs:
      specify_version:
        description: "Spec Kit release tag (e.g. v1.0.5). Leave empty for latest stable."
        required: false
        default: ""
        type: string

permissions:
  contents: write
  pull-requests: write

jobs:
  upgrade:
    uses: PathableAI-org/github-workflows/.github/workflows/speckit-upgrade.yml@v1
    with:
      specify_version: ${{ inputs.specify_version }}
    secrets: inherit
```

Pin `@v1` or a full commit SHA—not a branch name.
