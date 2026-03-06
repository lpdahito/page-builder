---
description: View or update the sitemap structure
allowed-tools: [Read, Write, Edit, AskUserQuestion]
---

# Page Builder — Sitemap

You are managing the sitemap for the designer's application.

## Prerequisites

Read `sitemap.json`. If it doesn't exist, tell the designer: "Please run `/start` first."

## Display sitemap

Present the current sitemap in a clear tree format, grouped by shell:

```
[App Name]

Shells:
  marketing — topNav, footer
  dashboard — sidebar, topBar

Pages:
├── Home (completed) [marketing]
├── Dashboard (completed) [dashboard]
│   ├── Analytics (planned) [dashboard]
│   └── Settings (planned) [dashboard]
├── Login (planned) [no shell]
└── Pricing (planned) [marketing]
```

Include status indicators and shell labels for each page. Show "[no shell]" for standalone pages.

## Actions

Ask: "What would you like to do?"
1. **Add a page** — add a new planned page to the sitemap
2. **Add a sub-page** — nest a page under an existing one
3. **Rename a page** — change a page's name
4. **Reorder pages** — change the page order
5. **Remove a page** — remove a planned page (won't delete the design)
6. **Add/edit a shell** — create a new shell or modify an existing one's blocks
7. **Change a page's shell** — reassign a page to a different shell or none
8. **Just viewing** — no changes needed

### Shell actions

**Add/edit a shell:**
- Ask for the shell name and which blocks it should include
- Each block needs a slot name (e.g. "topNav", "sidebar", "footer") and position (top, bottom, left, right)
- If editing an existing shell, show current blocks and ask what to change
- New shells start with empty `refs`
- Note: changes only affect future page generation, not already-designed pages

**Change a page's shell:**
- Show available shells plus "None (standalone)"
- Update the page's `shell` field in `sitemap.json`
- Note: this doesn't change the page's design — use `/refine` to update the visual layout

Apply the requested change to `sitemap.json` and show the updated sitemap.
