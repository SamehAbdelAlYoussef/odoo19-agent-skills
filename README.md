# odoo19-agent-skills

Odoo's official Skill Library for AI coding agents, adapted for **Odoo 19.0**.

The originals live in the `skills/` folder of the `20.0` branch of [odoo/odoo](https://github.com/odoo/odoo/tree/20.0/skills). They are written for Odoo 20, so this repo keeps a copy adjusted to Odoo 19 conventions, verified against the Odoo 19.0 source.

## Quick start for the team

Open Claude Code at the root of your Odoo 19 project and paste this prompt:

```
Install the Odoo 19 agent skills from https://github.com/SamehAbdelAlYoussef/odoo19-agent-skills into this project. Read that repo's README.md and follow the "Installation instructions for Claude" section exactly, then report the result.
```

When it finishes, exit the session (`/exit`) and start a new one. From then on, Claude uses the skills automatically whenever it writes or reviews Odoo code.

To confirm it worked, ask: `List the skills you can see`. All four Odoo skills should appear.

## What the skills do

| Skill | Used automatically when |
| --- | --- |
| `odoo-guidelines` | Writing or reviewing any addon file outside `static/`: module structure, manifest, Python/ORM, fields, controllers, XML views and data, QWeb reports, access rights, performance, tests |
| `odoo-web-guidelines` | Writing or reviewing JavaScript, Owl templates and SCSS under `static/` |
| `odoo-review` | Reviewing a diff, commit, PR or module. Dispatches each changed file to the matching rules, then judges the change on its merits |
| `odoo-security` | Auditing code, or judging whether `sudo()`, raw SQL, a route, a public/RPC method or an access rule is safe |

The four skills reference each other and must be installed together.

## Changes from upstream (20.0 → 19.0)

Every edit is marked in the files with `<!-- adapted for Odoo 19.0 -->`.

| File | What changed |
| --- | --- |
| `odoo-guidelines/guidelines/security.md` | Full rewrite: `ir.access` (20.0 only) → Odoo 19 two-mechanism system (`ir.model.access.csv` + `ir.rule`) |
| `odoo-guidelines/guidelines/module_structure.md` | `ir.access.csv` → `ir.model.access.csv` |
| `odoo-guidelines/SKILL.md` | Access rights table row |
| `odoo-security/SKILL.md` | Description and access-control section |
| `odoo-review/SKILL.md` | Sibling-skill table row |
| `odoo-web-guidelines/guidelines/javascript.md` | Removed Owl `computed` (Owl 3.x / 20.0+ only), replaced with `useState` guidance |

All other rules were checked against the Odoo 19.0 source and apply unchanged.

## Repository layout

```
skills/      ← the 4 adapted skills (this is what gets installed)
upstream/    ← untouched copy from odoo/odoo 20.0, kept for diffing future updates
README.md
LICENSE
```

---

## Installation instructions for Claude

These steps are for an AI coding agent (Claude Code or similar) installing the skills into the project it is running in. Follow them in order.

1. **Confirm the target.** The current working directory is the target project. Check it is an Odoo project (addons with `__manifest__.py` files). If the Odoo source is present (`odoo/release.py`), read the version; if it is not 19.x, warn the user that these skills are adapted for 19.0 and ask whether to continue.

2. **Fetch the repo** into a temporary folder:
```bash
   git clone --depth 1 https://github.com/SamehAbdelAlYoussef/odoo19-agent-skills.git /tmp/odoo19-agent-skills
```

3. **Install the skills.** Copy every folder inside `/tmp/odoo19-agent-skills/skills/` into `.claude/skills/` at the project root (create it if needed). If a skill with the same name already exists there, move the old one to `.claude/skills-backup-<date>/` first and tell the user. Do not copy `upstream/`.

4. **Check the skills load.** Each `.claude/skills/<name>/SKILL.md` must start with a valid YAML frontmatter block (`---` / `name:` / `description:` / `---`). Confirm all four are present and valid.

5. **Enable automatic use.** Create `CLAUDE.md` at the project root if it does not exist. If it does not already contain a `## Odoo skills` section, append this section verbatim, filling in the detected version:

```markdown
   ## Odoo skills
   - This project targets Odoo <detected version, e.g. 19.0>. When a skill rule conflicts with the Odoo source in this workspace, the source wins.
   - Access rights in Odoo 19 use ir.model.access.csv + ir.rule. ir.access does not exist in 19.
   - Before writing or editing any file in an Odoo addon, read the matching sections of the odoo-guidelines skill (or odoo-web-guidelines for anything under static/). Only read the sections relevant to the files being touched.
   - After finishing any code change, run the odoo-review skill on the diff and fix blocking findings before reporting the task as done.
   - For anything involving sudo(), raw SQL, controllers/routes, public or RPC-callable methods, or access rights, also apply the odoo-security skill.
```

   If a `## Odoo skills` section already exists, leave it as is and tell the user.

6. **Clean up** `/tmp/odoo19-agent-skills`.

7. **Report** to the user: the version detected, the four skills installed, whether `CLAUDE.md` was created or updated, any backups made. Then tell them to restart the session (`/exit`, then start again) so the skills and `CLAUDE.md` are loaded.

Do not modify the skill files during installation.

## Updating

To update an existing install, give Claude Code the same prompt again. It backs up the old skills and installs the current version.

Maintainers: to pull new upstream changes, sparse-clone the `skills/` folder from the `20.0` branch of odoo/odoo, diff it against `upstream/`, port relevant changes into `skills/` with the 19.0 adaptations kept, then replace `upstream/`.

## License and credit

The skills are the work of Odoo S.A., published in [odoo/odoo](https://github.com/odoo/odoo) under the GNU LGPL v3. This adapted copy is distributed under the same license. See `LICENSE`.
