---
description: Establish or update the design system (colors, typography, layout)
allowed-tools: [Read, Write, Edit, Glob, AskUserQuestion, mcp__pencil__get_guidelines, mcp__pencil__get_style_guide_tags, mcp__pencil__get_style_guide, mcp__pencil__batch_design, mcp__pencil__batch_get, mcp__pencil__set_variables, mcp__pencil__get_screenshot, mcp__pencil__find_empty_space_on_canvas, mcp__pencil__open_document, mcp__pencil__get_editor_state, mcp__figma__generate_figma_design, mcp__figma__get_screenshot, mcp__figma__get_variable_defs, mcp__paper__get_basic_info, mcp__paper__get_selection, mcp__paper__get_font_family_info, mcp__paper__create_artboard, mcp__paper__write_html, mcp__paper__get_screenshot, mcp__paper__update_styles, mcp__paper__set_text_content, mcp__paper__duplicate_nodes, mcp__paper__delete_nodes, mcp__paper__finish_working_on_nodes, mcp__paper__get_guide]
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

**If Paper:**
- Call `mcp__paper__get_basic_info` to understand the file structure and existing artboards
- Call `mcp__paper__get_font_family_info` to check available fonts before choosing typography
- Call `mcp__paper__get_guide` with relevant topics for workflow guidance

Store any relevant patterns and guidelines internally to inform your design decisions.

## Step 0: Scan assets

Check `assets/` subfolders for logos, inspiration images, or brand materials. If found, use them to inform color and style suggestions in the following steps. Mention to the designer what you found.

## Step 1: Color palette

Ask the designer:

"Let's pick your color palette. Would you like to:"
1. **Browse colors** — I'll render Tailwind's color palettes in your design tool so you can pick visually
2. **Let me propose** — I'll suggest a palette based on your app type and description, and you approve or adjust
3. **Provide your own** — share specific Tailwind color names (e.g. "blue for primary, slate for neutrals")

Wait for their answer.

### Option 1: Browse colors

Render Tailwind's color palettes as swatches in the design tool. Organize them into two groups:

**Neutrals** (for backgrounds, text, borders):
slate, gray, zinc, neutral, stone, taupe, mauve, mist, olive

**Chromatic** (for primary, secondary, accent):
red, orange, amber, yellow, lime, green, emerald, teal, cyan, sky, blue, indigo, violet, purple, fuchsia, pink, rose

For each color family, show shades 50, 100, 200, 300, 400, 500, 600, 700, 800, 900, 950 as a horizontal row of swatches with the family name as a label. Use the Tailwind CSS v4 color values.

**If Pencil:**
- Call `mcp__pencil__find_empty_space_on_canvas` to find space
- Use `mcp__pencil__batch_design` to create a frame labeled "Tailwind Colors — Neutrals" with neutral swatches, and a second frame "Tailwind Colors — Chromatic" with chromatic swatches
- Call `mcp__pencil__get_screenshot` for each frame

**If Figma:**
- Use `mcp__figma__generate_figma_design` to create the color swatch frames
- Call `mcp__figma__get_screenshot`

**If Paper:**
- Use `mcp__paper__create_artboard` to create artboards for neutral and chromatic swatches
- Use `mcp__paper__write_html` to render each color family row — one family per call
- Call `mcp__paper__get_screenshot` after every 2-3 families to review
- Call `mcp__paper__finish_working_on_nodes` when done

Then ask the designer:

"Here are all the Tailwind colors. Pick the ones you'd like to use:
- **Neutral family** — which neutral palette for backgrounds, text, and borders? (e.g. slate, zinc, stone)
- **Primary** — your main brand/action color? (e.g. blue, indigo, emerald)
- **Accent** — for highlights and CTAs? (can be the same as primary or a contrasting color)"

Wait for their answer. Once chosen, delete the swatch frames from the design tool.

### Option 2: Let me propose

Based on the app type and project description from `design-system.json`, propose a palette using Tailwind color families. For example:
- SaaS dashboard → zinc neutrals, blue primary, amber accent
- E-commerce → stone neutrals, emerald primary, rose accent
- Portfolio → neutral neutrals, indigo primary, amber accent

Present the proposal as a short list:
- **Neutrals:** [family] — for backgrounds, text, borders
- **Primary:** [family] — for buttons, links, active states
- **Accent:** [family] — for highlights, badges, CTAs

Ask: "How does this palette sound? You can approve, swap any color family, or switch to browsing all colors."

Wait for their answer.

### Option 3: Provide your own

Let the designer name Tailwind color families directly (e.g. "blue for primary, slate for neutrals, amber for accent"). Validate that the names match actual Tailwind families.

### After color selection

From the chosen color families, assign roles:
- `neutral` — the neutral family (all shades used for backgrounds, text, borders)
- `primary` — main brand/action color (shade 500 for default, with full range available)
- `accent` — highlights and CTAs (shade 500 for default)
- `error` — red (unless red is already primary/accent, then rose)
- `warning` — amber (unless already taken, then yellow)
- `success` — green (unless already taken, then emerald)
- `info` — sky (unless already taken, then cyan)

For each role, define both light and dark mode usage:
- Light mode: neutral-50 background, neutral-900 text, primary-600 buttons
- Dark mode: neutral-950 background, neutral-100 text, primary-400 buttons

Render a **palette preview** in the design tool showing the chosen colors with their roles labeled. Take a screenshot and ask the designer to confirm before proceeding.

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

Update `design-system.json` with all choices. Store colors as Tailwind family names:

```json
{
  "colors": {
    "neutral": "zinc",
    "primary": "blue",
    "accent": "amber",
    "error": "red",
    "warning": "amber",
    "success": "green",
    "info": "sky"
  }
}
```

Generate a **design system reference frame** in the chosen tool. Create two versions — **light mode** and **dark mode** — side by side or stacked:
- Color swatches with labels (including status colors)
- Typography samples (H1-H4, body, small)
- Button styles (primary, secondary, outline, destructive)
- Input fields (text input, select, checkbox)
- Card example
- Alert/notification examples (error, warning, success, info)
- Spacing scale visualization

### Design quality

Apply the frontend-design skill principles: choose a bold aesthetic direction, use distinctive typography, and make intentional color choices. The design system frame itself should demonstrate the quality bar for all future pages.

**If Pencil:**
- Call `mcp__pencil__find_empty_space_on_canvas` to find placement
- Use `mcp__pencil__batch_design` to create the design system frame
- Call `mcp__pencil__set_variables` to store colors and typography as variables
- Call `mcp__pencil__get_screenshot` to show the result

**If Figma:**
- Use `mcp__figma__generate_figma_design` to create the design system page
- Call `mcp__figma__get_screenshot` to show the result

**If Paper:**
- Call `mcp__paper__get_font_family_info` to verify chosen fonts are available
- Use `mcp__paper__create_artboard` to create the design system artboard
- Use `mcp__paper__write_html` to add content in small pieces — one visual group per call (color swatches, then typography samples, then buttons, etc.)
- Call `mcp__paper__get_screenshot` after every 2-3 write calls to review
- Call `mcp__paper__finish_working_on_nodes` when done

## Step 5: Confirm

Show a screenshot of the generated design system frame.

Ask: "Does this design system look right? I can adjust any of the colors, typography, or layout settings. When you're happy, run `/create-page` to start building pages."
