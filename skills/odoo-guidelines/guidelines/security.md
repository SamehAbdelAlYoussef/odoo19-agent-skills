# Access rights

<!-- adapted for Odoo 19.0: ir.access (unified model) was introduced in 20.0; Odoo 19 uses ir.model.access + ir.rule -->

Odoo 19 uses **two separate mechanisms** for access control:

1. **`ir.model.access`** (ACLs) — declared in `security/ir.model.access.csv` with columns
   `id,name,model_id:id,group_id:id,perm_read,perm_write,perm_create,perm_unlink`.
   Each row grants a group (or everyone when `group_id` is empty) read/write/create/unlink
   permissions on a model (`1` = allow, `0` = deny). Rows for the same group are OR-ed;
   an empty `group_id` applies to all users.
   - Default deny: a model with no ACL row is inaccessible to non-superusers.
   - Granting to everyone is done by leaving `group_id` blank. Flag any `perm_write`,
     `perm_create`, or `perm_unlink` granted without a group (world-writable).
   - Flag `perm_write`/`perm_create`/`perm_unlink` granted to `base.group_portal` or
     `base.group_public`.

2. **`ir.rule`** (record rules) — declared in `security/<module>_security.xml`.
   They filter which *records* a group can access within the model-level permission.
   - A rule with `groups` is a *group rule*: only applies to members of those groups.
     Multiple group rules for the same group are OR-ed; rules across groups are OR-ed.
   - A rule without `groups` is a *global rule*: AND-ed onto every user's effective domain.
   - Multi-company models need a global rule with `[('company_id', 'in', company_ids)]`;
     append `| company_id = false` when the company field is optional.
   - `domain_force` uses the `user` and `time` variables available in record-rule domains.

For auditing beyond the data files (`sudo()`, SQL, public methods, ...), use the
`odoo-security` skill.
