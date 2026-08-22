---
description: "Use to remove only this chat's created artifacts from the active workspace for a clean restart."
argument-hint: "Required: scope notes or exclusions; omit to clean all artifacts created in this chat."
---

# Clean Slate Workspace Engine

## 1. Scope & Analysis
- Inspect current conversation artifacts in the active workspace.
- List all files, plans, repo memory, session memory, and temporary artifacts created in this chat.
- Determine ownership for each artifact.

## 2. Resolution Rules
- Do not delete anything until inspection is complete and targets are listed.
- Ask for explicit confirmation before deletion.
- Delete only artifacts created in this chat.
- Treat ambiguous ownership as user-authored.
- Do not delete or modify user-authored files.
- Do not touch persistent user-level memories or reusable customizations unless they were created for this workspace in this chat.
- Remove empty directories left by deletions.
- Confirm no session-created files or empty directories remain after deletion.
- If no session-created artifacts exist, state that explicitly and stop.

## 3. Safety Guards
- **Execution Boundary:** Delete only after the deletion target list is presented and the user explicitly confirms.
- Never delete user-authored files or ambiguous-ownership artifacts.
- Never modify persistent user-level memories or reusable customizations that were not created in this chat.

## 4. Review Plan Layout
Use this exact markdown schema:

### Scope
- Target: <active workspace>
- Mode: <read-only | apply-after-confirmation>

### Result
- Summary: <what was deleted and what was preserved>

### Evidence
- Deleted: <none | item1; item2>
- Preserved: <none | item1; item2>
- Uncertainties: <none | item1; item2>

### Next Action
- <single minimal next step or `none`>

### Verdict
- READY | NEEDS FIXES | BLOCKED
