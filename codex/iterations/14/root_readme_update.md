# Root README Update — Iteration 14

Add a new section to the root `README.md` documenting the reference tracking
capability introduced in Iteration 14. Also update the existing "Local Knowledge
Library" section to reflect the new inbox layout.

---

## Suggested new section

### External File References

Iteration 14 lets you point a mod at **any file in your repository** (or filesystem)
without copying it into the inbox.

Create or edit the `references.md` file inside the mod's inbox:

```
.codex_library/mods/<mod_id>/inbox/references.md
```

Example contents:

```markdown
# CIM model SQL views
c:\dev\git\my-repo\sql\views\08_CIM_Views.sql

# Architecture README
c:\dev\git\my-repo\README.md
```

The pipeline reads `references.md` on every `process` run.
If a referenced file has changed since the last run, its derived artifacts
(notes, summaries, indices) are automatically **invalidated and regenerated**.

Supported formats for referenced files:

```
.md  .txt  .html  .pdf  .sql  .xml  .json  .yaml  .yml  .py  .csv
```

A full template with format instructions is placed in:

```
.codex_library/REFERENCES_TEMPLATE.md
```

Every new mod receives an empty `references.md` stub automatically.

---

## Update to "Local Knowledge Library" section

Replace the inbox paragraph:

> Users can add documents to a mod by placing files in:
>
> ```
> .codex_library/mods/<mod_id>/inbox/
> ```

With:

> Users can add source documents to a mod in two ways:
>
> **Option A — drop files into the inbox**
>
> ```
> .codex_library/mods/<mod_id>/inbox/
> ```
>
> Inbox files are tracked by content hash and modification date.
>
> **Option B — reference external files**
>
> Edit `inbox/references.md` to list absolute paths to files elsewhere on disk:
>
> ```
> .codex_library/mods/<mod_id>/inbox/references.md
> ```
>
> Referenced files are tracked by modification date. The pipeline detects
> changes automatically on every `process` run.

---

## Updated local library layout

```
.codex_library/
  REFERENCES_TEMPLATE.md       ← format guide for references.md files
  registry.json
  mods/
    <mod_id>/
      inbox/
        references.md          ← optional: paths to external files to ingest
        <drop documents here>
      sources/
      processed/
      notes/
      summaries/
      indices/
      manifests/
        state.json             ← tracks processed_docs + referenced_files
      mod.json
```
