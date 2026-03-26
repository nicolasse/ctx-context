# Context — Agent Instructions

This directory contains the product, engineering, and implementation context for each feature in the system. These files are the source of truth that AI agents use to understand, implement, and validate changes.

**Read this file before operating on any feature.**

## Structure

_ctx/
  _template/          # Templates for creating new features
  {feature-name}/
    product.md        # WHAT: product definition, use cases, business rules
    engineering.md    # HOW: technical contracts, interfaces, constraints
    implementation.md # CURRENT STATE: how it's built today, key files, flows

## Conventions

- Directory names use kebab-case: `follow-up/`, `web-chat/`, `voice-pipeline/`
- Each feature MUST have all three files
- `product.md` is the product contract — changes here mean the feature's behavior changes
- `engineering.md` is the technical contract — changes here may be breaking
- `implementation.md` is descriptive, not prescriptive — it reflects reality, not aspirations
- `_template/` contains starter files for new features, do not modify

## For Agents

When resolving a feature name from a user request, list the directories here and match semantically. Users may refer to features by aliases, abbreviations, or in different languages. If uncertain, ask.
