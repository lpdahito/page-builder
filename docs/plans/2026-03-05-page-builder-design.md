# Page Builder Design

## Overview

A conversational page builder that runs as Claude Code skills/slash commands. The designer types commands, answers questions, and watches designs appear live in Figma, Pencil, or Paper. Local JSON files track the design system and sitemap. An assets folder provides pre-existing images.

The builder is UI-first, built for designers. Code export is out of scope for now.

## Architecture

```
Designer (CLI)
    |
    v
Claude Code Skills (/start, /design-system, /create-page, etc.)
    |             |
    v             v
Local State       MCP Servers (Figma, Pencil, Paper)
(JSON files)           |
                       v
                  Design Tool UI (live updates)
```

- **Skills** — conversational flow, one per phase
- **Local state** — `design-system.json`, `sitemap.json` in project root
- **MCP adapters** — each skill calls the appropriate MCP server based on tool choice
- **Design tool** — open side-by-side, designs appear in real-time

## Commands

| Command | Purpose |
|---|---|
| `/start` | Initialize project: pick tool, app type, inspiration, scan assets |
| `/design-system` | Establish or update look & feel (colors, typography, layout) |
| `/create-page` | Generate 2-3 page variants, designer picks one |
| `/create-component` | Design a specific component in isolation |
| `/refine` | Rework a selected component or section iteratively |
| `/import` | Browse and import shadcn/ui components or blocks |
| `/sitemap` | View or update the sitemap structure |

## Command Flows

### /start
1. "Which design tool?" -> Figma / Pencil / Paper
2. "What type of app?" -> SaaS, e-commerce, portfolio, landing page, etc.
3. "Can you provide inspiration images?" -> Analyze style DNA + structural patterns
4. Scan `assets/` folder and report what was found
5. Initialize `design-system.json` and `sitemap.json`
6. Transition to `/design-system`

### /design-system
1. "What color palette?" -> Suggest Tailwind palettes, let designer pick or provide custom
2. "Typography?" -> Font families, heading/body sizes
3. "Layout style?" -> Dense vs spacious, rounded vs sharp, minimal vs detailed
4. Apply inspiration image analysis if provided
5. Save to `design-system.json`
6. Generate a design system frame/page in the chosen tool as visual reference

### /create-page
1. "Which page?" -> Or suggest next logical page based on sitemap
2. Ask about key components for the page
3. Generate 2-3 variants in the design tool
4. "Which version do you prefer?" -> Keep chosen, remove others
5. Update `sitemap.json`

### /create-component
1. "What component?" -> Button, card, nav, form, table, etc.
2. Generate component in an isolated frame
3. Show variants (states, sizes) if applicable
4. Once approved, reusable across pages

### /refine
1. Designer describes what to change or points to a specific frame/component
2. Builder makes adjustments in the design tool
3. Iterate until satisfied

### /import
1. Designer searches or browses shadcn/ui registry for components or blocks
2. Results shown with descriptions; designer picks one
3. Preview component code and summarize structure
4. Render the component in the chosen design tool, adapted to the project's design system
5. Designer reviews and can refine, try another variant, or import more

### /sitemap
1. Display current sitemap from `sitemap.json`
2. Allow reordering, renaming, or adding placeholder pages

## Skill Dependencies

```
/start (required first)
   |
   v
/design-system (required before any page/component work)
   |
   |-> /create-page (repeatable)
   |       |-> /refine (repeatable)
   |
   |-> /create-component (repeatable, standalone)
   |       |-> /refine (repeatable)
   |
   |-> /import (repeatable, standalone — browse and import shadcn/ui)
   |       |-> /refine (repeatable)
   |
   |-> /sitemap (available anytime after start)
```

## Assets Folder

```
assets/
  logos/        - brand logos
  avatars/      - user/team photos
  images/       - product photos, hero images, backgrounds
  icons/        - custom icons
  inspiration/  - mood boards, reference screenshots
```

- Auto-detect asset types from subfolder names
- Reference them when generating designs (logo in nav, avatars in testimonials, etc.)
- `inspiration/` subfolder used for style analysis

## Local State Files

### design-system.json
```json
{
  "tool": "pencil",
  "appType": "saas-dashboard",
  "colors": {
    "primary": "indigo-600",
    "secondary": "slate-500",
    "accent": "amber-400",
    "background": "white",
    "surface": "slate-50"
  },
  "typography": {
    "headingFont": "Inter",
    "bodyFont": "Inter",
    "scale": "default"
  },
  "layout": {
    "style": "spacious",
    "corners": "rounded-lg",
    "density": "comfortable"
  },
  "inspirationNotes": "Clean SaaS look, minimal chrome, card-based layout"
}
```

### sitemap.json
```json
{
  "appName": "Acme Dashboard",
  "pages": [
    {
      "id": "login",
      "name": "Login",
      "status": "completed",
      "toolRef": "frame-id-or-node-id",
      "children": []
    }
  ]
}
```

- `toolRef` links each page to its frame/node in the design tool
- `status`: planned -> in-progress -> completed
- `children` allows nesting for sub-pages

## MCP Tool Mapping

| Action | Pencil | Figma |
|---|---|---|
| Create frame/page | `batch_design` (Insert) | `generate_figma_design` |
| Read existing design | `batch_get` | `get_design_context` |
| Get style guide | `get_style_guide` + `get_guidelines` | `get_variable_defs` |
| Set design tokens | `set_variables` | N/A (embedded in design) |
| Screenshot for review | `get_screenshot` | `get_screenshot` |
| Check layout | `snapshot_layout` | `get_design_context` |
| Insert image/asset | `batch_design` (Generate image) | `generate_figma_design` |

### Paper MCP Tool Mapping

| Action | Paper |
|---|---|
| Create frame/page | `create_artboard` + `write_html` |
| Read existing design | `get_tree_summary`, `get_children`, `get_node_info`, `get_jsx` |
| Check fonts | `get_font_family_info` |
| Screenshot for review | `get_screenshot` |
| Check layout | `get_tree_summary`, `get_computed_styles` |
| Modify styles | `update_styles` |
| Update text | `set_text_content` |
| Duplicate elements | `duplicate_nodes` |
| Delete elements | `delete_nodes` |
| Finish editing | `finish_working_on_nodes` (mandatory when done) |

Paper uses HTML for content creation via `write_html`. Key workflow notes:
- Write small, write often — one visual group per `write_html` call
- Screenshot after every 2-3 modifications for review
- Always call `get_font_family_info` before using fonts for the first time
- Always call `finish_working_on_nodes` when done editing

Before generating designs, the builder should:
- Call `get_guidelines` with relevant topic based on app type
- Call `get_style_guide_tags` -> `get_style_guide` for design inspiration
- Use screenshots throughout to validate work and show the designer

## Out of Scope (for now)
- Code export
- Web UI for the builder
