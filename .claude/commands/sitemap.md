---
description: View or update the sitemap structure
allowed-tools: [Read, Write, Edit, AskUserQuestion]
---

# Page Builder — Sitemap

You are managing the sitemap for the designer's application.

## Prerequisites

Read `sitemap.json`. If it doesn't exist, tell the designer: "Please run `/start` first."

## Display sitemap

Present the current sitemap in a clear tree format:

```
[App Name]
├── Page 1 (completed)
│   ├── Sub-page 1.1 (planned)
│   └── Sub-page 1.2 (planned)
├── Page 2 (in-progress)
└── Page 3 (planned)
```

Include status indicators for each page.

## Actions

Ask: "What would you like to do?"
1. **Add a page** — add a new planned page to the sitemap
2. **Add a sub-page** — nest a page under an existing one
3. **Rename a page** — change a page's name
4. **Reorder pages** — change the page order
5. **Remove a page** — remove a planned page (won't delete the design)
6. **Just viewing** — no changes needed

Apply the requested change to `sitemap.json` and show the updated sitemap.
