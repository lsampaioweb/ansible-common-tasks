# Ansible Common Tasks

A reusable Ansible role providing OS-level utilities and system configuration tasks for multiple operating systems. This role is designed to be consumed by other roles via `import_role` with `tasks_from` to import specific task groups.

## Overview

This role provides modular, idempotent task collections for system administration across multiple OS families:
- **Debian/Ubuntu** — Package management, networking, filesystem, security, secrets
- **Darwin (macOS)** — Package management via Homebrew
- **FreeBSD** — Hostname, CA certificates, cleanup
- **Fortigate** — DNS configuration
- **Generic** — Mail, secrets management, utilities

## Supported Platforms

- Debian 10+
- Ubuntu 20.04+
- macOS (Darwin)
- FreeBSD
- Fortigate (partial support)

## Usage

Import specific task files from this role using `import_role` with `tasks_from`:

```yaml
- name: "Install APT packages"
  ansible.builtin.import_role:
    name: common
    tasks_from: debian/packages/apt
  vars:
    packages:
      - { name: "curl" }
      - { name: "git" }
    update_cache: true
```

## Task Areas

### Debian / Ubuntu

#### Known Hosts Management
- `debian/known_hosts/add.yml` — Add SSH host fingerprints to known_hosts
- `debian/known_hosts/add_current.yml` — Add fingerprints for current host
- `debian/known_hosts/remove.yml` — Remove fingerprints from known_hosts

#### Network Configuration
- `debian/network/default-name.yml` — Enable predictable network interface names
- `debian/network/ipv6-disable.yml` — Disable IPv6 via kernel parameters and NetworkManager

#### OS Configuration
- `debian/os/hostname.yml` — Configure system hostname and /etc/hosts
- `debian/os/timezone.yml` — Configure system timezone
- `debian/os/ntp-servers.yml` — Configure NTP server synchronization
- `debian/os/grub-menu.yml` — Configure GRUB boot menu options
- `debian/os/dns.yml` — Configure DNS resolver
- `debian/os/locale.yml` — Configure system locale
- `debian/os/machine-id.yml` — Configure unique machine identifier
- `debian/os/hardware-hotplug.yml` — Enable hardware hotplug support
- `debian/os/reboot-if-required.yml` — Detect and trigger system reboot if needed
- `debian/os/local-storage.yml` — Configure local storage (filesystems, LVM, mount points)
- `debian/os/nssdb.yml` — Manage NSS certificate databases
- `debian/os/trust-ca-certificate.yml` — Install and trust CA certificates
- `debian/os/ca-openssh-keypair.yml` — Create SSH Certificate Authority keypairs
- `debian/os/sign-ssh-keypair.yml` — Sign host SSH keys with CA keypair
- `debian/os/clean-up.yml` — Clean up system packages and cache
- `debian/os/ufw-install.yml` — Install and enable UFW firewall
- `debian/os/ufw-configure-rule.yml` — Configure UFW firewall rules

#### Package Management
- `debian/packages/apt.yml` — Install, upgrade, or remove APT packages (main handler)
- `debian/packages/apt-cloud-init.yml` — Wait for cloud-init completion and remove apt locks
- `debian/packages/pip.yml` — Install Python packages via pip
- `debian/packages/pipx.yml` — Install system-wide Python applications with pipx
- `debian/packages/pipx-fix-ssl.yml` — Fix SSL certificate validation issues in pipx
- `debian/packages/snap.yml` — Install Snap packages
- `debian/packages/ansible-galaxy.yml` — Install Ansible Galaxy collections/roles
- `debian/packages/docker.yml` — Install Docker and container runtime
- `debian/packages/fail2ban-install.yml` — Install Fail2ban intrusion detection
- `debian/packages/fail2ban-configure-jail.yml` — Configure Fail2ban jails
- `debian/packages/git-config.yml` — Configure Git client settings
- `debian/packages/google-chrome.yml` — Install Google Chrome browser
- `debian/packages/hashicorp.yml` — Install HashiCorp tools (Terraform, Consul, Vault, Nomad, Packer)
- `debian/packages/keepalived.yml` — Install Keepalived VRRP daemon
- `debian/packages/libsecret.yml` — Install libsecret credential storage backend
- `debian/packages/rsyslog.yml` — Configure Rsyslog logging daemon
- `debian/packages/vscode.yml` — Install Visual Studio Code
- `debian/packages/vscode-extensions.yml` — Install VS Code extensions
- `debian/packages/vscode-settings.yml` — Configure VS Code user settings
- `debian/packages/xclip.yml` — Install and configure clipboard manager
- `debian/packages/xrdp.yml` — Install remote desktop server

