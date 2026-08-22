---
description: "Use to analyze logs and exceptions, identify root causes, and apply permanent fixes."
argument-hint: "Required: error logs, stack traces, or terminal output."
---

# Root Cause & Permanent Resolution Engine

## 1. Scope & Analysis
1. **Trace Analysis:** Parse the stack trace or log to isolate the exact originating line, class, and feature package.
2. **Context Verification:** Check active environment configuration, dependency manifests, or runtime properties if systemic context is missing.
3. **Diagnostic Execution:** Run targeted diagnostics (terminal commands or file search) to verify framework behaviors, documented defects, or version-specific edge cases.

## 2. Resolution Rules
- State the verified Root Cause and failure mechanism before writing or proposing any code modifications.
- Do not apply temporary workarounds.
- Do not bypass visibility modifiers.
- Do not suppress exceptions.
- Do not inject unapproved dependencies.
- Code fixes must adhere to packaging conventions defined by the active project architecture.
- Code fixes must adhere to data-access patterns defined by the active project architecture.
- Code fixes must respect dependency boundaries and detected runtime version constraints.
- Correct broken interface contracts when they cause the failure.
- Align data schemas when schema mismatch causes the failure.
- Fix lifecycle and boundary violations when they cause the failure.

## 3. Safety Guards
- **Safety Gate:** If the root cause cannot be verified with high confidence, do not generate speculative code fixes.
- **Fallacious/Ambiguous Logs:** If the log is truncated or missing critical details, stop and prompt for the specific missing block.
- **Fallback Output:** If unresolved, output:
  - List of attempted verification steps.
  - Precise technical uncertainties remaining.
  - The single next decisive diagnostic action for the developer to execute.

## 4. Review Plan Layout
Use this exact markdown schema:

### Scope
- Target: <error logs, stack traces, or terminal output>
- Mode: <read-only | apply-after-confirmation>

### Result
- Summary: <validated root cause and failure mechanism>

### Evidence
- Root cause evidence: <file path>:<line> | <log anchor>
- Impacted area: <class/file/package>
- Permanent fix plan: <structural correction>

### Unresolved Fallback (if unresolved)
- Attempted verification steps: <item1; item2>
- Remaining technical uncertainties: <item1; item2>
- Next decisive diagnostic action: <one action>

### Next Action
- <single minimal next step or `none`>

### Verdict
- READY | NEEDS FIXES | BLOCKED
