---
description: Create a new page with 2-3 design variants
allowed-tools: [Read, Write, Edit, Glob, AskUserQuestion, mcp__pencil__get_guidelines, mcp__pencil__get_style_guide, mcp__pencil__get_style_guide_tags, mcp__pencil__batch_design, mcp__pencil__batch_get, mcp__pencil__get_screenshot, mcp__pencil__find_empty_space_on_canvas, mcp__pencil__snapshot_layout, mcp__pencil__get_editor_state, mcp__pencil__open_document, mcp__figma__generate_figma_design, mcp__figma__get_design_context, mcp__figma__get_screenshot, mcp__paper__get_basic_info, mcp__paper__get_selection, mcp__paper__get_tree_summary, mcp__paper__get_node_info, mcp__paper__get_font_family_info, mcp__paper__create_artboard, mcp__paper__write_html, mcp__paper__get_screenshot, mcp__paper__update_styles, mcp__paper__set_text_content, mcp__paper__duplicate_nodes, mcp__paper__delete_nodes, mcp__paper__finish_working_on_nodes, mcp__paper__get_guide]
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

## Step 3: Check for assets

Use Glob to scan `assets/` for relevant images (logos for nav, avatars for profiles, product images for listings, etc.). Note which assets are available to use in the designs.

## Step 4: Generate 2-3 variants

Read the design system from `design-system.json` and apply it consistently.

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
**If Paper:** Use `mcp__paper__delete_nodes` to remove unchosen artboards.

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