#### Password Management
- `debian/passwords/add.yml` — Add passwords to system credential storage
- `debian/passwords/get.yml` — Retrieve passwords from system credential storage

### macOS (Darwin)

#### Package Management
- `darwin/packages/homebrew.yml` — Install/upgrade packages with Homebrew
- `darwin/packages/pipx.yml` — Install system-wide Python applications with pipx
- `darwin/packages/ansible-galaxy.yml` — Install Ansible Galaxy collections/roles

### FreeBSD

#### OS Configuration
- `freebsd/os/hostname.yml` — Configure hostname in /etc/rc.conf
- `freebsd/os/trust-ca-certificate.yml` — Install and trust CA certificates
- `freebsd/os/clean-up.yml` — Clean up system packages

### Fortigate

#### Network Configuration
- `fortigate/dns/entry.yml` — Configure DNS entries in Fortigate

### Mail

- `mail/send.yml` — Send email notifications (supports SMTP with authentication)

### Secrets Management

#### Keychain (macOS)
- `secrets/keychain/read.yml` — Read secrets from macOS Keychain
- `secrets/keychain/write.yml` — Write secrets to macOS Keychain

#### Keyring (Linux)
- `secrets/keyring/read.yml` — Read secrets from GNOME Keyring via secret-tool
- `secrets/keyring/write.yml` — Write secrets to GNOME Keyring via secret-tool

#### Vault
- `secrets/vault/read.yml` — Read secrets from HashiCorp Vault
- `secrets/vault/write.yml` — Write secrets to HashiCorp Vault

### Utilities

- `utils/password/random.yml` — Generate random passwords
- `utils/format_certificate_serial.yml` — Format certificate serial numbers (Decimal to Hex with colons)

## Variable Patterns

All tasks follow consistent variable patterns:

### Optional Parameters
Optional parameters use `default(omit, true)`:
```yaml
install_recommends: "{{ item.install_recommends | default(omit, true) }}"
```

### Loop Guards
Loops are guarded against undefined variables:
```yaml
loop: "{{ packages | default([]) }}"
```

### Debug Logging
Secret-sensitive operations use conditional no_log:
```yaml
no_log: "{{ not (debug | default(false)) }}"
```

### Delegation
Tasks that execute on different hosts expose `delegate_to` as a variable:
```yaml
delegate_to: "{{ delegate_to | default(omit, true) }}"
```

## Common Task Variable Examples

### APT Packages
```yaml
packages:
  - name: "curl"
    state: "present"
    install_recommends: true
  - name: "old-package"
    state: "absent"
update_cache: true
upgrade: "safe"  # or 'full'
```

### UFW Firewall Rules
```yaml
rules:
  - name: "Allow SSH"
    rule: "allow"
    proto: "tcp"
    from_port: 22
    to_port: 22
  - name: "Deny all"
    policy: "deny"
```

### Mail
```yaml
mail_host: "smtp.gmail.com"
mail_port: 587
mail_secure: "starttls"
mail_subject: "System Alert"
mail_body: "Alert content"
mail_from: "sender@example.com"
mail_to: "recipient@example.com"
```

### SSH Keypairs
```yaml
ca_host_private_key_path: "/etc/ssh/ca_host_key"
principals: ["hostname", "hostname.example.com"]
```

### Storage Configuration
```yaml
volumes:
  - name: "data"
    pvs: ["/dev/sdb"]
    vg: "vg_data"
    size: "100G"
    mount_point: "/data"
```

## Handler Notifications

Common handlers available in `handlers/main.yml`:
- Applying sysctl configuration
- Updating GRUB
- Restarting services (SSH, fail2ban, docker, keepalived, etc.)
- Rebooting system
- Network reload/restart
- Updating CA certificates
- Refreshing shell environment

## Requirements

- Ansible 2.10+
- Target system must have Python 2.7+ or Python 3.x

## License

MIT

## Author

Luciano Sampaio
