# Ansible Configuration — Quick Revision

## 1. Configuration File

`ansible.cfg` is the main configuration file used to control how Ansible behaves, such as inventory location, SSH settings, privilege escalation, and output behavior.

Common sections:

```ini
[defaults]
[inventory]
[privilege_escalation]
[ssh_connection]
[colors]
```

Example ansible.cfg:

```
[defaults]
inventory = ./inventory.ini
remote_user = ansible
host_key_checking = False
timeout = 30

[privilege_escalation]
become = True
become_method = sudo
become_user = root
```

Important Sections

`[defaults]`→ Controls general Ansible settings such as inventory, SSH user, and timeout.
`[privilege_escalation]`→ Controls sudo/root access.
`[ssh_connection]` → Controls SSH connection behavior and options.
`[inventory]` → Controls how Ansible processes inventory.
`[persistent_connection]` → Controls persistent connection settings, commonly for network devices.

---

## 2. Search Order

Ansible uses the first configuration file found:

1. `ANSIBLE_CONFIG`
2. `./ansible.cfg`
3. `~/.ansible.cfg`
4. `/etc/ansible/ansible.cfg`

```bash
# syntax
export ANSIBLE_CONFIG=<absolute-config-path>

# example
export ANSIBLE_CONFIG=/opt/ansible/ansible.cfg
```

Ansible may ignore `./ansible.cfg` when the current directory is world-writable.

---

## 3. Environment Overrides

Environment variables override matching `ansible.cfg` settings.

```bash
# syntax
export ANSIBLE_<SETTING>=<value>

# example
export ANSIBLE_GATHERING=explicit
```

`explicit` disables automatic fact gathering unless a play sets `gather_facts: true`.

---

## 4. Configuration Commands

### `ansible-config list`

Lists available configuration options.

```bash
# syntax
ansible-config list

# example
ansible-config list
```

### `ansible-config view`

Displays the active configuration file.

```bash
# syntax
ansible-config view

# example
ansible-config view
```

### `ansible-config dump`

Shows resolved settings. `--only-changed` limits output to overrides.

```bash
# syntax
ansible-config dump [--only-changed]

# example
ansible-config dump --only-changed
```

Use `ansible-config --version` to see the selected configuration file path.

---

## Quick Rules to Remember

- Use lowercase configuration section names.
- Keep project-specific `ansible.cfg` in the project directory.
- Use an absolute path with `ANSIBLE_CONFIG`.
- Environment variables override config-file settings.
- Only the first configuration file found is used.
- Use `ansible-config dump --only-changed` to inspect overrides.
