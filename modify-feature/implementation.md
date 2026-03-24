# Modify Feature — Implementation

## Current State

Initial implementation. One skill and one agent, following the same patterns as `add-use-case` + `feature-implementer`.

## Key Files

| Repo | Path | What it does |
|---|---|---|
| `ctx` | `skills/modify-feature/SKILL.md` | Skill: resolves feature, loads context, spawns agent, validates, updates context files |
| `ctx` | `agents/feature-modifier.md` | Agent: receives change description + full context, implements code changes |

## Flows

### UC-001: Modify existing behavior

1. User runs `/ctx:modify-feature` and describes the change
2. [skill] Resolves feature name from `context/` directories
3. [skill] Loads product.md + engineering.md + implementation.md
4. [skill] Clarifies change with user (2-3 questions max)
5. [skill → feature-modifier agent] Spawns agent with change description + all three context files
6. [agent] Plans changes using implementation.md as file map
7. [agent] Implements code changes, preserving unrelated behavior
8. [agent] Reports back: what changed, what was preserved, what context needs updating
9. [skill] Validates against product + engineering contracts
10. [skill] Updates context files to reflect new state
11. [skill] Hands off — no commit

### UC-002: Refactor within contracts

Same flow as UC-001, but the agent preserves product contracts entirely and only changes engineering/implementation aspects.

## Data Model

No data model. All state lives in context markdown files.

## Known Limitations

- No automatic rollback if the agent makes a mistake — developer must review
- No diff preview before agent starts implementing

## Recent Changes

- 2026-03-24: Initial implementation
