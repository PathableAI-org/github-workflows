# github-workflows

Organization-shared [GitHub Actions](https://docs.github.com/en/actions) library for **reusable workflows** and **composite actions**.

This is callable platform automation—not the org special [`.github`](https://docs.github.com/en/organizations/collaborating-with-groups-in-organizations/creating-a-default-community-health-file) repository. Product repos call workflows from here; they keep their own `schedule` / `workflow_dispatch` wrappers.

## Layout

| Path | Purpose |
|------|---------|
| [`.github/workflows/`](.github/workflows/) | Reusable workflows only (`on: workflow_call`) |
| [`actions/<name>/`](actions/) | Composite actions (`action.yml`) when step sequences are shared |
| [`docs/`](docs/) | Short usage docs per workflow |

Do **not** put product-repo schedule/dispatch wrappers here as the primary artifact. Those stay in consuming repos (or later in org `.github/workflow-templates/`).

## Versioning

- Publish semantic version tags (`v1.0.0`, `v1.0.1`, …).
- After a reviewed release, move the floating major tag (`v1`) to the same commit.
- Callers should pin `@v1` or a full commit SHA—not a branch name.

Example:

```yaml
jobs:
  example:
    uses: PathableAI-org/github-workflows/.github/workflows/<workflow>.yml@v1
```

## Conventions

- **Least privilege:** reusable workflows declare only the `permissions:` they need; callers grant what each workflow documents.
- **Pin third-party actions** by full commit SHA, with a version comment (e.g. `# v7`).
- Prefer composite actions under `actions/` when the same step sequence is reused across workflows.

## Catalog

### Workflows

| Workflow | Description | Docs |
|----------|-------------|------|
| — | None yet | — |

Coming next: Spec Kit project-file upgrade (`speckit-upgrade`).

### Actions

| Action | Description | Docs |
|--------|-------------|------|
| — | None yet | — |

## License

[Unlicense](LICENSE) — public domain dedication.
