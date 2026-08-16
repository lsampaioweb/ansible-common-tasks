---
description: "Use to synchronize Markdown documentation with current code and configuration behavior."
argument-hint: "Optional: scope, folder path, or feature-area filter."
---

# Markdown Documentation Sync Engine

## 1. Scope & Analysis
1. Resolve the target scope from `{{$ARGUMENTS}}`.
2. **Fallback Scan:** If scope is omitted, inspect uncommitted workspace changes (`git status`, `git diff`) first, then evaluate recent commits.
3. Correlate code and configuration deltas with impacted Markdown targets.

## 2. Resolution Rules
- **Execution Target:** Update Markdown only when code or configuration changes alter documented behavior or setup.
- **README Boundary:** Update `*.md` only when the mapped change affects onboarding, execution, or architecture.
- **Style Match:** Match existing formatting, heading style, and list conventions.
- **Correction Protocol — Content:** Repair stale instructions, outdated keys, and deprecated paths.
- **Correction Protocol — References:** Resolve broken links and replace obsolete examples.

## 3. Safety Guards
- **Execution Boundary:** Apply Markdown edits only after the read-only plan is presented and the user explicitly confirms.
- Do not invent features, parameters, properties, or runtime behavior absent from the codebase.
- **Uncertainty Gate:** If a documentation update cannot be verified with available code context, stop and ask focused questions before editing.

## 4. Review Plan Layout
Use this exact markdown schema:

### Scope
- Target: <scope>
- Mode: <read-only | apply-after-confirmation>

### Result
- Summary: <synced files and primary update outcome>

### Evidence
- Synced files: <none | path1; path2>
- Structural updates: <none | item1; item2>
- Verification points: <none | item1; item2>

### Next Action
- <single minimal next step or `none`>
