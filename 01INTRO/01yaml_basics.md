# YAML Basics — Quick Revision

## 1. Data Types in YAML

### Collections
| Type | Also called | Example |
|------|-------------|---------|
| List | Sequence / Array | `- web` |
| Dictionary | Mapping / Hash | `key: value` |

### Scalars
| Type | Examples |
|------|----------|
| String | `hello`, `"quoted"`, `'single'` |
| Integer | `42`, `-7`, `0xFF` |
| Float | `3.14`, `6.02e23` |
| Boolean | `true` / `false` (also `yes`/`no`, `on`/`off`) |
| Null | `null`, `~`, or empty value |

### Multiline strings
```yaml
literal: |          # keeps newlines
  line one
  line two

folded: >           # folds into a single line
  line one
  line two
```

### Anchors & Aliases (reuse a block)
```yaml
defaults: &base
  timeout: 30

prod:
  <<: *base         # merge defaults
  timeout: 60       # override
```

---

## 2. All Types Together

```yaml
name: deploy-app          # string
port: 8080                # integer
enabled: true             # boolean
retries: null             # null
tags: [web, prod]         # list (inline)
config:                   # dictionary
  timeout: 30.5           # float
  hosts:                  # nested list
    - db1
    - db2
script: |                 # multiline string
  echo hello
  exit 0
```

---

## 3. List Styles — Flow vs Block

Both produce the **same data**: `tags = ["web", "prod"]`

```yaml
# Flow style (inline, JSON-like) — good for short lists
tags: [web, prod]
```

```yaml
# Block style — good for long/readable lists
tags:
  - web
  - prod
```

---

## 4. Indentation of List Items

### Rule
Child content should be indented **more** than its parent key.

```yaml
# Not indented — works in Ansible/PyYAML, but fragile
tags:
- web
- prod
```

```yaml
# Indented — correct and recommended
tags:
  - web
  - prod
```

Both usually parse to the same list, but the indented form is unambiguous.

### Why it matters when nested
```yaml
# Hard to read — is "tags" under the task?
tasks:
  - name: Deploy app
    tags:
    - web
    - prod
```

```yaml
# Clear — tags clearly belongs to the task
tasks:
  - name: Deploy app
    tags:
      - web
      - prod
```

---

## 5. Common Types in Ansible

| Use case | Type |
|----------|------|
| Hostnames, paths, commands | String |
| `become: true`, `gather_facts: false` | Boolean |
| Ports, retries, timeouts | Integer / Float |
| Clear or omit a value | Null |
| Shell scripts, templates | Multiline string (`|`, `>`) |
| Tasks, handlers, inventory groups | List of dicts |
| `vars`, `group_vars`, module args | Dict of dicts |

---

## 6. Quick Rules to Remember

- Use **spaces only** — never tabs.
- Keep indentation consistent (2 spaces is standard).
- Indent list items under their parent key.
- Quote strings containing `: `, `#`, `{`, `}`, `[`, `]`, or starting with `*`/`&`.
- Quote values like `yes`, `no`, `on`, `off` when you mean the literal text.
- `---` starts a document, `...` ends it (optional).
- `#` starts a comment.
