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

These steps are for an AI coding agent (Claude Code or similar) installing the skills into the project it is running in. Run the preflight checks first. **If any check marked STOP fails, do not install anything**: tell the user which check failed, what you found, and how to fix it, then end.

### Preflight checks

1. **Odoo source present (STOP).** Look for `odoo/release.py` in the current working directory, or at most two folder levels below it. If none is found, stop: tell the user to open Claude Code at the root of their Odoo 19 project (the folder containing the Odoo source and their addons).

2. **Odoo version is 19 (STOP).** Read `version_info` from the `release.py` found. If its first element is not `19`, stop: tell the user which version was found and that these skills are adapted for Odoo 19 only.

3. **Working directory is the project root (STOP).** The current working directory must be the folder that contains the Odoo source (or its parent project folder), not a subfolder such as a single addon, `odoo/`, or `addons/`. If the current directory is inside the Odoo source or inside an addon, stop and tell the user which folder to open Claude Code in instead.

4. **Git available (STOP).** Run `git --version`. If git is missing, stop and tell the user to install git.

5. **Addons present (warning only).** Find addon folders (containing `__manifest__.py`) outside the Odoo core source. If none are found, warn the user but continue. If any have a manifest `version` that clearly targets another major version (e.g. starts with `17.0` or `18.0`), list them in the report.

6. **Existing install (info).** Note whether `.claude/skills/` already contains any of the four skill names and whether `CLAUDE.md` already has a `## Odoo skills` section. These are handled below, not blockers.

Report the preflight results to the user in a short table (check, result, details) before continuing.

### Install

7. **Fetch the repo** into a temporary folder (use the system temp directory; on Windows `/tmp` may not exist):
```bash
   git clone --depth 1 https://github.com/SamehAbdelAlYoussef/odoo19-agent-skills.git <temp>/odoo19-agent-skills
```
   If the clone fails, stop and report the error.

8. **Install the skills.** Copy the four folders inside `<temp>/odoo19-agent-skills/skills/` (`odoo-guidelines`, `odoo-web-guidelines`, `odoo-review`, `odoo-security`) into `.claude/skills/` at the project root (create it if needed). If a skill with the same name already exists there, first move the old one to `.claude/skills-backup-<YYYYMMDD-HHMM>/`. Copy nothing else: not `upstream/`, not any README.

9. **Enable automatic use.** Create `CLAUDE.md` at the project root if it does not exist. If it does not already contain a `## Odoo skills` section, append this section verbatim, filling in the detected version:

```markdown
   ## Odoo skills
   - This project targets Odoo <detected version, e.g. 19.0>. When a skill rule conflicts with the Odoo source in this workspace, the source wins.
   - Access rights in Odoo 19 use ir.model.access.csv + ir.rule. ir.access does not exist in 19.
   - Before writing or editing any file in an Odoo addon, read the matching sections of the odoo-guidelines skill (or odoo-web-guidelines for anything under static/). Only read the sections relevant to the files being touched.
   - After finishing any code change, run the odoo-review skill on the diff and fix blocking findings before reporting the task as done.
   - For anything involving sudo(), raw SQL, controllers/routes, public or RPC-callable methods, or access rights, also apply the odoo-security skill.
```

   If a `## Odoo skills` section already exists, leave it unchanged and tell the user.

### Verify

10. **Check the install.** All four `.claude/skills/<name>/SKILL.md` files exist and each starts with a valid YAML frontmatter block (`---`, `name:`, `description:`, `---`), and `CLAUDE.md` contains the `## Odoo skills` section. If anything is wrong, fix it by recopying from the temp clone; if it still fails, report it.

11. **Clean up** the temp clone.

12. **Report:** the Odoo version and project root detected, the four skills installed, whether `CLAUDE.md` was created or updated, any backups made, and any warnings from preflight. Then tell the user: "Exit this session (`/exit`) and start Claude Code again in this same folder. The skills become active on the next session."

Do not modify the skill files during installation.

## Updating

To update an existing install, give Claude Code the same prompt again. It backs up the old skills and installs the current version.

Maintainers: to pull new upstream changes, sparse-clone the `skills/` folder from the `20.0` branch of odoo/odoo, diff it against `upstream/`, port relevant changes into `skills/` with the 19.0 adaptations kept, then replace `upstream/`.

## License and credit

The skills are the work of Odoo S.A., published in [odoo/odoo](https://github.com/odoo/odoo) under the GNU LGPL v3. This adapted copy is distributed under the same license. See `LICENSE`.
