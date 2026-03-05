# Page Builder Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Build a conversational page builder as Claude Code slash commands that drive Figma/Pencil MCP servers to create frontend designs.

**Architecture:** Project-level slash commands in `.claude/commands/` that orchestrate a question-driven design workflow. Local JSON files (`design-system.json`, `sitemap.json`) persist state across sessions. Each command reads state, asks questions via conversation, and calls MCP tools to generate designs in real-time.

**Tech Stack:** Claude Code commands (Markdown + YAML frontmatter), MCP servers (Figma, Pencil), JSON state files, Tailwind color system.

---

### Task 1: Project scaffolding

**Files:**
- Create: `.claude/commands/` directory
- Create: `assets/logos/.gitkeep`
- Create: `assets/avatars/.gitkeep`
- Create: `assets/images/.gitkeep`
- Create: `assets/icons/.gitkeep`
- Create: `assets/inspiration/.gitkeep`

**Step 1: Create directory structure**

```bash
mkdir -p .claude/commands
mkdir -p assets/{logos,avatars,images,icons,inspiration}
```

**Step 2: Add .gitkeep files so empty dirs are tracked**

```bash
touch assets/logos/.gitkeep assets/avatars/.gitkeep assets/images/.gitkeep assets/icons/.gitkeep assets/inspiration/.gitkeep
```

**Step 3: Commit**

```bash
git add .claude/ assets/
git commit -m "feat: scaffold project structure with commands dir and assets folders"
```

---

### Task 2: `/start` command

**Files:**
- Create: `.claude/commands/start.md`

**Step 1: Create the start command**

Write `.claude/commands/start.md`:

