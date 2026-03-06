# Page Shells Design

## Overview

Pages in most applications share common layout regions — headers, sidebars, footers — that repeat across multiple pages. These shared layouts are called "shells." A SaaS app typically has a marketing shell (topNav + footer) and a dashboard shell (sidebar + topBar). Some pages (login, signup) have no shell at all.

This design adds shell awareness to the page builder so that shared blocks are generated consistently across pages without introducing component-instance complexity.

## Approach

Shells are **metadata labels with reference snapshots** — not linked component instances.

- Each shell defines a set of named, positioned blocks (e.g. topNav at top, sidebar at left)
- Pages reference a shell by ID, or use `null` for standalone layouts
- The first page created in a shell establishes the reference design for each block
- Subsequent pages in the same shell replicate those blocks by reading the reference
- Updating a shared block does not auto-propagate to existing pages; use `/refine` for that

This keeps the architecture simple: each page remains a standalone frame in the design tool.

## Data Model

Shells live in `sitemap.json` alongside pages:

```json
{
  "appName": "Acme",
  "shells": [
    {
      "id": "marketing",
      "name": "Marketing",
      "blocks": [
        { "slot": "topNav", "position": "top" },
        { "slot": "footer", "position": "bottom" }
      ],
      "refs": {}
    },
    {
      "id": "dashboard",
      "name": "Dashboard",
      "blocks": [
        { "slot": "sidebar", "position": "left" },
        { "slot": "topBar", "position": "top" }
      ],
      "refs": {}
    }
  ],
  "pages": [
    { "id": "home", "name": "Home", "shell": "marketing", "status": "planned", "toolRef": null, "children": [] },
    { "id": "dashboard", "name": "Dashboard", "shell": "dashboard", "status": "planned", "toolRef": null, "children": [] },
    { "id": "login", "name": "Login", "shell": null, "status": "planned", "toolRef": null, "children": [] }
  ]
}
```

### Fields

- **shell.id** — unique identifier
- **shell.name** — display name
- **shell.blocks** — array of `{ slot, position }` where position is `top`, `bottom`, `left`, or `right`
- **shell.refs** — map of slot name to design tool node ID, populated when the first page in the shell is created
- **page.shell** — shell ID or `null` for standalone pages

## Workflow Changes

### /start — Shell suggestion

After the app type is chosen, the builder suggests default shells:

| App Type | Suggested Shells |
|---|---|
| SaaS | `marketing` (topNav + footer), `dashboard` (sidebar + topBar) |
| E-commerce | `marketing` (topNav + footer), `account` (topNav + sidebar) |
| Portfolio | `marketing` (topNav + footer) |
| Landing page | `marketing` (topNav + footer) |

The builder presents the suggestions and asks whether to keep, modify, or skip shells. Shells are written into `sitemap.json` during initialization with empty `refs`.

### /create-page — Shell assignment

After asking "Which page?", the builder asks: "Which layout should this page use?" and lists available shells plus "None (standalone)."

- **First page in a shell:** shared blocks are generated as part of the page, and their tool references are stored in `refs`
- **Subsequent pages:** the builder reads the reference blocks (screenshot + node inspection) and replicates them in the new page
- **Standalone page (shell: null):** no shared blocks, full creative freedom

### /sitemap — Shell management

Two new actions:
- **Add/edit a shell** — create a new shell or modify blocks in an existing one
- **Change a page's shell** — reassign a page to a different shell or none

Changes only affect future page generation, not already-designed pages.

## Reference Snapshot Mechanism

### First page in a shell

1. Generate the shared blocks as part of the page design
2. Record tool-specific node IDs for each block in `refs`:
   - Pencil: node IDs from `batch_design`
   - Figma: node IDs from the generated frame
   - Paper: node IDs from `write_html`

### Subsequent pages

1. Read `refs` from the shell definition
2. For each ref, take a screenshot and inspect node structure (tool-appropriate)
3. Replicate those blocks in the new page, matching layout and style
4. Generate only the unique page content in the remaining content area

### Stale refs

If a referenced node no longer exists:
1. The read/screenshot fails
2. Fall back to generating the block fresh from shell definition + design system
3. Update `refs` to point to the newly created block

## What Doesn't Change

- `design-system.json` — untouched
- `/design-system`, `/create-component`, `/import` — unaffected
- Each page remains a standalone frame — no linked instances across tools

## Trade-offs

- **No auto-propagation:** updating a shared block in one page doesn't update it in others. New pages get the latest version; existing pages are updated via `/refine`.
- **Copy-based consistency:** blocks are visually matched copies, not true instances. Minor drift is possible but acceptable for a design exploration tool.
