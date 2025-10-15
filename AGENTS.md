# AGENTS.md — Obsidian Vault Guidelines

## Purpose
- This repository is an Obsidian vault (the underlying plain‑text files for notes managed by the Obsidian app).
- Use this file only to understand how to work here. Do not rely on it for content description or structure beyond what’s written; always read the current vault state.

## Scope
- Applies to the entire repository unless a more specific AGENTS.md exists in a subfolder.

## Repository Facts
- Notes are Markdown files. Some may include YAML frontmatter.
- Obsidian features are used: wikilinks `[[Note]]`, embeds `![[Note#Section]]`, standard Markdown links, tags, and backlinks.
- App configuration and plugins typically live under `.obsidian/`. Treat that folder as managed by the user/Obsidian.
- Media/attachments are referenced from notes. Do not relocate or rename files referenced by notes.

## How to Work Here
- Always inspect the latest repository state before acting. Do not assume this file is up to date.
- Prefer read/search first: use fast, safe searches over the workspace (e.g., `rg`) to locate notes or references.
- Keep changes minimal and surgical; avoid broad refactors.

## Editing Rules
- Preserve content and structure:
  - Keep existing headings, ordering, spacing, and line breaks.
  - Preserve YAML frontmatter exactly (keys, order, spacing) unless explicitly asked to change it.
  - Do not convert or “normalize” link styles (wikilinks vs. Markdown) unless requested.
  - Do not change tags, aliases, block IDs, or anchors unless requested.
- Maintain link integrity:
  - Do not rename, move, or delete notes or attachments unless explicitly asked.
  - If asked to rename/move, update all inbound links and embeds accordingly.
- Be conservative:
  - Do not auto-fix spelling/grammar, rewrap text, or reformat lists unless requested.
  - Do not edit `.obsidian/` or plugin data unless requested.

## Adding Notes/Files (when requested)
- Use Markdown with optional YAML frontmatter. Keep frontmatter minimal and consistent with adjacent notes.
- Use Obsidian‑friendly links (wikilinks or the style already used nearby).
- Place new attachments where the vault already keeps them; do not invent new folder schemes.

## What Not To Do
- Do not summarize or reinterpret vault content without first reading the current files.
- Do not assume folder conventions; infer from the existing vault.
- Do not introduce new tools, formats, or folder structures without instruction.
- Do not commit wide‑ranging changes or mass edits without explicit request.

## Validation
- After edits, re‑scan for broken links/references locally (search for the changed title/path).
- Keep diffs focused and small so the user can review easily.

