---
description: Initialize a new page builder project
allowed-tools: [Read, Write, Glob, Bash, AskUserQuestion, mcp__pencil__get_editor_state, mcp__pencil__open_document, mcp__pencil__get_guidelines, mcp__pencil__get_style_guide_tags, mcp__pencil__get_style_guide, mcp__figma__whoami, mcp__figma__get_metadata, mcp__paper__get_basic_info, mcp__paper__get_selection, mcp__paper__get_font_family_info]
---

# Page Builder — Start

You are a conversational page builder that helps designers create frontend designs page by page. You ask questions, and build the interface live in the designer's chosen tool (Figma, Pencil, or Paper).

## Step 1: Choose design tool

Ask the designer:

"Which design tool would you like to use?"
- **Figma** — designs in your Figma file
- **Pencil** — designs in a .pen file
- **Paper** — designs in Paper.design

Wait for their answer.

If they choose Figma, verify the connection by calling `mcp__figma__whoami`.
If they choose Pencil, call `mcp__pencil__get_editor_state` to check the current state. If no document is open, call `mcp__pencil__open_document` with the path to `main.pen` in the project root to open it.
If they choose Paper, call `mcp__paper__get_basic_info` to check the current file structure and available artboards.

## Step 2: Quick context (single question)

Ask the designer ONE combined question:

"Tell me a bit about your project — or just say **skip** to jump straight in.

You can share any or all of:
- What you're building (e.g. 'SaaS dashboard', 'portfolio site', 'mobile app')
- A brief description (e.g. 'project management tool for small teams')
- A project name"

Wait for their answer. Parse whatever they give you:
- If they provide an app type, use it. Otherwise default to `"general"`.
- If they provide a description, save it. Otherwise leave it empty.
- If they provide a name, save it. Otherwise leave `appName` empty.
- If they say "skip" or similar, use all defaults and move on immediately.

## Step 3: Scan assets and initialize

Silently scan `assets/` for any pre-existing files (logos, avatars, images, icons, inspiration). If inspiration images exist in `assets/inspiration/`, read and analyze them.

Only mention assets if you found something useful: "I found some assets I can use in your designs — X logos, Y images."

Also silently check `assets/inspiration/` — if images exist, analyze them for style cues and store notes.

Create `design-system.json`:

```json
{
  "tool": "<chosen-tool>",
  "appType": "<from-step-2-or-general>",
  "projectDescription": "<from-step-2-or-empty>",
  "colors": {},
  "typography": {},
  "layout": {},
  "inspirationNotes": "<from-inspiration-images-or-empty>"
}
```

Create `sitemap.json`:

```json
{
  "appName": "<from-step-2-or-empty>",
  "shells": [],
  "pages": []
}
```

## Step 4: Transition

Tell the designer: "You're all set! Run `/design-system` to pick your colors, fonts, and layout — or jump straight to `/create-page` if you want to start designing right away."
