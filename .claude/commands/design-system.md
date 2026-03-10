---
description: Establish or update the design system (typography, colors, layout)
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

## Step 1: Typography

Ask the designer:

"Let's pick your typography. Would you like to:"
1. **Browse pairings** — I'll render font pairings in your design tool so you can compare visually
2. **Custom** — specify your own fonts

Wait for their answer.

### Option 1: Browse pairings

The `main.pen` file ships with a pre-built **"Font Pairings — Serif + Sans-Serif"** frame (node ID `qNlQ9`) containing 6 popular Google Fonts pairings:
- **A** — Playfair Display + Inter (Classic & Modern)
- **B** — Merriweather + Open Sans (Warm & Readable)
- **C** — Lora + Roboto (Elegant & Clean)
- **D** — DM Serif Display + DM Sans (Cohesive & Contemporary)
- **E** — Libre Baskerville + Source Sans 3 (Editorial & Professional)
- **F** — Cormorant Garamond + Nunito (Refined & Friendly)

**If Pencil:**
- The pairings are already in `main.pen`. Call `mcp__pencil__get_screenshot` on the font pairings frame (node ID `qNlQ9`) so the designer can see them.

**If Figma:**
- Use `mcp__figma__generate_figma_design` to create a font pairing frame with the same 6 pairings
- Call `mcp__figma__get_screenshot`

**If Paper:**
- Use `mcp__paper__create_artboard` to create a typography artboard
- Use `mcp__paper__write_html` to render each pairing — one pairing per call
- Call `mcp__paper__get_screenshot` after every 2 pairings to review
- Call `mcp__paper__finish_working_on_nodes` when done

Then ask the designer:

"Here are some font pairings (serif headings + sans-serif body). Which one do you like? Or I can:
- Show you **more pairings** with a different vibe (e.g. bolder, more playful, more editorial)
- **Mix and match** — pick a heading font from one pairing and a body font from another"

Wait for their answer. Once chosen, keep the font pairing frame in `main.pen` for reference.

If the designer asks for more options, generate a fresh set of 3–4 pairings with a different stylistic direction and render them in a new frame. Use Google Fonts. Before rendering, verify each font is available:
- **If Paper:** call `mcp__paper__get_font_family_info` for each font family
- **If Pencil / Figma:** confirm the fonts are available on Google Fonts

For each new pairing, show:
- The pairing name/label (e.g. "Pairing G — Bold & Expressive")
- A large heading sample using the heading font (e.g. "The quick brown fox jumps over the lazy dog")
- A body paragraph sample using the body font (2–3 sentences of realistic placeholder text)
- Font names and weights displayed as a small caption

Repeat until the designer is satisfied. Delete any extra pairing frames from the design tool once a choice is made (but keep the original `main.pen` pairings frame).

### Option 2: Custom

Let the designer specify their own fonts. Verify availability (Google Fonts or system fonts) before accepting. If a font isn't available, suggest close alternatives.

### After typography selection

From the chosen pairing, establish:
- **Heading font** — family and weight (e.g. Sora, 700)
- **Body font** — family and weight (e.g. Inter, 400)
- **Type scale** — ask the designer: "What density do you prefer for text sizing?"
  - **Compact** — smaller sizes, tighter spacing (good for data-heavy UIs)
  - **Default** — balanced readability
  - **Spacious** — larger sizes, generous line height (good for marketing/editorial)

## Step 2: Color palette

Ask the designer:

"Let's pick your color palette. Would you like to:"
1. **Browse colors** — I'll render Tailwind's color palettes in your design tool so you can pick visually
2. **Let me propose** — I'll suggest a palette based on your app type, description, and chosen typography, and you approve or adjust
3. **Provide your own** — share specific Tailwind color names (e.g. "blue for primary, slate for neutrals")

Wait for their answer.

### Option 1: Browse colors

The `main.pen` file ships with pre-built Tailwind color swatch frames:
- **"Tailwind Colors — Neutrals"** — slate, gray, zinc, neutral, stone, taupe, mauve (shades 50–950)
- **"Tailwind Colors — Chromatic"** — red, orange, amber, yellow, lime, green, emerald, teal, cyan, sky, blue, indigo, violet, purple, fuchsia, pink, rose (shades 50–950)

**If Pencil:**
- The swatches are already in `main.pen`. Call `mcp__pencil__get_screenshot` on each swatch frame (node IDs `PZP1d` for Neutrals, `16jND` for Chromatic) so the designer can see them.

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

Wait for their answer. Once chosen, delete the swatch frames from the design tool (except for Pencil, where the swatches are part of `main.pen` and should be kept).

### Option 2: Let me propose

Based on the app type, project description, and chosen typography from `design-system.json`, propose a palette using Tailwind color families. Let the typography mood inform your color suggestions — e.g. a bold geometric font pairs well with saturated, confident colors, while a delicate serif suits more muted, refined tones. For example:
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
