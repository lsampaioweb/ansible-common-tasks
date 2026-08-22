---
description: "Role governance contract for Ansible roles, including directory ownership, task composition, and variable boundaries."
applyTo: "**/roles/**"
---

# Ansible Role Governance

## Dependencies

- For task-execution conventions (idempotency, variable passing, and `changed_when`/`failed_when` keys), defer to `ansible-playbook-style.instructions.md`.
- For playbook and task naming conventions, see `ansible-playbook-style.instructions.md`.

## Naming Conventions

- Name role directories with lowercase snake_case.
- Name role task files with lowercase kebab-case or snake_case.
- Use one casing convention consistently within each role.
- Name variable keys with lowercase snake_case.

## Rules

### Directory ownership
- Keep each role self-contained with `tasks`, `vars`, `handlers`, `templates`, `files`, and `meta` directories as needed.
- Keep `tasks/main.yml` as the role entry point that orchestrates internal task files.
- Keep template rendering in `templates/*.j2` and static artifacts in `files/`.
- Keep OS-specific task trees under `tasks/<os_family>/` (for example `tasks/debian/`, `tasks/ol9/`, `tasks/windows/`).
- Keep reusable cross-role utilities as importable task files in a dedicated `common` role.
- Declare role dependencies and Galaxy collection requirements in `meta/main.yml` with required fields: `role_name` and `namespace`.

### Role Metadata (`meta/main.yml`)
- Required structure:
  ```yaml
  ---
  galaxy_info:
    role_name: my_role        # lowercase, no hyphens; uniquely identifies role
    namespace: my_namespace   # lowercase; must match ^[a-z][a-z0-9_-]+$ (no dots)
    author: Author Name
    description: "Role description"
    license: MIT              # required field
    min_ansible_version: "2.10"  # required field; optional fields (tags, github_branch, etc.) below per ansible-galaxy requirements
  ```
- Namespace values must match `^[a-z][a-z0-9_-]+$`.
- Do not use dot-separated namespace values (for example, `local.proxmox`).
- Run `ansible-lint` to validate `meta/main.yml` for Galaxy compliance before marking role complete.
- Do not assume default role name from directory; always explicitly declare `role_name` and `namespace`.

### Variable boundaries
- Keep role defaults and vars scoped to the role.
- Use `vars/main.yml` for role-internal constants that must not be overridden by callers.
- Keep host- or environment-wide values in `group_vars` or role `vars` files.
- Reference group_vars values through named variables, not through direct lookup calls inside roles.

### Handlers
- Use handlers for restart, reload, and flush operations.
- Define all role handlers in `handlers/main.yml`.
- Keep handler names unique across the entire play scope.
- Trigger handlers through `notify`.

### Cross-role reuse
- Keep cross-role reuse explicit through `import_role` or `include_role`.
- Use `ansible.builtin.import_role` for static role composition known at parse time.
- Use `ansible.builtin.include_role` when static import cannot satisfy the use case (including but not limited to: dynamic role names, loops, or host-fact-driven branching).
- Use `ansible.builtin.include_role` with `loop` when the same role must run multiple times with varying parameters.
- Pass parameters to roles exclusively through `vars` on the `import_role` or `include_role` call.
- Use `tasks_from` on `import_role`/`include_role` to target a specific task file.

## Safety Guards

- Never restart services directly in task bodies when a handler can manage the restart.
- Never copy tasks from one role into another.
- Never place secrets directly in role task files, templates, or static files.
- Never perform irreversible state changes in a role without a scoped condition or explicit user intent.
- Never use bare variable references without `default([])` or `default(omit, true)` guards in loops and optional parameters.
