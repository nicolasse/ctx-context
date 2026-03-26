# ctxProject — Context Map

> Auto-generated. Last updated: 2026-03-24
> Repos analyzed: ctx

---

## 1. Domain Glossary

### Workspace
- **What it is**: A directory containing one or more git repos and a `_ctx/` directory for feature documentation
- **Possible states**: Not initialized, initialized (has WORKSPACE.md), indexed (has index.md)
- **Where it lives**: Filesystem root directory
- **Consumed by**: All skills (discovery), agents (exploration scope)

### Feature
- **What it is**: A product capability that spans one or more repos, documented by three context files
- **Possible states**: Unmapped (exists in code only), partially mapped, fully mapped (all three context files)
- **Where it lives**: `_ctx/{feature-name}/` (product.md, engineering.md, implementation.md)
- **Consumed by**: Mapping skills, contract-reviewer agent, feature-implementer agent

### Context Files
- **What it is**: A triplet of markdown documents (product, engineering, implementation) defining a feature's contracts
- **Possible states**: Template, draft, reviewed
- **Where it lives**: `_ctx/{feature-name}/`
- **Consumed by**: Skills (read before implementing), agents (validation and implementation guidance)

### Skill
- **What it is**: A user-invocable command that performs a workflow task via Claude Code
- **Possible states**: Defined, invoked, completed
- **Where it lives**: `ctx/skills/{skill-name}/SKILL.md`
- **Consumed by**: Claude Code runtime, users

### Agent
- **What it is**: A specialized AI worker with scoped tools and instructions, spawned by skills
- **Possible states**: Available, spawned, completed
- **Where it lives**: `ctx/agents/{agent-name}.md`
- **Consumed by**: Skills (for exploration and implementation)

---

## 2. Services

### ctx (Claude Code Plugin)
- **Main responsibility**: Provides skills and agents for managing feature context across multi-repo workspaces
- **Stack**: Markdown skill definitions, Claude Code plugin architecture (v2.0.0)
- **Receives**: User slash commands (`/ctx:init`, `/ctx:map-product`, etc.)
- **Emits**: Generated markdown files (WORKSPACE.md, index.md, product.md, engineering.md, implementation.md, config.json); spawns Explore, contract-reviewer, and feature-implementer agents
- **User configs that affect behavior**: Project name, feature names (kebab-case), GitHub repo URLs
- **Key cross-repo dependencies**: None — self-contained plugin that operates on any workspace

#### Skills Inventory

| Skill | Category | What it does |
|---|---|---|
| `init` | Setup | Discover repos, name project, generate WORKSPACE.md |
| `add-index` | Setup | Deep scan all repos, generate index.md |
| `export` | Sharing | Generate config.json with repo URLs |
| `pull-repos` | Sharing | Clone repos from config.json |
| `map-product` | Mapping | Generate product.md for a feature |
| `map-engineering` | Mapping | Generate engineering.md for a feature |
| `map-implementation` | Mapping | Generate implementation.md for a feature |
| `add-feature` | Implementation | Design + implement new feature end-to-end |
| `add-use-case` | Implementation | Add use case to existing feature |

#### Agents Inventory

| Agent | Mode | What it does |
|---|---|---|
| `contract-reviewer` | Read-only | Validates code against product and engineering contracts |
| `feature-implementer` | Read-write | Implements code changes respecting feature contracts |

---

## 3. Flows

### Flow: Workspace Initialization
- **Trigger**: User runs `/ctx:init`
- **Steps**:
  1. [init] Ask user for project name, rename workspace directory
  2. [init] Create `_ctx/` structure (CLAUDE.md, templates), init git
  3. [init] Discover repos (list directories with `.git`)
  4. [init → Explore agent] Deep scan each repo (tech stack, entry points, communication)
  5. [init] Identify features spanning repos, generate WORKSPACE.md
- **Repos involved**: All workspace repos (discovered dynamically)
- **Related domain terms**: Workspace, Repo, Feature

