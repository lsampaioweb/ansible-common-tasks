---
description: "ansible.cfg governance contract for runtime defaults, connection behavior, and safe automation settings."
applyTo: "**/ansible.cfg"
---

# Ansible Configuration Contract

## Naming Conventions

- Place `[defaults]` before `[ssh_connection]`.
- Place any project-specific sections after the standard sections.
- Write `# <reason>` comments on the line above the key they document.
- Use backticks when referencing section names in prose: `` `[defaults]` ``, `` `[ssh_connection]` ``.

## Rules

### General formatting
- Do not quote scalar values.

### Inventory and execution
- Keep `inventory` set to the repository-managed inventory path (for example `inventory/hosts` or `inventory/home`).
- Always set `forks` explicitly.
- Always set `retry_files_enabled = false`.
- Set `gathering = smart` for SSH-based plays.
- Set `gathering = explicit` only when no facts are needed for the play.

### Callbacks and profiling
- Set `callbacks_enabled = timer, profile_roles` as the baseline for all projects.
- Add project-specific plugins to `callbacks_enabled` only when the plugin provides observability or profiling value specific to your playbook domain (for example, `profile_tasks` for task-level timing diagnostics).

### Fact caching
- Set `fact_caching`, `fact_caching_connection`, and `fact_caching_timeout` explicitly when the project benefits from caching.
- Omit fact-caching directives (`fact_caching`, `fact_caching_connection`, `fact_caching_timeout`) when playbooks make no use of `ansible_facts` lookups or role-level facts.

### SSH connection
- Set `pipelining = true` when privilege escalation does not conflict with requiretty.
- Set `timeout` explicitly.
- Set `ssh_args` with `ControlMaster=auto` and `ControlPersist`.
- Choose a `ControlPersist` duration proportional to the longest expected task gap.
- Keep `host_key_checking` enabled by default.

### Linting configuration
- Maintain `.ansible-lint` at the project root with explicit `skip_list` and `warn_list` entries for project-specific exceptions.
- Optionally override root `.ansible-lint` in `roles/common/.ansible-lint` or other specific roles when they have unique linting requirements (for example, roles with deprecated modules in `files/` or special template conventions).
- When a role does not have its own `.ansible-lint`, it inherits the root project configuration during `ansible-lint` runs.
- Document any role-specific lint suppressions with `# noqa: <rule>` comments directly in the affected code.

## Safety Guards

- Never set permissive defaults that expose secrets in callback output.
- Never disable `host_key_checking` without an inline comment explaining the operational boundary.
