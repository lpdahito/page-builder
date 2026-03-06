# Page Builder

A conversational page builder that runs as [Claude Code](https://docs.anthropic.com/en/docs/claude-code) slash commands. Designers answer questions and watch designs appear live in **Figma**, **Pencil**, or **Paper** via MCP servers.

No code, no UI framework — just a CLI conversation that produces real designs, page by page.

## Prerequisites

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) installed and configured
- At least one design tool MCP server connected:
  - **Figma** — [Figma MCP server](https://github.com/nichochar/figma-mcp) with a valid API token
  - **Pencil** — Pencil MCP server with the desktop app running
  - **Paper** — Paper MCP server with the Paper.design app running

## Getting Started

1. **Clone the repo**

   ```bash
   git clone git@github.com:lpdahito/page-builder.git my-design-project
   cd my-design-project
   ```

2. **Open Claude Code** in the project directory

   ```bash
   claude
   ```

3. **Run `/start`** to initialize your project — pick a design tool, app type, and scan assets.

4. **Run `/design-system`** to establish colors, typography, and layout.

5. **Start building pages** with `/create-page`, `/create-component`, and `/refine`.

## Commands

| Command | Purpose |
|---|---|
| `/start` | Initialize project: pick design tool, app type, scan assets |
| `/design-system` | Establish colors, typography, and layout tokens |
| `/create-page` | Generate 2-3 page variants, pick one to keep |
| `/create-component` | Design a component in an isolated frame |
| `/refine` | Iteratively adjust a component or section |
| `/sitemap` | View or update the page structure |

### Typical workflow

```
/start → /design-system → /create-page (repeat) → /refine (as needed)
```

`/create-component` and `/sitemap` can be used anytime after the design system is set up.

## Project Structure

```
my-design-project/
├── .claude/
│   ├── commands/       # Slash command definitions
│   └── skills/         # Design skills (frontend-design, etc.)
├── assets/
│   ├── logos/           # Brand logos
│   ├── avatars/         # User/team photos
│   ├── images/          # Hero images, backgrounds, product photos
│   ├── icons/           # Custom icons
│   └── inspiration/     # Mood boards, reference screenshots
├── docs/plans/          # Design docs
├── design-system.json   # Generated — design tokens (colors, fonts, layout)
├── sitemap.json         # Generated — page tree with status tracking
└── CLAUDE.md            # AI instructions and guardrails
```

### Assets

Drop files into the `assets/` subfolders before or during a session. The builder will detect and use them — logos in navbars, avatars in testimonials, inspiration images to guide style choices.

### State Files

- **`design-system.json`** — Stores the chosen tool, app type, color palette, typography, and layout preferences. Created by `/start` and `/design-system`.
- **`sitemap.json`** — Tracks the app name and page tree. Each page has a status (`planned` → `in-progress` → `completed`) and a reference to its frame in the design tool.

Both files are gitignored since they are generated per-project.

## Architecture

```
Designer (CLI)
    │
    ▼
Claude Code Commands (/start, /design-system, /create-page, ...)
    │              │
    ▼              ▼
Local State       MCP Servers (Figma / Pencil / Paper)
(JSON files)           │
                       ▼
                 Design Tool UI (live updates)
```

## Design Principles

- **Tailwind CSS v4 colors** — Palette is chosen from built-in Tailwind colors for easy code conversion later
- **Google Fonts** — Typography selections use Google Fonts for web compatibility
- **Light & dark mode** — Every design includes both modes
- **Mobile responsive** — Designs use Tailwind responsive breakpoints

## License

MIT
