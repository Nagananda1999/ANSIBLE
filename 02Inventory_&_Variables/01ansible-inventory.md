# Ansible Inventory — Quick Revision

## 1. Inventory Basics

Inventory defines managed hosts, groups, and connection variables.

- Default file: `/etc/ansible/hosts`
- Custom source: pass `-i <inventory>`
- Common static formats: INI and YAML
- Default groups: `all` and `ungrouped`

```bash
# syntax
ansible-inventory -i <inventory> --graph

# example
ansible-inventory -i inventory.ini --graph
```

```
ansible-inventory -i inv.ini --graph
@all:
  |--@all_servers:
  |  |--@db_servers:
  |  |  |--db1
  |  |--@web_servers:
  |  |  |--web1
  |  |  |--web2
  |--@ungrouped:

```

---

## 2. Agentless Connections

Ansible normally connects without installing an agent on managed hosts.

- Linux/Unix: usually `ssh`
- Windows: commonly `winrm` or `psrp`
- Control node itself: `local`

---

## 3. INI Inventory

Bracketed names define groups.

```ini
# example
[web_servers]
web1 ansible_host=192.168.8.101
web2 ansible_host=192.168.8.102

[db_servers]
db1 ansible_host=192.168.8.201

[all_servers:children]
web_servers
db_servers
```

**A host in a child group is automatically part of its parent group.**

---

## 4. YAML Inventory

YAML is structured and preserves variable types consistently.

```yaml
# example
all:
  children:
    web_servers:
      hosts:
        web1:
          ansible_host: 192.168.8.101
        web2:
          ansible_host: 192.168.8.102
    db_servers:
      hosts:
        db1:
          ansible_host: 192.168.8.201
```

`web1`, `web2`, and `db1` are inventory hostnames. `ansible_host` is the address used for the connection.

---

## 5. Connection Variables

- `ansible_host`: remote hostname or IP address
- `ansible_connection`: connection plugin, such as `ssh`, `winrm`, `psrp`, or `local`
- `ansible_port`: remote connection port
- `ansible_user`: login user
- `ansible_password`: login password

```ini
# example
[linux]
app1 ansible_host=192.168.8.10 ansible_connection=ssh ansible_user=ansible

[windows]
win1 ansible_host=192.168.8.20 ansible_connection=winrm ansible_user=Administrator

[control]
localhost ansible_connection=local
```

Never store production passwords in plain text. Protect secrets with Ansible Vault.


---

## 6. Verification Commands

```bash
# show group/host tree
ansible-inventory -i inventory.ini --graph

# show full inventory with variables (JSON)
ansible-inventory -i inventory.ini --list

# show variables for one host
ansible-inventory -i inventory.ini --host web1

# list hosts matching a group
ansible web_servers -i inventory.ini --list-hosts

# test connectivity
ansible all -i inventory.ini -m ping
```

---

## 7. Dynamic Inventory and Multiple Sources

**Dynamic inventory** pulls hosts from an external source, such as a cloud provider or CMDB, at runtime through inventory plugins.

```yaml
# example: aws_ec2.yml (file name must end with aws_ec2.yml)
plugin: amazon.aws.aws_ec2
regions:
  - ap-south-1
```

```bash
# list available inventory plugins
ansible-doc -t inventory -l

# use dynamic inventory
ansible-inventory -i aws_ec2.yml --graph
```

**Multiple sources** can be combined.

```bash
# multiple -i flags
ansible-inventory -i inventory.ini -i aws_ec2.yml --graph

# a directory of inventory files (loaded alphabetically)
ansible-inventory -i inventory/ --graph
```

---

## Quick Rules to Remember

- Inventory maps logical names to managed hosts.
- Use groups to target related hosts together.
- Use `:children` in INI and `children:` in YAML.
- Use `ansible_host` for the real connection address.
- Use host ranges like `web[01:03]` for numbered hosts.
- Use aliases consistently in playbook host patterns.
- More specific variables win: host > child group > parent group > `all`.
- Extra vars (`-e`) override everything.
- Verify inventory with `--graph`, `--list` and `--host` before running playbooks.
- Use inventory plugins for dynamic or cloud hosts.
- Combine sources with multiple `-i` flags or an inventory directory.
- Prefer SSH keys over passwords.
- Store required passwords with Ansible Vault.