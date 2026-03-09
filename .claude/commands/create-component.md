---
description: Design a specific component in an isolated frame
allowed-tools: [Read, Write, Edit, Glob, AskUserQuestion, mcp__pencil__batch_design, mcp__pencil__batch_get, mcp__pencil__get_screenshot, mcp__pencil__find_empty_space_on_canvas, mcp__pencil__get_guidelines, mcp__pencil__get_editor_state, mcp__pencil__open_document, mcp__figma__generate_figma_design, mcp__figma__get_screenshot, mcp__figma__get_design_context, mcp__paper__get_basic_info, mcp__paper__get_selection, mcp__paper__get_font_family_info, mcp__paper__create_artboard, mcp__paper__write_html, mcp__paper__get_screenshot, mcp__paper__update_styles, mcp__paper__set_text_content, mcp__paper__duplicate_nodes, mcp__paper__delete_nodes, mcp__paper__finish_working_on_nodes, mcp__paper__get_guide]
---

# Page Builder — Create Component

You are designing a specific component in isolation. The component will be created in its own frame so the designer can review and approve it before it's used in pages.

## Prerequisites

Read `design-system.json`. If it doesn't exist, tell the designer: "Please run `/start` first to initialize your project."

If it has empty `colors` or `typography`, use sensible defaults (zinc neutrals, blue primary, amber accent, Inter font) and proceed. Tell the designer: "No design system set up yet — using clean defaults. Run `/design-system` anytime to customize."

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

**If Paper:**
- Use `mcp__paper__create_artboard` to create an artboard labeled with the component name
- Use `mcp__paper__write_html` to build the component and its variants in small pieces — one visual group per call
- Use `mcp__paper__duplicate_nodes` to efficiently create variant states from the base component
- Call `mcp__paper__get_screenshot` after every 2-3 write calls to review
- Call `mcp__paper__finish_working_on_nodes` when done

## Step 4: Review

Show the screenshot and ask: "How does this look? I can adjust colors, sizing, spacing, or any other aspect. Say 'looks good' when you're satisfied, or describe what to change."

If changes requested, iterate using `/refine` logic — adjust and show updated screenshot until approved.
