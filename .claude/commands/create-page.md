---
description: Create a new page with 2-3 design variants
allowed-tools: [Read, Write, Edit, Glob, AskUserQuestion, mcp__pencil__get_guidelines, mcp__pencil__get_style_guide, mcp__pencil__get_style_guide_tags, mcp__pencil__batch_design, mcp__pencil__batch_get, mcp__pencil__get_screenshot, mcp__pencil__find_empty_space_on_canvas, mcp__pencil__snapshot_layout, mcp__pencil__get_editor_state, mcp__pencil__open_document, mcp__figma__generate_figma_design, mcp__figma__get_design_context, mcp__figma__get_screenshot, mcp__paper__get_basic_info, mcp__paper__get_selection, mcp__paper__get_tree_summary, mcp__paper__get_node_info, mcp__paper__get_font_family_info, mcp__paper__create_artboard, mcp__paper__write_html, mcp__paper__get_screenshot, mcp__paper__update_styles, mcp__paper__set_text_content, mcp__paper__duplicate_nodes, mcp__paper__delete_nodes, mcp__paper__finish_working_on_nodes, mcp__paper__get_guide]
---

# Page Builder — Create Page

You are creating a new page for the designer's application. You generate 2-3 variants and let the designer choose.

## Prerequisites

Read `design-system.json`. If it doesn't exist, tell the designer: "Please run `/start` first to initialize your project."

If it has empty `colors`, `typography`, or `layout`, use sensible defaults and proceed:
- Colors: `zinc` neutral, `blue` primary, `amber` accent, `red` error, `amber` warning, `green` success, `sky` info
- Typography: Inter for body, Inter for headings, default scale
- Layout: comfortable spacing, slightly rounded corners (md), balanced style

Tell the designer: "No design system set up yet — I'll use a clean default palette. You can run `/design-system` anytime to customize."

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

### Shell setup (first page only)

Read `sitemap.json`. If the `shells` array is empty, set up shells before proceeding.

Based on the app type from `design-system.json`, suggest default shells:

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

Want to keep these, modify them, or skip shells entirely?"

Wait for their answer. The designer can:
- Keep the defaults
- Add, remove, or rename shells
- Change which blocks a shell includes
- Skip shells entirely (no shells will be created)

If the designer chose shells, populate the `shells` array in `sitemap.json`:

```json
{
  "id": "<shell-slug>",
  "name": "<Shell Name>",
  "blocks": [
    { "slot": "<block-name>", "position": "<top|bottom|left|right>" }
  ],
  "refs": {}
}
```

### Shell assignment

Read `sitemap.json` for available shells. If shells exist, ask:

"Which layout should this page use?"
- **[Shell Name]** — [list blocks, e.g. "topNav + footer"]
- ...
- **None** — standalone page, no shared layout (good for login, signup, error pages)

Wait for their answer. Record the chosen shell ID (or `null` for standalone).

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

## Step 3: Wireframe

Ask the designer:

"Before building the final design, would you like to start with a wireframe?"
- **I'll wireframe it** — I'll wait while you sketch a wireframe in the design tool, then build the page from it
- **Wireframe for me** — I'll generate a low-fidelity wireframe first, then we'll refine before the final design
- **Skip** — Go straight to the full design

Wait for their answer.

### Option A: Designer wireframes

Tell the designer: "Go ahead and sketch your wireframe. When you're done, select the frame and let me know."

Wait for their response. Then read the wireframe from the design tool:
- **Pencil:** `mcp__pencil__batch_get` with the node ID, then `mcp__pencil__get_screenshot`
- **Figma:** `mcp__figma__get_design_context` with the node ID, then `mcp__figma__get_screenshot`
- **Paper:** `mcp__paper__get_node_info` and `mcp__paper__get_jsx` for the node, then `mcp__paper__get_screenshot`

Analyze the wireframe and summarize what you see: layout structure, content areas, component placement, hierarchy. Confirm with the designer: "Here's what I see in your wireframe: [summary]. Does this look right before I build from it?"

Wait for confirmation. Store the wireframe reference (node ID and analysis) for use in Step 5.

### Option B: Generated wireframe

