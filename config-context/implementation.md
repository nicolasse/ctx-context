# Config Context — Implementation

## Current State

Fully implemented as two ctx skills (`export` and `pull-repos`). Both are declarative SKILL.md files — no runtime code, the LLM executes the logic directly via bash/git commands.

## Key Files

| Repo | Path | What it does |
|---|---|---|
| `ctx` | `skills/export/SKILL.md` | Scans workspace, collects git remotes, writes config.json |
| `ctx` | `skills/pull-repos/SKILL.md` | Reads config.json, clones missing repos |

## Flows

### UC-001: Export workspace

1. Ensure `_ctx/` directory exists
2. List directories at workspace root, skip `_ctx/`, `node_modules/`, `.git/`
3. For each directory with a `.git` folder, run `git -C {dir} remote get-url origin`
4. Skip repos with no remote (warn user)
5. Ask user for context repo GitHub URL (optional)
6. If `_ctx/config.json` exists, read it and preserve existing `context` value unless user provided a new one
7. Replace `repos` array entirely with fresh scan results (URL strings only)
8. Sort repos alphabetically by URL
9. Write `_ctx/config.json` with structure: `{ "context": "...", "repos": ["url1", "url2"] }`
10. Print summary: context URL, repos found, skipped directories

### UC-002: Pull repos

1. Read and parse `_ctx/config.json`
2. If file missing, no `repos` key, or empty array → stop with message
3. For each URL: derive directory name (last path segment, minus `.git`), check if it exists at workspace root
4. If exists → skip; if not → `git clone {url}` (git derives directory name automatically)
5. If clone fails → log error, continue with remaining repos
6. Print summary: cloned, skipped, failed

## Data Model

Single file: `_ctx/config.json`

```json
{
  "context": "{github_url_or_empty_string}",
  "repos": [
    "{git_remote_url}"
  ]
}
```

- `context`: string — GitHub URL for the context repo, or `""` if not yet set
- `repos[]`: string — git clone URL from `git remote get-url origin`

## Known Limitations

- No validation of URL format — any string from `git remote get-url origin` is accepted
- No deduplication check — if two directories point to the same remote, both are included
- Directory name is derived from the URL at clone time — if a repo was cloned under a different directory name, pull-repos won't detect the mismatch
- No schema validation when reading config.json in pull-repos

## Recent Changes

- 2026-03-25: Added `_ctx/README.md` creation to init and export skills
- 2026-03-25: Renamed `context/` directory references to `_ctx/` across all skills and docs
- 2026-03-24: Simplified repos schema from `{ name, url }` objects to plain URL strings
- 2026-03-24: Initial implementation mapping
