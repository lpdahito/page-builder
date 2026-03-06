# Page Shells Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Add shell/layout awareness so pages share consistent headers, sidebars, and footers based on which shell they belong to.

**Architecture:** Shells are metadata in `sitemap.json` with reference snapshots. Three command files are modified: `/start` (suggest shells), `/create-page` (assign shell, replicate blocks), `/sitemap` (manage shells). No new files created beyond the command changes.

**Tech Stack:** Claude Code slash commands (markdown skill files), JSON state files.

---

### Task 1: Update `/start` command — add shell suggestion step

**Files:**
- Modify: `.claude/commands/start.md`

**Step 1: Read current file**

Read `.claude/commands/start.md` to confirm current content matches expectations.

**Step 2: Add shell suggestion between Step 2 (app type) and Step 3 (inspiration)**

Insert a new step after app type selection. Renumber subsequent steps. The new step should be:

```markdown
## Step 3: Page shells

Based on the app type, suggest default shells:

| App Type | Suggested Shells |
|---|---|
| SaaS dashboard | `marketing` (topNav + footer), `dashboard` (sidebar + topBar) |
| E-commerce store | `marketing` (topNav + footer), `account` (topNav + sidebar) |
| Portfolio / personal site | `marketing` (topNav + footer) |
| Landing page | `marketing` (topNav + footer) |
| Mobile app | `app` (bottomTabs + topBar) |
| Blog / content site | `marketing` (topNav + footer) |
| Admin panel | `admin` (sidebar + topBar) |

Present the suggestions and ask:

"Most apps share common layouts across pages — like a marketing site with a navbar and footer, or a dashboard with a sidebar. I'd suggest these shells for your app:

[list shells with their blocks]

Want to keep these, modify them, or skip shells for now?"

Wait for their answer. The designer can:
- Keep the defaults
- Add, remove, or rename shells
- Change which blocks a shell includes
- Skip shells entirely (no shells array will be created)
```

**Step 3: Update Step 5 (Initialize state files) — add shells to sitemap.json**

Update the `sitemap.json` template in the initialization step:

```markdown
Create `sitemap.json`:

\`\`\`json
{
  "appName": "",
  "shells": [],
  "pages": []
}
\`\`\`

If the designer chose shells in Step 3, populate the `shells` array:

\`\`\`json
{
  "appName": "",
  "shells": [
    {
      "id": "<shell-slug>",
      "name": "<Shell Name>",
      "blocks": [
        { "slot": "<block-name>", "position": "<top|bottom|left|right>" }
      ],
      "refs": {}
    }
  ],
  "pages": []
}
\`\`\`
```

**Step 4: Verify the full file reads correctly**

Read the modified file end-to-end and confirm step numbering is correct (Steps 1-7 after the insertion).

**Step 5: Commit**

```bash
git add .claude/commands/start.md
git commit -m "feat: add shell suggestion step to /start command"
```

---

### Task 2: Update `/create-page` command — add shell assignment and reference replication

**Files:**
- Modify: `.claude/commands/create-page.md`

**Step 1: Read current file**

Read `.claude/commands/create-page.md` to confirm current content.

**Step 2: Add shell assignment to Step 1**

After "Which page would you like to build?" and the designer's answer, add shell assignment:

```markdown
### Shell assignment

Read `sitemap.json` for available shells. If shells exist, ask:

"Which layout should this page use?"
- **[Shell Name]** — [list blocks, e.g. "topNav + footer"]
- ...
- **None** — standalone page, no shared layout (good for login, signup, error pages)

Wait for their answer. Record the chosen shell ID (or `null` for standalone).
```

**Step 3: Add reference replication logic to Step 4 (Generate variants)**

Insert shell-aware generation instructions before the tool-specific variant generation. Add this block at the beginning of Step 4:

