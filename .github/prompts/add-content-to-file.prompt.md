---
description: "Use to add, update, or deduplicate content in markdown or plain text files."
argument-hint: "Required: #file:path/to/file and the new content to add"
---

# Add Content To File Engine

## 1. Scope & Analysis
- Read the target file.
- Identify structure: headings, sections, list patterns, and style conventions.
- Find exact heading matches for the requested content.
- Find exact content matches.
- If no exact match exists, find near matches by heading-keyword overlap.

## 2. Resolution Rules
- Use one insertion target only.
- Prefer exact heading match.
- If no exact heading match exists, use the nearest section by heading-keyword overlap.
- If no suitable section exists, propose a new section.
- Place new content at the logically correct position within the target section.
- Append at the end of the section only when the new content belongs last.
- Preserve existing formatting exactly: spacing, indentation, capitalization.
- Do not introduce unrelated formatting changes.
- If duplicate content exists, update the existing content instead of adding a second copy.
- Correct only clear grammar errors (e.g., subject-verb agreement, missing articles).

## 3. Safety Guards
- Do not change non-targeted sections.
- Never rephrase sentences; grammar corrections limited to inflection or missing articles are exempt.

## 4. Review Plan Layout
Use this exact markdown schema:

### Scope
- Target: <file path>
- Mode: <read-only | apply-after-confirmation>

### Result
- Summary: <ADDED | UPDATED | SKIPPED>

### Evidence
- <file path>:<line> - <location and what changed>
- Duplicates: <none | item1; item2>
- Improvements: <none | item1; item2>

### Next Action
- <single minimal next step or `none`>
