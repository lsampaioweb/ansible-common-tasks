---
description: "Jinja2 template contract for Ansible role templates: variable safety, block formatting, and rendered-output hygiene."
applyTo: "**/*.j2"
---

# Ansible Jinja2 Template Contract

## Naming Conventions

- Name template files after the destination filename and add the `.j2` suffix (for example `smb.conf.j2`, `sshd_config.j2`).

## Rules

- Use `{{ var | default('') }}` for optional string variables.
- Use `{{ var | default([]) }}` for optional list variables.
- Use `{# comment #}` for comments that must not appear in the rendered file.
- Use the target file format's native comment syntax for comments that must appear in the rendered output.
- Place `{% if %}`, `{% for %}`, `{% endif %}`, and `{% endfor %}` tags on their own lines.
- Use `loop.last` and `loop.first` for conditional rendering inside loops.
- Resolve complex expressions in task `vars:` before passing to the template.

## Safety Guards

- Never embed vault secrets or credential literals in template content; reference them only through variables.
- Never call `{{ lookup() }}` inside a template; resolve lookups in the calling task.