```markdown
### Shell block handling

If the page has a shell assigned:

1. Look up the shell in `sitemap.json`
2. Check if `refs` is populated (has node IDs for the shell's blocks)

**If refs are empty (first page in this shell):**
- Generate the shell blocks (nav, sidebar, footer, etc.) as part of each variant
- After the designer chooses a variant and before cleanup, record the node IDs of each shell block in the shell's `refs` object in `sitemap.json`
- Use the design system tokens and app type to inform the block design

**If refs are populated (subsequent pages):**
- For each ref in the shell, read the reference block from the design tool:
  - **Pencil:** `mcp__pencil__batch_get` with the node ID, then `mcp__pencil__get_screenshot` of the node
  - **Figma:** `mcp__figma__get_design_context` with the node ID, then `mcp__figma__get_screenshot`
  - **Paper:** `mcp__paper__get_node_info` and `mcp__paper__get_jsx` for the node, then `mcp__paper__get_screenshot`
- If a ref read fails (node deleted or missing), fall back to generating the block fresh from the shell definition + design system
- Replicate the reference blocks in each variant, then generate only the unique page content in the remaining content area

**If shell is null (standalone):**
- Generate the full page with no shared blocks, as currently done
```

**Step 4: Update Step 6 (Clean up and save) — save refs and shell field**

Update the sitemap update section:

```markdown
Update `sitemap.json` — add the new page with its shell reference:

\`\`\`json
{
  "id": "<page-slug>",
  "name": "<Page Name>",
  "shell": "<shell-id-or-null>",
  "status": "completed",
  "toolRef": "<frame-id-or-node-id>",
  "children": []
}
\`\`\`

If this was the first page in a shell (refs were empty), also update the shell's `refs` in `sitemap.json` with the node IDs of each shell block from the chosen variant:

\`\`\`json
"refs": {
  "topNav": "<node-id>",
  "footer": "<node-id>"
}
\`\`\`
```

**Step 5: Verify the full file reads correctly**

Read the modified file end-to-end and confirm all steps flow logically.

**Step 6: Commit**

```bash
git add .claude/commands/create-page.md
git commit -m "feat: add shell assignment and reference replication to /create-page"
```

---

### Task 3: Update `/sitemap` command — add shell management actions

**Files:**
- Modify: `.claude/commands/sitemap.md`

**Step 1: Read current file**

Read `.claude/commands/sitemap.md` to confirm current content.

**Step 2: Add shell display to the sitemap view**

Update the "Display sitemap" section to show shells and which pages belong to each:

```markdown
## Display sitemap

Read `sitemap.json`. Present the current sitemap in a clear tree format, grouped by shell:

\`\`\`
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
\`\`\`

Include status indicators and shell labels for each page. Show "[no shell]" for standalone pages.
```

**Step 3: Add shell management actions**

Update the Actions section to include shell-related options:

```markdown
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
```

**Step 4: Verify the full file reads correctly**

Read the modified file end-to-end and confirm all actions are numbered correctly.

**Step 5: Commit**

```bash
git add .claude/commands/sitemap.md
git commit -m "feat: add shell management to /sitemap command"
```

---

### Task 4: Update design doc and CLAUDE.md

**Files:**
- Modify: `docs/plans/2026-03-05-page-builder-design.md`
- Modify: `CLAUDE.md`

**Step 1: Update the main design doc**

Add shells to the `sitemap.json` example in the design doc. Update the example to include the `shells` array and the `shell` field on pages:

```json
{
  "appName": "Acme Dashboard",
  "shells": [
    {
      "id": "marketing",
      "name": "Marketing",
      "blocks": [
        { "slot": "topNav", "position": "top" },
        { "slot": "footer", "position": "bottom" }
      ],
      "refs": {}
    }
  ],
  "pages": [
    {
      "id": "login",
      "name": "Login",
      "shell": null,
      "status": "completed",
      "toolRef": "frame-id-or-node-id",
      "children": []
    }
  ]
}
```

**Step 2: Update CLAUDE.md**

Add a brief mention of shells in the State files section:

```markdown
- `sitemap.json` — App name, shells (shared layouts), and page tree with status and tool references
```

**Step 3: Commit**

```bash
git add docs/plans/2026-03-05-page-builder-design.md CLAUDE.md
git commit -m "docs: update design doc and CLAUDE.md with shell references"
```
