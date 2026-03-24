# Config Context — Engineering

## Architecture

Single JSON file (`context/config.json`) read and written by two ctx skills. No services, no runtime — the LLM executes git commands directly based on declarative SKILL.md instructions.

## Key Interfaces

**config.json schema** — the contract between `export` (writer) and `pull-repos` (reader):

```json
{
  "context": "string",
  "repos": ["string"]
}
```

- `context`: GitHub URL for the context repo (or `""`)
- `repos[]`: git remote origin URL strings — pull-repos derives the directory name from the last path segment (minus `.git` suffix)

Changing this schema breaks both skills.

## Constraints

- Each URL in `repos` must be a valid git clone URL — no validation is performed, failures surface at clone time
- Directory name is derived from the URL (last path segment, minus `.git`) — not stored explicitly
- `context/` directory is never treated as a repo — always excluded from scans
- Export is destructive to the repos array — it's a full replacement, not a merge

## Patterns

- Filesystem is source of truth for export; config.json is source of truth for pull-repos
- Skills are declarative (SKILL.md) — no imperative code, the LLM interprets and executes
- Alphabetical sort by URL for deterministic output
- Graceful degradation: skip on failure (no remote, clone error) and continue with the rest
