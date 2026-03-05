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
