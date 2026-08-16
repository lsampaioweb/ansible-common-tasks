---
description: "Use to audit code against active instruction files and identify governance gaps in both directions."
argument-hint: "Optional: target path, module, file, or glob to audit."
---

# Review Code And Instruction Coverage

## 1. Scope & Analysis
1. Resolve the target scope from `{{$ARGUMENTS}}`. If missing, audit the full workspace.
2. Before reading file content, enumerate the implementation files in scope that are relevant to the active instruction set.
3. Use whole-scope pattern searches when they are an efficient way to test an active rule, and use file reads to confirm surrounding context and cover rule classes that pattern searches cannot validate.
4. Identify all applicable instruction files for the target scope.
5. Establish active cross-references between code files and instruction rules.
6. Build findings only from code implementation files in the target scope.

## 2. Resolution Rules
- Check code against all applicable instruction files.
- Derive instruction coverage needs from observed code patterns.
- Keep instruction/prompt/agent file quality out of scope for findings.
- Exclude `.github/instructions/*.instructions.md`, `.github/prompts/*.prompt.md`, and `.github/agents/*.agent.md` from violation evidence unless they are the explicit audit target.
- **Shared Governance Retention:** Treat `.github/instructions/*.instructions.md`, `.github/prompts/*.prompt.md`, and `.github/agents/*.agent.md` as shared cross-project assets by default.
- **No Cross-Stack Deletion Recommendation:** Never recommend deleting instruction/prompt/agent files only because they belong to another stack that is not present in the current repository.
- **Cross-Stack Handling:** If a file is not applicable to the current code stack, classify it as retained shared governance and exclude it from violation counts.
- **Code Violations:** Flag places where the implementation does not follow the active rules.
- **Instruction Gaps:** Flag recurring code patterns, missing constraints, or missing governance that should be added.
- **Coverage Absence:** If no instruction files exist for the target scope, infer the minimum enforceable initial set from the code and propose it.
- **New File Placement:** Place proposed instruction files in `.github/instructions/*`.
- **Evidence Rule:** Base all conclusions on files that actually exist in scope.
- **Scanning Rigor:** Scan the full target scope.
- **Finding Format:** For each finding, state the problem briefly and include one minimal remediation action.
- **Coverage Gate:** Report missing required instruction coverage under Required Violations.
- **Deduplication Rule:** Merge repeated observations that share the same root cause into one finding.
- **Compression Rule:** Do not list compliant files, retained instruction files, or exhaustive instruction inventories unless they are necessary to explain a finding.
- **Reference Precision:** Include line references when they are available without guesswork.
- **Ambiguity Handling:** If a conclusion depends on an instruction whose meaning is materially ambiguous in the target stack, do not report it as a Required Violation. Classify it under Ambiguous Cases and state the exact instruction ambiguity.
- **No Speculative Expansion:** Do not infer stricter requirements than the active instruction text explicitly supports.

## 3. Safety Guards
- **Execution Boundary:** Apply changes only after explicit user confirmation.
- **Uncertainty Gate:** If context is insufficient to validate a finding, state uncertainty explicitly and stop.
- **Review Boundary:** Do not report style, wording, or structural defects inside instruction/prompt/agent files during code-governance audits.

## 4. Review Plan Layout
Use this exact markdown schema:

### Scope
- Target: <scope>
- Mode: <read-only | apply-after-confirmation>
- Instructions applied: <only files that materially affected findings>

### Result
- Summary: <top compliance outcome>

### Findings (Critical | High | Medium | Low)
- Keep heading shape exactly: `[ID] - [SEVERITY] - [TYPE]`.
- `ID` must be `<SeverityCode><Index>` using `C|H|M|L` + 1-based index within each severity bucket.
- Number findings after sorting by severity rank, then file path (asc), line (asc), and type (asc).

### Required Violations

#### [ID] - [SEVERITY] - [TYPE]
- Category: <Required Violation | Optional Gap | Ambiguous Case | Coverage Gap>
- Rule: <instruction file or coverage area>
- Evidence: <file path>:<line>
- Minimal remediation: <one minimal action>

### Optional Gaps
- List optional findings.

### Ambiguous Cases
- List unresolved findings.

### Instruction Coverage Gaps
- List missing required coverage and cross-reference related Required Violations IDs.

### Evidence
- Retained shared governance: <none | path1; path2>
- File actions: <none | Update path reason; Create path reason; Delete path reason>

### Next Action
- <single minimal next step or `none`>

### Verdict
- READY | NEEDS FIXES | BLOCKED
