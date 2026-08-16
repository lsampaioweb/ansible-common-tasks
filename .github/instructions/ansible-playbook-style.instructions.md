---
description: "Playbook and task style contract for Ansible YAML files, including task naming, condition placement, and include/import policy."
applyTo: "**/*.yml"
---

# Ansible Playbook Style Contract

## Naming Conventions

- Start every `name` field — plays, tasks, handlers, and blocks — with a gerund verb.
- Use English for all `name` values across plays, tasks, handlers, and blocks.

## Rules

### Condition placement
- Place `when` immediately after `name` when the task has conditions.
- Group related tasks in a `block` when they share the same `when` expression.
- Use one `when` on the block instead of repeating it on individual tasks.

### Block structure
- Keep every block-scoped task under an enclosing task `name` that summarizes the group intent.
- Do not require a second nested `name` key directly under `block:` when the enclosing task already provides that summary.
- Add an inner named block only when multiple nested blocks exist and need distinct identities.
- Tag tasks included only for debugging with `["never", "debug"]` to exclude them from normal runs.
- Nest a block inside another block when its `when` condition evaluates a registered variable produced by the outer block's tasks.

### Task key order
- Keep task key order: `name` → `when` → `no_log` → module FQCN → `args`/`vars` → `loop`/`loop_control` → `register` → `changed_when`/`failed_when` → `notify`.

### Import vs include policy
- Use `ansible.builtin.import_tasks` for static task-file composition known at parse time.
- Use `ansible.builtin.include_tasks` only when runtime evaluation is required, such as dynamic filenames, loops, or host-fact-driven branching.
- Use `ansible.builtin.import_role` for static role composition known at parse time.
- Use `ansible.builtin.include_role` only when runtime evaluation is required, such as dynamic role names, loops, or host-fact-driven branching.
- Use `ansible.builtin.include_role` with `loop` when the same role must run multiple times with varying parameters.

### Module and play declarations
- Keep module invocations fully qualified (for example, `ansible.builtin.*`).
- Replace deprecated modules before their announced ansible-core removal version.
- Track deprecated-module replacements as planned work; do not defer migration past one minor release before removal.
- Keep top-level play declarations explicit with `hosts` and `become` when privilege escalation is required.
- Add `become: false` on tasks that must run as the calling user, not the become user.
- Set `become_user` when the operation must run as a specific non-root user.
- Always pair `become_user` with `become: true`.
- Add `delegate_to: "{{ host_to | default(omit, true) }}"` on tasks that must execute on a different host.
- Expose `delegate_to` as a caller-supplied variable.
- Use `run_once: true` for controller-side operations that must not repeat per managed host.
- Set `environment:` at the task level for variables scoped to that task.
- Set `environment:` at the play level only when all tasks in the play require the same environment.

### Loop conventions
- Use `loop: "{{ var | default([]) }}"` to guard every loop against undefined variables.
- Add `loop_control.label` with a meaningful field when looping over complex objects.
- Use `with_fileglob` for file-system glob patterns.
- Use `ansible.builtin.find` with `loop` for recursive or filtered file discovery.
- Write loop items with two or more fields in block style (`- key: value` on separate lines); never use multi-line flow style (`- {\n    key: value,\n  }`) because YAML formatters re-indent flow keys at a depth that conflicts with ansible-lint's indentation rules.

### Idempotency and error handling
- Use `changed_when: <register_var>.rc == 0` on `command` and `shell` tasks that perform state-changing operations.
- Use `changed_when: false` on read-only probe tasks whose purpose is inspection, discovery, or status checks.
- Keep the task `name` and any surrounding comment explicit enough to show that a `changed_when: false` command or shell task is a read-only probe.
- Use `failed_when` as a list of conditions when failure semantics require multiple guards.
- Use `retries` with `until` and `delay` for tasks that poll for an eventual state.
- Always set an explicit `retries` count on any task that uses `until`.
- Use `ignore_errors: true` only when a follow-up task immediately evaluates or reports the failure.
- Use `ansible.builtin.meta: flush_handlers` mid-play when a later task depends on the handler's side effect.
- Use `poll: 0` with `async` on service restart tasks.

### Boolean semantics guards
- Treat list-style `when`, `failed_when`, and `changed_when` as implicit logical AND across items.
- Never split an expression containing `or` across multiple list items.
- Keep `or` logic in a single expression item (or a single scalar expression) so semantics are preserved.
- Convert to multiple list items only when each item is intended to be AND-combined.
- Preserve original truth tables when refactoring condition syntax; style normalization must not alter behavior.
- After refactoring any `when`, `failed_when`, or `changed_when`, run syntax checks for all maintained inventories before merging.

### Variable and register conventions
- Pass parameters to roles exclusively through `vars` on the `import_role` or `include_role` call.
- Name `register` variables with a descriptive snake_case noun.
- Use `output` as the register variable name only in generic pass-through task wrappers.
- Use `r_<name>` as the register variable for intermediate results inside a block when the block promotes a cleaned result through `set_fact`.
- Use `default(omit, true)` for optional module parameters that must be absent when the caller does not supply a value.
- Guard variables that are unresolved during parser-only checks with `default(...)` when feasible so syntax-check remains actionable across inventories.
- Use `no_log: "{{ not (debug | default(false)) }}"` to allow debug visibility without permanently exposing secrets.

### Comments
- Write `# <reason>` comments only to document non-obvious intent, exception rationale, or operational boundary.

## Approved Exception Handling

- Use `include_*` instead of `import_*` when static imports would violate required runtime branching semantics.
- Keep a one-line comment above every `include_*` call explaining the runtime requirement; omit the comment when the `when` expression already fully describes the branching condition (for example, `when: ansible_facts['os_family'] == 'Debian'`).
- Use `ansible.builtin.command` or `ansible.builtin.shell` when no native module exists.
- Document the absence of a native module in a one-line comment directly above the task.
- Use `ansible.builtin.shell` only with `set -o pipefail` and an explicit `executable:` to prevent silent pipe failures.
- Use `failed_when: false` only when the next task immediately validates or reports the fallback behavior.
- Keep runtime-only variable dependencies explicit; when syntax-check cannot resolve a variable, document the expected source (`host_vars`, `group_vars`, or extra vars) near the task or role README.

## Safety Guards

- Never use `shell` or `command` for package management, service control, or file mutation when a dedicated Ansible module exists.
- Never use `ignore_errors: true` as a substitute for proper failure handling.
- Never leave `no_log` absent on any task that processes a password, token, or secret.
- Never write a comment that restates what the next line of code does.
- Never change boolean operator semantics (`and` vs `or`) during condition-style refactors.
