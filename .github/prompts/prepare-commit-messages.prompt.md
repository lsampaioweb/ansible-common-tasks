---
description: "Use to cluster uncommitted changes into logical, feature-scoped Conventional Commits and execute after approval."
argument-hint: "Required: path or feature-scope filter; omit to cluster all uncommitted changes."
---

# Logical Git Commit Engine

## 1. Scope & Analysis
- Inspect uncommitted changes (`git status`, `git diff`).
- Cluster files into atomic commits.

## 2. Resolution Rules
- **Sort Order:** Commit foundational changes (config, schemas, deps) before feature layers.
- **Grouping Boundary:** Group files strictly by feature domain (e.g., `auth`, `payment`).
- **Documentation Gate:** If Markdown documentation exists for changed code but contains no corresponding updates, flag the affected docs and recommend running `review-and-sync-docs` before proceeding.
- **Commit Format:** Use Conventional Commits only: `type(scope): description`.
- **Scope Rule:** Use a feature domain or infrastructure area.
- **Description Tense:** Write the description in imperative present tense.
- **Description Length:** Keep the description at most 50 characters.
- **Description Punctuation:** Do not end the description with a period.
- **Body Rule:** Document the *why* and the *impact* only.
- **Footer Rule:** Append `Closes #123` or tracking IDs if detected in branch or context.
- **Post-approval:** Run `git add` and `git commit` sequentially for each cluster.

## 3. Safety Guards

- **Execution Boundary:** Apply changes only after the commit plan is confirmed.
- **Ask for Confirmation:** Output: `Proceed with executing this automated commit sequence? [yes/no]`
- Confirm the exit code is 0 after each git commit before staging the next cluster.
- Never use technical-layer identifiers for grouping or as the commit scope.
- Never list modified files or duplicate diff data in the commit body.
- If files in a cluster span more than one feature domain, flag the cluster and stop.
- Never use commit types not defined by the Conventional Commits specification.

## 4. Review Plan Layout
Use this exact markdown schema:

### Scope
- Target: <workspace or filtered scope>
- Mode: <read-only | apply-after-confirmation>

### Result
- Summary: <number of commit clusters>
- Documentation gate: CLEAR | PENDING SYNC

### Sequence
- S1 | Header: `type(scope): description` | Files: <path1; path2>
- S<N> | Header: `type(scope): description` | Files: <path...>

### Evidence
- Commit body blueprint: `Why this change is needed and its impact.`
- Documentation gap details: <none | item1; item2>

### Next Action
- <single minimal next step or `Proceed with executing this automated commit sequence? [yes/no]`>
