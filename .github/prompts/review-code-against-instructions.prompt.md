---
description: "Use to audit code against active instruction files in both directions, with file-batch coverage when the scope is too large."
argument-hint: "Required: target path, module, file, or glob; omit to audit the full workspace. Re-run starting at Continue from when Verdict is CONTINUE."
---

# Review Code And Instruction Coverage

## 1. Scope & Analysis
1. Resolve the target scope from `{{$ARGUMENTS}}`. If missing, audit the full workspace.
2. Before reading implementation file content, enumerate every implementation file in scope that is relevant to the active instruction set. Sort that list by path ascending. This list is the **coverage queue**.
4. Prefer the user-provided path, module, or glob; do not expand beyond that scope.
5. Identify all applicable instruction files for the target scope.
6. Take the next batch of at most **25** files from the coverage queue (path order). If the queue has 25 or fewer files, review the full queue. State the batch limit in Coverage when Remaining is non-empty.
7. Use whole-scope pattern searches when they efficiently test an active rule across the current batch.
8. Use file reads to confirm surrounding context and cover rule classes that pattern searches cannot validate.
9. For every file in the current batch, check code against applicable instructions.
10. For every file in the current batch, derive instruction coverage needs from observed code patterns in the same run.
11. Build findings only from code implementation files in the current batch.

## 2. Resolution Rules
- Check code against all applicable instruction files for the current batch.
- Derive instruction coverage needs from observed code patterns in the current batch.
- Keep instruction/prompt/agent file quality out of scope for findings.
- Exclude `.github/instructions/*.instructions.md`, `.github/prompts/*.prompt.md`, and `.github/agents/*.agent.md` from violation evidence unless they are the explicit audit target.
- **Shared Governance Retention:** Treat instruction, prompt, and agent files under `.github/` as shared cross-project assets; never recommend deleting them only because another stack is absent from this repository.
- **Cross-Stack Handling:** If a file is not applicable to the current code stack, classify it as retained shared governance and exclude it from violation counts.
- **Code Violations:** Flag places where the implementation does not follow an explicit active rule.
- **Instruction Gaps:** Flag recurring code patterns, missing constraints, or missing governance that should be added.
- **Coverage Absence:** If no instruction files exist for the target scope, infer the minimum enforceable initial set from the code and propose it.
- **New File Placement:** Place proposed instruction files in `.github/instructions/*`.
- **Evidence Rule:** Base all conclusions on files that actually exist in scope.
- **Batch Completeness:** Within the current batch, report all Required Violations, Optional Gaps, Ambiguous Cases, and Instruction Coverage Gaps found in the same run.
- **Finding Format:** For each finding, state the problem briefly and include one minimal remediation action.
- **Rule Citation:** Set `Rule` to `<instruction-file>#<exact bullet quote or section anchor>`. Do not cite a vague coverage area alone.
- **Severity Map:** Critical = security, auth bypass, data loss, or secret exposure. High = clear Must-rule contract break. Medium = explicit non-security Must-rule gap. Low = explicit naming/style Must-rule gap. Soft preferences and inferred stricter rules are not Required Violations.
- **Coverage Gate:** Report missing required instruction coverage under Instruction Coverage Gaps and cross-reference related Required Violation IDs when applicable.
- **Deduplication Rule:** Merge repeated observations that share the same root cause into one finding.
- **Compression Rule:** Do not list compliant files, retained instruction files, or exhaustive instruction inventories unless they are necessary to explain a finding.
- **Reference Precision:** Include line references when they are available without guesswork.
- **Ambiguity Handling:** If a conclusion depends on an instruction whose meaning is materially ambiguous in the target stack, do not report it as a Required Violation. Classify it under Ambiguous Cases and state the exact instruction ambiguity.
- **No Speculative Expansion:** Do not infer stricter requirements than the active instruction text explicitly supports.
- **Continuation Rule:** When Remaining is non-empty, set Verdict to `CONTINUE` and set Next Action to re-run this prompt on the same scope starting at the first Remaining path.

## 3. Safety Guards
- **Execution Boundary:** Apply changes only after explicit user confirmation.
- **Uncertainty Gate:** If context is insufficient to validate a finding, state uncertainty explicitly and stop.
- **Review Boundary:** Do not report style, wording, or structural defects inside instruction/prompt/agent files during code-governance audits.
- **No Fake Completeness:** Never mark Verdict `READY` while Remaining files exist. Never claim the full target scope was reviewed when Remaining files exist.

## 4. Review Plan Layout
Use this exact markdown schema:

### Scope
- Target: <scope>
- Mode: <read-only | apply-after-confirmation>
- Instructions applied: <only files that materially affected findings>
- Coverage queue size: <N>
- Batch limit reason: <none | max 25 files | user-requested path>

### Coverage
- Reviewed this run: <path1; path2; ... | none>
- Remaining for later: <path1; path2; ... | none>
- Continue from: <first Remaining path | none>

### Result
- Summary: <top compliance outcome for the current batch>

### Findings (Critical | High | Medium | Low)
- Keep heading shape exactly: `[ID] - [SEVERITY] - [TYPE]`.
- `ID` must be `<SeverityCode><Index>` using `C|H|M|L` + 1-based index within each severity bucket.
- Number findings after sorting by severity rank, then file path (asc), line (asc), and type (asc).

### Required Violations

#### [ID] - [SEVERITY] - [TYPE]
- Category: Required Violation
- Rule: <instruction-file>#<exact bullet quote or section anchor>
- Evidence: <file path>:<line>
- Minimal remediation: <one minimal action>

### Optional Gaps
- <none | list optional findings>

### Ambiguous Cases
- <none | list unresolved findings>

### Instruction Coverage Gaps
- <none | list missing required coverage and related Required Violation IDs>

### Evidence
- Retained shared governance: <none | path1; path2>
- File actions: <none | Update path reason; Create path reason; Delete path reason>

### Next Action
- <single minimal next step, continuation command, rejection-closure action, or `none`>

### Verdict
- READY | NEEDS FIXES | BLOCKED | CONTINUE
