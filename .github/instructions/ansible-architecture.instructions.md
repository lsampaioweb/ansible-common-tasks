---
description: "Global architecture baseline for Ansible automation repositories with project layout, playbook sequencing, and idempotency conventions."
applyTo: "**"
---

# Ansible Architecture Baseline

## Dependencies

- For task-execution conventions (task key order, idempotency, and module declarations), defer to `ansible-playbook-style.instructions.md`.
- For role structure and variable-boundary rules, defer to `ansible-role.instructions.md`.
- For `ansible.cfg` runtime defaults and SSH settings, defer to `ansible-config.instructions.md`.

## Rules

### Project layout
- When all layered instruction files are active simultaneously, each file's rules govern its own file-type scope.
- Apply task-level style rules at the `.yml` file scope, not the project scope.
- Keep orchestration entry points as top-level playbooks directly under the `ansible/` root.
- Keep implementation logic inside roles under `ansible/roles/`.
- Keep execution order explicit by using numbered top-level playbook filenames when sequencing matters.
- Keep inventory and group variable ownership inside `ansible/inventory/` and `ansible/group_vars/`.
- Keep a shared `common` role for reusable OS-specific utilities.
- Import `common` role utilities via `tasks_from`.
- Store environment-specific values in `group_vars` or role `vars` files.

### Playbook declarations
- Keep each top-level playbook focused on a single concern (provisioning, updating, hardening, backup, restore).
- Compose multi-step pipelines from separate playbooks via `ansible.builtin.import_playbook` in a `site.yml`.

### Idempotency
- Keep playbooks idempotent so re-runs converge without unintended changes.
- Keep every play result deterministic: tasks must reach `ok` or `skipped` on a second run with no `changed` state on the target.

### Validation & Tooling
- Require all code to pass `ansible-lint` with **0 failures, 0 warnings** in production profile before code is merged.
- Maintain `.ansible-lint` configuration file in the project root with explicit `skip_list` and `warn_list` if project-specific exceptions are needed.
- Run `yamllint` on all YAML files to enforce consistent formatting.
- Validate YAML syntax on all playbooks and roles before committing.
- Document all ansible-core and ansible-lint versions used in CI/CD or development setup to ensure consistent validation across environments.

## Safety Guards

- Never substitute role-based task composition with ad-hoc shell script files.
- Never introduce host-destructive operations without an explicit user request.
- Never hardcode environment-specific values in playbook files.
