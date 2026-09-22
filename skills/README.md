# Odoo Skills

Four skills cloned from the official Odoo repository (branch `20.0`) and adapted for **Odoo 19.0**.

- Working copy: `.claude/skills/` (this directory)
- Untouched upstream snapshot: `.claude/skills-upstream/` (diff against it to see future upstream changes)

## Skills

| Skill | Purpose |
|---|---|
| `odoo-guidelines` | House rules for addon code outside `static/` |
| `odoo-web-guidelines` | House rules for JS, OWL templates, SCSS under `static/` |
| `odoo-review` | Review a diff, commit, or PR against the house rules |
| `odoo-security` | Security audit: sudo, SQL, controllers, public methods, access rights |

## Changes from upstream

Every change is marked `<!-- adapted for Odoo 19.0 -->` in the file it touches.

| File | What changed | Marker present |
|---|---|---|
| `odoo-guidelines/guidelines/security.md` | Full rewrite: `ir.access` → `ir.model.access` + `ir.rule` two-mechanism system | ✓ top of file |
| `odoo-guidelines/guidelines/module_structure.md` | `ir.access.csv` → `ir.model.access.csv` | ✓ inline |
| `odoo-guidelines/SKILL.md` | Access rights table row | ✓ inline |
| `odoo-security/SKILL.md` | Description + access-control section | ✓ two inline markers |
| `odoo-review/SKILL.md` | Sibling-skill table row | ✓ inline |
| `odoo-web-guidelines/guidelines/javascript.md` | Removed `computed` (OWL 3.x / Odoo 20+ only); replaced with `useState` guidance for OWL 2.8.2 | ✓ block comment |

## Updating from upstream

```bash
# Re-fetch the upstream snapshot
git clone --depth 1 --filter=blob:none --sparse --branch 20.0 \
    https://github.com/odoo/odoo.git /tmp/odoo-skills-src
cd /tmp/odoo-skills-src && git sparse-checkout set skills

# Diff to see what changed
diff -rq /path/to/project/.claude/skills-upstream/ /tmp/odoo-skills-src/skills/

# Copy new upstream into the snapshot, then re-apply adaptations
cp -r /tmp/odoo-skills-src/skills/* /path/to/project/.claude/skills-upstream/
```

Re-apply every row in the table above after updating.
