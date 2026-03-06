# Page Builder

A conversational page builder that runs as Claude Code slash commands. Designers answer questions and watch designs appear live in Figma, Pencil, or Paper via MCP servers.

## Commands

- `/start` — Initialize project (tool choice, app type, assets scan)
- `/design-system` — Establish colors, typography, layout
- `/create-page` — Generate 2-3 page variants, pick one
- `/create-component` — Design a component in isolation
- `/refine` — Iteratively adjust a component or section
- `/sitemap` — View/manage page structure

---

## State files

- `design-system.json` — Design tokens (colors, typography, layout, tool choice)
- `sitemap.json` — App name and page tree with status and tool references

---

## Assets

Drop files into `assets/` subfolders (logos, avatars, images, icons, inspiration) and the builder will use them in designs.

---

## Design Requirements

**Keep in mind**: Even if no code is being generated, the designs will eventually be converted into code.
For this reason, the color palette should be chosen from Tailwind CSS v4 and the typography from Google Fonts.

### Design System

When creating page designs, follow these guidelines:

- **Mobile Responsive**: Use Tailwind's responsive sizes/prefixes (`sm:`, `md:`, `lg:`, `xl:`).

- **Light & Dark Mode**: Use `dark:` variants for all colors. Test that all UI elements are visible and readable in both modes.

- **Use Design Tokens**: When design tokens are defined, apply the product's color palette and typography. Otherwise, fall back to `taupe` for neutrals and `lime` for accents.

- **Use Built-in Tailwind CSS Colors**: Avoid defining custom colors. Use Tailwind's built-in color utility classes (e.g., `stone-500`, `lime-400`, `red-600`).

---

## Guardrails

- **Read before writing**: Always read `design-system.json` and `sitemap.json` before referencing their contents. Never assume token values, page names, or settings from memory.
- **Verify assets**: Scan `assets/` subfolders before referencing any file. Never guess asset filenames.
- **Visual verification**: Take a screenshot after designing or modifying a frame to confirm the result matches intent.

---

## Design doc

See `docs/plans/2026-03-05-page-builder-design.md` for the full design.