```markdown
---
description: Initialize a new page builder project
allowed-tools: [Read, Write, Glob, Bash, AskUserQuestion, mcp__pencil__get_editor_state, mcp__pencil__open_document, mcp__pencil__get_guidelines, mcp__pencil__get_style_guide_tags, mcp__pencil__get_style_guide, mcp__figma__whoami, mcp__figma__get_metadata]
---

# Page Builder — Start

You are a conversational page builder that helps designers create frontend designs page by page. You ask questions, and build the interface live in the designer's chosen tool (Figma or Pencil).

## Step 1: Choose design tool

Ask the designer:

"Which design tool would you like to use for this project?"
- **Figma** — designs will be created in your Figma file
- **Pencil** — designs will be created in a .pen file

Wait for their answer.

If they choose Figma, verify the connection by calling `mcp__figma__whoami`.
If they choose Pencil, call `mcp__pencil__get_editor_state` to check the current state. If no document is open, call `mcp__pencil__open_document` with `new` to create a fresh .pen file.

## Step 2: App type

Ask the designer:

"What type of application are you building?"
- SaaS dashboard
- E-commerce store
- Portfolio / personal site
- Landing page
- Mobile app
- Blog / content site
- Admin panel
- Other (describe it)

Wait for their answer.

## Step 3: Inspiration

Ask the designer:

"Do you have any inspiration images or screenshots? You can:
1. Drop them into the `assets/inspiration/` folder
2. Describe the look you're going for
3. Skip this for now"

If they added images to `assets/inspiration/`, use the Read tool to view each image and analyze:
- Color schemes and palettes
- Typography styles
- Layout patterns (grid, card-based, sidebar, etc.)
- Spacing and density
- Corner radius and shape language
- Overall mood (minimal, bold, playful, corporate, etc.)

Summarize what you found from the inspiration images.

## Step 4: Scan assets folder

Use Glob to scan the `assets/` directory for any pre-existing files:
- `assets/logos/*` — brand logos
- `assets/avatars/*` — user/team photos
- `assets/images/*` — product photos, hero images
- `assets/icons/*` — custom icons

Report what you found: "I found X logos, Y avatars, Z images, and W icons. I'll use these in your designs."

If the folders are empty (only .gitkeep), say: "No assets found yet. You can add logos, avatars, and images to the `assets/` folder anytime and I'll incorporate them."

## Step 5: Initialize state files

Create `design-system.json` with the tool choice and app type:

```json
{
  "tool": "<chosen-tool>",
  "appType": "<chosen-app-type>",
  "colors": {},
  "typography": {},
  "layout": {},
  "inspirationNotes": "<summary-from-step-3>"
}
```

Create `sitemap.json`:

```json
{
  "appName": "",
  "pages": []
}
```

Ask the designer: "What's the name of your app/project?"

Update `sitemap.json` with the app name.

## Step 6: Transition

Tell the designer: "Project initialized! Now let's establish your design system. Run `/design-system` to define your colors, typography, and layout preferences."
```

**Step 2: Test the command**

Run `/start` in Claude Code and verify:
- It asks each question one at a time
- It connects to the chosen design tool
- It scans assets folder
- It creates `design-system.json` and `sitemap.json`

**Step 3: Commit**

```bash
git add .claude/commands/start.md
git commit -m "feat: add /start command for project initialization"
```

---

### Task 3: `/design-system` command

**Files:**
- Create: `.claude/commands/design-system.md`

**Step 1: Create the design-system command**

Write `.claude/commands/design-system.md`:

```markdown
---
description: Establish or update the design system (colors, typography, layout)
allowed-tools: [Read, Write, Edit, Glob, AskUserQuestion, mcp__pencil__get_guidelines, mcp__pencil__get_style_guide_tags, mcp__pencil__get_style_guide, mcp__pencil__batch_design, mcp__pencil__batch_get, mcp__pencil__set_variables, mcp__pencil__get_screenshot, mcp__pencil__find_empty_space_on_canvas, mcp__pencil__open_document, mcp__pencil__get_editor_state, mcp__figma__generate_figma_design, mcp__figma__get_screenshot, mcp__figma__get_variable_defs]
---

# Page Builder — Design System

You are establishing the visual design system for a page builder project. Read `design-system.json` first to understand the current state.

## Prerequisites

Read `design-system.json`. If it doesn't exist or has no `tool` field, tell the designer: "Please run `/start` first to initialize your project."

Read the tool choice from `design-system.json`.

## Fetch design guidelines

Based on the tool choice:

**If Pencil:**
- Call `mcp__pencil__get_guidelines` with the topic matching the app type:
  - SaaS/dashboard/admin → `web-app`
  - Landing page → `landing-page`
  - Mobile app → `mobile-app`
  - Other → `web-app`
- Also call with topic `tailwind` for Tailwind-specific guidance
- Also call with topic `design-system` for design system patterns
- Call `mcp__pencil__get_style_guide_tags` to discover available style guides
- Use the tags to call `mcp__pencil__get_style_guide` for design inspiration

**If Figma:**
- Call `mcp__figma__get_variable_defs` if a file is already open, to check for existing tokens

Store any relevant patterns and guidelines internally to inform your design decisions.

## Step 1: Color palette

Ask the designer:

"Let's pick your color palette. Which approach do you prefer?"
1. **Choose from Tailwind palettes** — I'll show you options that match your app type
2. **Provide specific colors** — share hex codes or Tailwind color names
3. **Extract from inspiration** — I'll derive colors from your inspiration images
4. **Surprise me** — I'll pick a palette that fits your app type

Based on their choice, establish:
- `primary` — main brand/action color
- `secondary` — supporting color
- `accent` — highlights and CTAs
- `background` — page background
- `surface` — card/section background

Present the palette and ask for confirmation before proceeding.

## Step 2: Typography

Ask the designer:

"What typography style fits your project?"
1. **Clean & modern** — Inter / Plus Jakarta Sans
2. **Professional** — DM Sans / Outfit
3. **Bold & expressive** — Sora / Space Grotesk
4. **Classic** — Merriweather / Lora (serif)
5. **Custom** — specify your own fonts

Establish heading font, body font, and scale (compact, default, spacious).

## Step 3: Layout style

Ask the designer:

"What layout style do you prefer?"
- **Spacing**: Dense / Comfortable / Spacious
- **Corners**: Sharp (0) / Slightly rounded (md) / Rounded (lg) / Pill (full)
- **Style**: Minimal / Balanced / Detailed

## Step 4: Save and generate

Update `design-system.json` with all choices.

Generate a **design system reference frame** in the chosen tool:
- Color swatches with labels
- Typography samples (H1-H4, body, small)
- Button styles (primary, secondary, outline)
- Card example
- Spacing scale visualization

**If Pencil:**
- Call `mcp__pencil__find_empty_space_on_canvas` to find placement
- Use `mcp__pencil__batch_design` to create the design system frame
- Call `mcp__pencil__set_variables` to store colors and typography as variables
- Call `mcp__pencil__get_screenshot` to show the result

**If Figma:**
- Use `mcp__figma__generate_figma_design` to create the design system page
- Call `mcp__figma__get_screenshot` to show the result

## Step 5: Confirm

Show a screenshot of the generated design system frame.

Ask: "Does this design system look right? I can adjust any of the colors, typography, or layout settings. When you're happy, run `/create-page` to start building pages."
```

**Step 2: Test the command**

Run `/design-system` after `/start` and verify:
- It reads existing state
- Asks questions one at a time
- Generates the design system frame in the tool
- Saves to `design-system.json`
- Shows a screenshot

**Step 3: Commit**

```bash
git add .claude/commands/design-system.md
git commit -m "feat: add /design-system command for establishing look and feel"
```

---

### Task 4: `/create-page` command

**Files:**
- Create: `.claude/commands/create-page.md`

**Step 1: Create the create-page command**

Write `.claude/commands/create-page.md`:

```markdown
---
description: Create a new page with 2-3 design variants
allowed-tools: [Read, Write, Edit, Glob, AskUserQuestion, mcp__pencil__get_guidelines, mcp__pencil__get_style_guide, mcp__pencil__get_style_guide_tags, mcp__pencil__batch_design, mcp__pencil__batch_get, mcp__pencil__get_screenshot, mcp__pencil__find_empty_space_on_canvas, mcp__pencil__snapshot_layout, mcp__pencil__get_editor_state, mcp__pencil__open_document, mcp__figma__generate_figma_design, mcp__figma__get_design_context, mcp__figma__get_screenshot]
---

# Page Builder — Create Page

You are creating a new page for the designer's application. You generate 2-3 variants and let the designer choose.

## Prerequisites

Read `design-system.json`. If it has empty `colors`, `typography`, or `layout`, tell the designer: "Please run `/design-system` first to establish your look and feel."

Read `sitemap.json` to understand existing pages.

## Step 1: Which page?

If there are existing pages in the sitemap, show them and suggest what might come next based on the app type.

Ask: "Which page would you like to build?"

Common suggestions based on app type:
- SaaS: Login, Dashboard, Settings, Profile, Billing
- E-commerce: Home, Product listing, Product detail, Cart, Checkout
- Portfolio: Home, Projects, About, Contact
- Landing page: Hero, Features, Pricing, Testimonials, Footer

Wait for their answer.

## Step 2: Page components

Ask: "What key components should this page include?"

Suggest relevant components based on the page type. For example, for a Dashboard:
- Sidebar navigation
- Top header with search and profile
- Stats/metrics cards
- Charts or graphs area
- Activity feed / recent items
- Quick actions

Let the designer pick, modify, or add their own.

## Step 3: Check for assets

Use Glob to scan `assets/` for relevant images (logos for nav, avatars for profiles, product images for listings, etc.). Note which assets are available to use in the designs.

## Step 4: Generate 2-3 variants

Read the design system from `design-system.json` and apply it consistently.

Announce: "I'm generating 3 variants for your [page name]. Watch your design tool!"

**If Pencil:**
- Call `mcp__pencil__get_guidelines` with the appropriate topic
- Call `mcp__pencil__find_empty_space_on_canvas` to find space for 3 frames side by side
- Use `mcp__pencil__batch_design` to create 3 distinct layout variants, each in its own frame labeled "Variant A", "Variant B", "Variant C"
- Each variant should use the same design system but differ in layout composition, component arrangement, or visual hierarchy
- Call `mcp__pencil__get_screenshot` for each variant

**If Figma:**
- Use `mcp__figma__generate_figma_design` to create 3 variants
- Call `mcp__figma__get_screenshot` for each variant

Show screenshots of all variants to the designer.

## Step 5: Choose variant

Ask: "Which variant do you prefer?"
- **A** — [brief description of variant A's layout]
- **B** — [brief description of variant B's layout]
- **C** — [brief description of variant C's layout]
- **Mix** — describe what you'd like to combine from different variants

## Step 6: Clean up and save

Remove the unchosen variants from the design tool.

**If Pencil:** Use `mcp__pencil__batch_design` with Delete operations to remove unchosen frames.
**If Figma:** Note: manual cleanup may be needed.

Update `sitemap.json` — add the new page:

```json
{
  "id": "<page-slug>",
  "name": "<Page Name>",
  "status": "completed",
  "toolRef": "<frame-id-or-node-id>",
  "children": []
}
```

Tell the designer: "Page added to your sitemap! You can run `/refine` to adjust specific components, `/create-component` to build a component in isolation, or `/create-page` for the next page."
```

**Step 2: Test the command**

Run `/create-page` after completing design system setup. Verify:
- It reads state from both JSON files
- Suggests appropriate pages for the app type
- Generates 3 variants in the design tool
- Shows screenshots of each
- Cleans up unchosen variants
- Updates sitemap.json

**Step 3: Commit**

```bash
git add .claude/commands/create-page.md
git commit -m "feat: add /create-page command with multi-variant generation"
```

---

### Task 5: `/create-component` command

**Files:**
- Create: `.claude/commands/create-component.md`

**Step 1: Create the create-component command**

Write `.claude/commands/create-component.md`:

```markdown
---
description: Design a specific component in an isolated frame
allowed-tools: [Read, Write, Edit, Glob, AskUserQuestion, mcp__pencil__batch_design, mcp__pencil__batch_get, mcp__pencil__get_screenshot, mcp__pencil__find_empty_space_on_canvas, mcp__pencil__get_guidelines, mcp__pencil__get_editor_state, mcp__pencil__open_document, mcp__figma__generate_figma_design, mcp__figma__get_screenshot, mcp__figma__get_design_context]
---

# Page Builder — Create Component

You are designing a specific component in isolation. The component will be created in its own frame so the designer can review and approve it before it's used in pages.

## Prerequisites

Read `design-system.json`. If it has empty `colors` or `typography`, tell the designer: "Please run `/design-system` first."

## Step 1: Which component?

Ask: "What component would you like to design?"

Common components:
- Navigation (top bar, sidebar, breadcrumbs)
- Buttons (primary, secondary, outline, ghost)
- Cards (content, pricing, team member, testimonial)
- Forms (login, signup, contact, search)
- Tables (data table, list view)
- Modals / dialogs
- Hero sections
- Footer
- Charts / data visualization
- Other (describe it)

Wait for their answer.

## Step 2: Component details

Ask relevant follow-up questions based on the component type. For example:
- Navigation: "Top bar or sidebar? Include search? User avatar/menu?"
- Card: "What content? Image, title, description, CTA?"
- Form: "Which fields? Validation style?"

Keep it to 1-2 questions maximum.

## Step 3: Generate component

Read `design-system.json` and apply the design system.

Create the component in an **isolated frame** labeled with the component name.

Include relevant **variants/states** where applicable:
- Buttons: default, hover, active, disabled
- Inputs: empty, filled, focused, error
- Cards: with/without image, different content lengths

**If Pencil:**
- Call `mcp__pencil__find_empty_space_on_canvas` to find placement
- Use `mcp__pencil__batch_design` to create the component frame with variants
- Call `mcp__pencil__get_screenshot` to show the result

**If Figma:**
- Use `mcp__figma__generate_figma_design` to create the component
- Call `mcp__figma__get_screenshot` to show the result

## Step 4: Review

Show the screenshot and ask: "How does this look? I can adjust colors, sizing, spacing, or any other aspect. Say 'looks good' when you're satisfied, or describe what to change."

If changes requested, iterate using `/refine` logic — adjust and show updated screenshot until approved.
```

**Step 2: Test the command**

Run `/create-component` and verify it creates an isolated component with variants.

**Step 3: Commit**

```bash
git add .claude/commands/create-component.md
git commit -m "feat: add /create-component command for isolated component design"
```

---

### Task 6: `/refine` command

**Files:**
- Create: `.claude/commands/refine.md`

**Step 1: Create the refine command**

Write `.claude/commands/refine.md`:

```markdown
---
description: Refine a component or section of a page
argument-hint: [description of what to change]
allowed-tools: [Read, Write, Edit, Glob, AskUserQuestion, mcp__pencil__batch_design, mcp__pencil__batch_get, mcp__pencil__get_screenshot, mcp__pencil__snapshot_layout, mcp__pencil__get_editor_state, mcp__pencil__search_all_unique_properties, mcp__pencil__replace_all_matching_properties, mcp__figma__generate_figma_design, mcp__figma__get_design_context, mcp__figma__get_screenshot]
---

# Page Builder — Refine

You are refining a specific component or section of a design. The designer wants to iterate until they're satisfied.

## Prerequisites

Read `design-system.json` for the tool choice and design tokens.

## Step 1: What to refine?

If the designer provided arguments ($ARGUMENTS), use that as the refinement description.

Otherwise, ask: "What would you like to refine? You can:
1. Describe the component or section (e.g., 'the navigation bar', 'the hero section')
2. Describe the change you want (e.g., 'make the buttons larger', 'change the card layout to horizontal')"

## Step 2: Locate the element

**If Pencil:**
- Call `mcp__pencil__get_editor_state` to check if there's an active selection — if the designer has selected a node, work on that
- If no selection, use `mcp__pencil__batch_get` with search patterns matching the designer's description to find the relevant nodes
- Call `mcp__pencil__snapshot_layout` to understand the current layout
- Call `mcp__pencil__get_screenshot` to see the current state

**If Figma:**
- Use `mcp__figma__get_design_context` to understand the current design
- Call `mcp__figma__get_screenshot` to see the current state

Show the current state to the designer: "Here's what I see. I'll now make the changes you described."

## Step 3: Make changes

Apply the requested changes while maintaining the design system.

**If Pencil:**
- Use `mcp__pencil__batch_design` with Update/Replace operations to modify existing nodes
- For property-wide changes, use `mcp__pencil__replace_all_matching_properties`
- Call `mcp__pencil__get_screenshot` to show the result

**If Figma:**
- Use `mcp__figma__generate_figma_design` to regenerate the refined section
- Call `mcp__figma__get_screenshot` to show the result

## Step 4: Review loop

Show the updated screenshot.

Ask: "How does this look? I can keep adjusting, or say 'looks good' to move on."

If more changes requested, repeat steps 2-4.
If satisfied, confirm: "Great! The refinement is complete. You can continue with `/create-page`, `/create-component`, or `/refine` again."
```

**Step 2: Test the command**

Run `/refine make the header text larger` and verify it locates and modifies the element.

**Step 3: Commit**

```bash
git add .claude/commands/refine.md
git commit -m "feat: add /refine command for iterative design adjustments"
```

---

### Task 7: `/sitemap` command

**Files:**
- Create: `.claude/commands/sitemap.md`

**Step 1: Create the sitemap command**

Write `.claude/commands/sitemap.md`:

```markdown
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
```

**Step 2: Test the command**

Run `/sitemap` and verify it reads and displays the sitemap correctly.

**Step 3: Commit**

```bash
git add .claude/commands/sitemap.md
git commit -m "feat: add /sitemap command for viewing and managing page structure"
```

---

### Task 8: CLAUDE.md project instructions

**Files:**
- Create: `CLAUDE.md`

**Step 1: Create project CLAUDE.md**

Write `CLAUDE.md` with project context so Claude understands this project in any session:

```markdown
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
```

**Step 2: Commit**

```bash
git add CLAUDE.md
git commit -m "feat: add CLAUDE.md with project overview and command reference"
```

---

### Task 9: End-to-end walkthrough test

**Files:** None (testing only)

**Step 1: Run `/start`**

Verify the full onboarding flow works: tool selection, app type, inspiration, assets scan, state file creation.

**Step 2: Run `/design-system`**

Verify it reads state, asks questions, generates a design system frame in the tool, saves to JSON.

**Step 3: Run `/create-page`**

Verify it generates 3 variants, shows screenshots, handles selection, updates sitemap.

**Step 4: Run `/create-component`**

Verify isolated component creation with variants.

**Step 5: Run `/refine`**

Verify it can locate and modify an existing element.

**Step 6: Run `/sitemap`**

Verify it displays the tree and can add/reorder pages.

**Step 7: Fix any issues found and commit**

```bash
git add -A
git commit -m "fix: address issues found during end-to-end testing"
```
