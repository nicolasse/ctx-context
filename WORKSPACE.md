# ctxProject

A Claude Code plugin that provides a context layer for multi-repo workspaces — structured product, engineering, and implementation contracts for each feature.

## Repos

| Repo | Tech | What it does |
|---|---|---|
| `ctx` | Markdown / Claude Code Plugin | Defines skills and agents for managing feature context across multi-repo workspaces |

## Connections

Single repo — no inter-service connections. The plugin is designed to be installed into workspaces that contain multiple repos, but the plugin itself is self-contained.

## Features

| Feature | Repos involved | Has context |
|---|---|---|
| Workspace Init | ctx | No |
| Feature Mapping | ctx | No |
| Feature Implementation | ctx | No |
| Workspace Sharing | ctx | No |
