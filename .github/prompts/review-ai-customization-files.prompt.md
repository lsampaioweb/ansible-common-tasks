---
description: "Use to audit AI customization files for duplicates, conflicts, and enforceability."
argument-hint: "Required: file, file list, folder, or glob to audit."
---

# AI Customization Audit Engine

## 1. Scope & Analysis
1. Target only user-provided scope. If missing, stop and request inputs.
2. Load and apply the style contract from `.github/instructions/ai-customization.instructions.md`.
3. Establish active cross-file references across target directories.

## 2. Resolution Rules
- **Scanning Rigor:** Scan 100% of files in scope.
- **Audit Checklist:** Inspect every file along six dimensions:
  1. Duplicate rules (intra-file and cross-file).
  2. Conflicting rules (direct and soft conflicts).
  3. Verbosity (filler and low-signal prose).
  4. Directives (ambiguous or non-enforceable phrasing).
  5. Frontmatter (routing patterns, discoverability).
  6. Token efficiency (density; reward high-signal literals; penalize descriptive bloat).
- **Scoring Protocol:** Score each file 0–10. Assign 0–2 per dimension with factual justification. Map dimensions as follows: Clarity = frontmatter + token efficiency (2 checklist items → 1 column), Enforceability = directives, Consistency = cross-file alignment, Brevity = verbosity, Conflict-Free = duplicates + conflicts (2 checklist items → 1 column).
- **Status Classification:** PASS (9–10) | WARN (7–8) | FAIL (0–6).

## 3. Safety Guards
- Never sample.
- **Execution Boundary:** Apply changes only after explicit user confirmation.
- **Fix Application Rule:** If authorized, modify only approved items.

## 4. Review Plan Layout
Use this exact markdown schema:

### Scope
- Target: <file, folder, or glob>
- Mode: <read-only | apply-after-confirmation>
- Assumptions applied: <none | item1; item2>

### Result
- Summary: <files scanned and top outcome>

### Findings (Critical | High | Medium | Low)
- Keep heading shape exactly: `[ID] - [SEVERITY] - [TYPE]`.
- `ID` must be `<SeverityCode><Index>` using `C|H|M|L` + 1-based index within each severity bucket.
- Number findings after sorting by severity rank, then file path (asc), line (asc), and type (asc).

#### [ID] - [SEVERITY] - [TYPE]
- Location: <file path>:<line>
- Why it matters: <technical impact>
- Minimal fix: <one minimal action>

### Evidence
- Scores: <path | total | Clarity 0-2 reason | Enforceability 0-2 reason | Consistency 0-2 reason | Brevity 0-2 reason | Conflict-Free 0-2 reason | status>
- Quick wins: <none | item1; item2>

### Next Action
- <single minimal next step or `none`>

### Verdict
- READY | NEEDS FIXES | BLOCKED
