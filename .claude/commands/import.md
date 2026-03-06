---
description: Browse and import shadcn/ui components or blocks into your design
argument-hint: [search query, e.g. "login", "dashboard", "pricing"]
allowed-tools: [Read, Write, Edit, AskUserQuestion, mcp__shadcn__search_items_in_registries, mcp__shadcn__list_items_in_registries, mcp__shadcn__view_items_in_registries, mcp__shadcn__get_item_examples_from_registries, mcp__pencil__batch_design, mcp__pencil__batch_get, mcp__pencil__get_screenshot, mcp__pencil__find_empty_space_on_canvas, mcp__pencil__get_guidelines, mcp__pencil__get_editor_state, mcp__figma__generate_figma_design, mcp__figma__get_screenshot, mcp__figma__get_design_context, mcp__paper__get_basic_info, mcp__paper__get_selection, mcp__paper__get_font_family_info, mcp__paper__create_artboard, mcp__paper__write_html, mcp__paper__get_screenshot, mcp__paper__update_styles, mcp__paper__set_text_content, mcp__paper__duplicate_nodes, mcp__paper__delete_nodes, mcp__paper__finish_working_on_nodes, mcp__paper__get_guide]
---

# Page Builder — Import

You are importing a shadcn/ui component or block into the designer's chosen design tool. shadcn/ui provides high-quality, ready-made UI components and full-page blocks (login pages, dashboards, sidebars, etc.) that can be rendered directly in the design.

## Prerequisites

Read `design-system.json`. If it doesn't exist or has no `tool` field, tell the designer: "Please run `/start` first to initialize your project."

## Step 1: Search or browse

If the designer provided arguments ($ARGUMENTS), use that as the search query.

Otherwise, ask: "What kind of component or block are you looking for? You can:
1. **Search** — describe what you need (e.g., 'login form', 'pricing table', 'sidebar')
2. **Browse** — I'll show you what's available"

**If searching:** Call `mcp__shadcn__search_items_in_registries` with `registries: ["@shadcn"]` and the query.

**If browsing:** Call `mcp__shadcn__list_items_in_registries` with `registries: ["@shadcn"]` and `limit: 20`.

Present the results in a clean list, grouping by type (block vs component). Include descriptions where available.

## Step 2: Pick an item

Ask: "Which one would you like to import?"

If the list is long, offer to search more specifically or paginate.

## Step 3: Preview the code

Call `mcp__shadcn__get_item_examples_from_registries` with `registries: ["@shadcn"]` and the selected item name as the query.

Also call `mcp__shadcn__view_items_in_registries` to get file details.

Show the designer a brief summary of the component:
- What it contains (layout, elements, interactions)
- Number of files / sub-components
- Any notable features

Ask: "Ready to import this into your design? I'll adapt it to your design system (colors, typography, spacing)."

## Step 4: Import into design tool

Read `design-system.json` to get the current design tokens (colors, typography, layout).

**Critical:** Do NOT just copy the shadcn code verbatim. Adapt the component to match the project's design system:
- Replace shadcn's default colors with the project's color palette
- Apply the project's typography (fonts, scale)
- Match the project's spacing and corner radius preferences
- Maintain the component's structure and UX patterns

### If Pencil:
- Call `mcp__pencil__find_empty_space_on_canvas` to find placement
- Use `mcp__pencil__batch_design` to create the component, translating the JSX structure and Tailwind classes into Pencil node properties
- Call `mcp__pencil__get_screenshot` to show the result

### If Figma:
- Use `mcp__figma__generate_figma_design` to create the component, using the shadcn code as a detailed reference for structure and layout
- Call `mcp__figma__get_screenshot` to show the result

### If Paper:
- Call `mcp__paper__get_font_family_info` to verify fonts before first use
- Use `mcp__paper__create_artboard` to create an artboard labeled with the component/block name
- Convert the React/JSX code to semantic HTML with inline styles or Tailwind classes
- Use `mcp__paper__write_html` to build the component in small pieces — one visual group per call
- Call `mcp__paper__get_screenshot` after every 2-3 write calls to review
- Call `mcp__paper__finish_working_on_nodes` when done

## Step 5: Review

Show a screenshot of the imported component.

Ask: "Here's the imported [component name], adapted to your design system. Would you like to:
1. **Keep it** — looks good as-is
2. **Adjust it** — I'll refine specific parts (run `/refine`)
3. **Try another variant** — import a different version of this component
4. **Import another** — browse for more components"
