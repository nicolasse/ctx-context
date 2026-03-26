# Config Context

Workspace distribution file (`_ctx/config.json`) that lets developers share and recreate multi-repo workspaces. Generated from the local filesystem, consumed to clone repos elsewhere.

## Use Cases

- **UC-001: Export workspace** — Scan repos at workspace root, collect git remote URLs, and write `config.json` with repo list and optional context repo link.
- **UC-002: Pull repos** — Read `config.json` and clone any repos not already present at the workspace root.

## Business Rules

- Repos without a git remote origin are skipped during export (with a warning)
- Export replaces the entire repos array — filesystem is the source of truth
- Existing `context` URL is preserved when re-exporting
- Repos are sorted alphabetically by URL
- Pull-repos skips directories that already exist (never overwrites)
- The `context` field is optional and can be empty

## Out of Scope

- Repo version pinning (branches, tags, commits)
- Automatic sync or pull of existing repos
- Private repo authentication
