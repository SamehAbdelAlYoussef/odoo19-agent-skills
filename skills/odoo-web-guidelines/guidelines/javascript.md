# Organize files by feature, not by type

Code is organized by feature, not by type. Instead of folders like `components/`,
`plugins/`, `services/` or `hooks/`, each feature gets one folder holding all of its
files: `notification/` contains the notification component, its plugin, its template
and its styles.

# Avoid getters

A getter hides the fact that a computation happens, and only saves a pair of
parentheses. Write a plain function instead: the call site then shows that code runs,
and it can take an argument the day the computation needs one.

<!-- adapted for Odoo 19.0: OWL 2.8.2 (used in Odoo 19) does not have a `computed` hook.
     Use `useState` for reactive state. For derived values, compute them in the render
     method or in a plain function — OWL's reactive system will re-render when reactive
     state read during render changes. The `computed` hook exists in OWL 3.x (Odoo 20+). -->
When the value is derived reactive state, store it in `useState` and update it when
its dependencies change; for values that only need recalculating on each render, a
plain function is sufficient. (The `computed` hook mentioned in Odoo 20.0 docs does not
exist in OWL 2.8.2.)

# Avoid patching JavaScript code

Patching (the `patch` function) is strongly discouraged inside Odoo itself. It is fine
outside of Odoo. A patch makes the code hard to reason about, since reading a snippet
no longer tells you what will run, and harder to maintain, since the patch and the code
it targets drift apart. Usually there is a better solution: design a proper extension
point.
