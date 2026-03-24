# Modify Feature — Engineering

## Architecture

Two new files in the `ctx` repo: a skill (`skills/modify-feature/SKILL.md`) and an agent (`agents/feature-modifier.md`). The skill handles feature resolution, context loading, and context file updates. The agent handles code changes. Follows the same caller/agent split as `add-use-case` + `feature-implementer`.

## Key Interfaces

**Skill → Agent contract**: The skill provides the agent with:
- Feature name (resolved)
- Change description (from user conversation)
- Full context: product.md, engineering.md, implementation.md content

**Agent output**: The agent returns:
- Summary of code changes made
- List of files created/modified
- Decisions and assumptions
- Whether product or engineering contracts need updating

## Constraints

- Agent must read all three context files before making any code change
- Agent must not modify context files — only the skill does that after validation
- Agent must preserve behavior not mentioned in the change request
- Follows the same agent `.md` format as `feature-implementer` and `contract-reviewer`

## Patterns

- Skill resolves feature semantically (same as `add-use-case`)
- Skill loads context, spawns agent, validates, updates context — same lifecycle as `add-use-case`
- Agent follows plan → implement → report pattern (same as `feature-implementer`)
