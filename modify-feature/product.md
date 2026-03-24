# Modify Feature

A skill + agent that lets developers describe how an existing feature should change, then implements those changes in code while preserving everything that shouldn't change. The developer writes the desired change in conversation; the system loads the feature's full context and delegates to a specialized agent.

## Use Cases

- **UC-001: Modify existing behavior** — Developer describes what should change in an existing feature. The skill loads all three context files, spawns a feature-modifier agent that implements the change, validates against contracts, and updates context files.
- **UC-002: Refactor within contracts** — Developer requests a structural change (e.g., "extract this into a separate service") that doesn't change product behavior but changes engineering/implementation. The agent preserves product contracts while restructuring code.

## Business Rules

- The feature must already be fully mapped (all three context files exist)
- Changes are described in conversation, not by editing context files directly
- The agent receives the full context (product + engineering + implementation) to understand what exists
- Existing behavior not mentioned in the change request must be preserved
- Context files are updated after code changes to reflect the new state
- No commits — developer reviews everything

## Out of Scope

- Creating new features from scratch (use `add-feature`)
- Adding entirely new use cases (use `add-use-case`)
- Mapping existing features (use `map-*` skills)