### Flow: Feature Mapping
- **Trigger**: User runs `/ctx:map-product`, `/ctx:map-engineering`, or `/ctx:map-implementation`
- **Steps**:
  1. [skill] Resolve feature name from user input (semantic matching)
  2. [skill] Load existing context files if any
  3. [skill → Explore agent] Deep scan repos for feature-related code
  4. [skill] Generate context file from exploration + user conversation
  5. [skill] Show user, iterate, write final version
- **Repos involved**: All workspace repos
- **Related domain terms**: Feature, Context Files

### Flow: New Feature Design & Implementation
- **Trigger**: User runs `/ctx:add-feature` with a feature description
- **Steps**:
  1. [add-feature] Load index.md for landscape understanding
  2. [add-feature] Clarify scope with user, create feature directory
  3. [add-feature] Write product.md collaboratively
  4. [add-feature → Explore agent] Discover relevant patterns in repos
  5. [add-feature] Write engineering.md collaboratively
  6. [add-feature → feature-implementer] Implement code across repos
  7. [add-feature → contract-reviewer] Validate against contracts
  8. [add-feature] Write implementation.md, leave changes for developer review
- **Repos involved**: All workspace repos
- **Related domain terms**: Feature, Context Files, Agent

### Flow: Add Use Case
- **Trigger**: User runs `/ctx:add-use-case` for an existing feature
- **Steps**:
  1. [add-use-case] Resolve feature, load all three context files
  2. [add-use-case] Check use case doesn't already exist
  3. [add-use-case → feature-implementer] Implement the new use case
  4. [add-use-case → contract-reviewer] Validate against contracts
  5. [add-use-case] Update all three context files as needed
- **Repos involved**: Feature-specific repos (from implementation.md)
- **Related domain terms**: Feature, Context Files, Agent

### Flow: Workspace Sharing
- **Trigger**: User runs `/ctx:export` or `/ctx:pull-repos`
- **Steps (export)**:
  1. [export] Scan repos, read git remote URLs
  2. [export] Ask user for context repo GitHub URL
  3. [export] Write `_ctx/config.json`
- **Steps (pull-repos)**:
  1. [pull-repos] Read config.json, clone missing repos
- **Repos involved**: All workspace repos
- **Related domain terms**: Workspace, Repo

---

## 4. Dependency Map

| Source | Target | Communication | Context |
|---|---|---|---|
| Skills | Explore agent | Agent spawn | Deep repo scanning |
| `add-feature` | feature-implementer agent | Agent spawn | Code implementation |
| `add-feature`, `add-use-case` | contract-reviewer agent | Agent spawn | Contract validation |
| All skills | `_ctx/` filesystem | File read/write | Context file management |
| `pull-repos` | GitHub | git clone | Repo cloning from config.json |

---

## 5. Additional Details per Repo

### ctx — Plugin Architecture

**Skill pattern**: Each skill is a `SKILL.md` file containing a complete prompt with workflow steps. Skills are declarative — they define what to do, not executable code.

**Agent pattern**: Agents are `.md` files defining role, tools, constraints, and behavioral rules. They're spawned by skills as needed.

**Context file conventions**:
- `product.md`: < 40 lines, no code snippets
- `engineering.md`: < 50 lines, only cross-service contracts
- `implementation.md`: specific file paths, function names, real schemas
- Feature directories: kebab-case naming
- Semantic feature resolution: agents match user input against directory names flexibly

---

## 6. Implementation Notes

### Key Patterns
1. **Contract-driven development** — three-file contract per feature (product/engineering/implementation) as source of truth
2. **Agent composition** — skills define workflows, agents handle deep exploration and implementation
3. **Dynamic discovery** — repos found by scanning for `.git` directories, never hardcoded
4. **Iterative generation** — all context files shown to user for review before writing

### Conventions
- Feature names: kebab-case (`follow-up`, `rag-catalog`)
- Context files: always a triplet (product.md, engineering.md, implementation.md)
- Agents: never commit code — developers decide when to commit
- Exploration: must read actual source code, not just READMEs