Generate a single low-fidelity wireframe using only grayscale colors (#FFFFFF, #F5F5F5, #E0E0E0, #9E9E9E, #424242, #212121), simple rectangles for images, and placeholder text. No branding, no icons, no color — just structure and hierarchy.

**If Pencil:**
- Call `mcp__pencil__find_empty_space_on_canvas` to find space
- Use `mcp__pencil__batch_design` to create the wireframe in a frame labeled "Wireframe — [Page Name]"
- Call `mcp__pencil__get_screenshot`

**If Figma:**
- Use `mcp__figma__generate_figma_design` to create the wireframe
- Call `mcp__figma__get_screenshot`

**If Paper:**
- Use `mcp__paper__create_artboard` labeled "Wireframe — [Page Name]"
- Use `mcp__paper__write_html` to build the wireframe in small pieces
- Call `mcp__paper__get_screenshot` after every 2-3 write calls
- Call `mcp__paper__finish_working_on_nodes` when done

Show the screenshot and ask: "Here's the wireframe. Would you like to:"
- **Approve** — I'll build the final design from this layout
- **Adjust** — tell me what to change
- **Start over** — I'll generate a different wireframe
- **Scrap it** — skip the wireframe and go straight to full design variants

If they want adjustments, make them and re-screenshot until approved. Store the wireframe reference (node ID and layout analysis) for use in Step 5.

### Option C: Skip

Proceed directly to Step 4.

## Step 4: Check for assets

Use Glob to scan `assets/` for relevant images (logos for nav, avatars for profiles, product images for listings, etc.). Note which assets are available to use in the designs.

## Step 5: Generate design

Read the design system from `design-system.json` and apply it consistently.

### If a wireframe exists (from Step 3A or 3B)

The wireframe defines the layout structure. Generate **1 final design** that faithfully follows the wireframe's layout, component placement, and hierarchy — but applies the full design system (colors, typography, spacing, real content, icons, and assets).

Do NOT deviate from the wireframe's structure. The wireframe is the blueprint. Skip to Step 7 (save) after generating, since there's no variant selection needed.

### If no wireframe (Step 3C — skipped)

### Shell block handling

If the page has a shell assigned:

1. Look up the shell in `sitemap.json`
2. Check if `refs` is populated (has node IDs for the shell's blocks)

**If refs are empty (first page in this shell):**
- Generate the shell blocks (nav, sidebar, footer, etc.) as part of each variant
- Use the design system tokens and app type to inform the block design
- After the designer chooses a variant (Step 5), record the node IDs of each shell block in the shell's `refs` object in `sitemap.json`

**If refs are populated (subsequent pages):**
- For each ref in the shell, read the reference block from the design tool:
  - **Pencil:** `mcp__pencil__batch_get` with the node ID, then `mcp__pencil__get_screenshot` of the node
  - **Figma:** `mcp__figma__get_design_context` with the node ID, then `mcp__figma__get_screenshot`
  - **Paper:** `mcp__paper__get_node_info` and `mcp__paper__get_jsx` for the node, then `mcp__paper__get_screenshot`
- If a ref read fails (node deleted or missing), fall back to generating the block fresh from the shell definition + design system, and update the shell's `refs` after completion
- Replicate the reference blocks in each variant, then generate only the unique page content in the remaining content area

**If shell is null (standalone):**
- Generate the full page with no shared blocks, as currently done

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

**If Paper:**
- Call `mcp__paper__get_font_family_info` to verify fonts before first use
- Use `mcp__paper__create_artboard` to create 3 artboards labeled "Variant A", "Variant B", "Variant C"
- Use `mcp__paper__write_html` to build each variant in small pieces — one visual group per call
- Each variant should use the same design system but differ in layout composition, component arrangement, or visual hierarchy
- Call `mcp__paper__get_screenshot` after every 2-3 write calls to review progress
- Use `mcp__paper__duplicate_nodes` when repeating similar elements across variants
- Call `mcp__paper__finish_working_on_nodes` after completing each variant

Show screenshots of all variants to the designer.

## Step 6: Choose variant (no-wireframe path only)

Ask: "Which variant do you prefer?"
- **A** — [brief description of variant A's layout]
- **B** — [brief description of variant B's layout]
- **C** — [brief description of variant C's layout]
- **Mix** — describe what you'd like to combine from different variants

## Step 7: Clean up and save

Remove the unchosen variants (if any) and any wireframe frames from the design tool.

**If Pencil:** Use `mcp__pencil__batch_design` with Delete operations to remove unchosen variant frames and wireframe frames.
**If Figma:** Note: manual cleanup may be needed.
**If Paper:** Use `mcp__paper__delete_nodes` to remove unchosen artboards and wireframe artboards.

Update `sitemap.json` — add the new page with its shell reference:

```json
{
  "id": "<page-slug>",
  "name": "<Page Name>",
  "shell": "<shell-id-or-null>",
  "status": "completed",
  "toolRef": "<frame-id-or-node-id>",
  "children": []
}
```

If this was the first page in a shell (refs were empty), also update the shell's `refs` in `sitemap.json` with the node IDs of each shell block from the chosen variant:

```json
"refs": {
  "topNav": "<node-id>",
  "footer": "<node-id>"
}
```

Tell the designer: "Page added to your sitemap! You can run `/refine` to adjust specific components, `/create-component` to build a component in isolation, or `/create-page` for the next page."
