# Page Builder

A conversational page builder that runs as Claude Code slash commands. Designers answer questions and watch designs appear live in Figma or Pencil via MCP servers.

## Commands

- `/start` — Initialize project (tool choice, app type, assets scan)
- `/design-system` — Establish colors, typography, layout
- `/create-page` — Generate 2-3 page variants, pick one
- `/create-component` — Design a component in isolation
- `/refine` — Iteratively adjust a component or section
- `/sitemap` — View/manage page structure

## State files

- `design-system.json` — Design tokens (colors, typography, layout, tool choice)
- `sitemap.json` — App name and page tree with status and tool references

## Assets

Drop files into `assets/` subfolders (logos, avatars, images, icons, inspiration) and the builder will use them in designs.

## Design doc

See `docs/plans/2026-03-05-page-builder-design.md` for the full design.
