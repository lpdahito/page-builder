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
