# Ansible Common Tasks

Reusable tasks shared by all project roles.

#
### Debian Task Areas

### 1. Known Hosts

1. Add and remove target fingerprints in `known_hosts`.

### 2. Network

1. Configure predictable network interface names.
1. Disable IPv6 (GRUB, sysctl, hosts file, and NetworkManager profiles).

### 3. OS

1. Configure timezone.
1. Configure NTP servers.
1. Configure GRUB boot menu.
1. Configure UFW and firewall rules.
1. Validate reboot requirements.

### 4. Packages

1. APT package lifecycle (install, upgrade, cleanup).
1. pipx package management.
1. Snap package management.
1. Tool-specific package tasks such as vscode, hashicorp, docker, git, and fail2ban.

### 5. Password and Secrets

1. Add and retrieve passwords from the secret manager.

#
### Created by:

1. Luciano Sampaio.
