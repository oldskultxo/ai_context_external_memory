# Iteration 14 Implementation Prompt — codex_context_referenced_files

Extend the knowledge mod system from Iterations 11–13 with two capabilities:
**external file references** and **modification-time-based invalidation**.

---

## Goal

A mod inbox currently only accepts documents dropped physically into the directory.
This forces users to copy source files, which is impractical when the source is a
living artifact elsewhere in the repository (e.g. a SQL view file, an XML spec, a
README that is constantly updated).

Iteration 14 solves this with a `references.md` pointer file and robust change
detection for all tracked files.

---

## 1. The `references.md` file

### Location

Each mod's inbox contains a special file:

```
.codex_library/mods/<mod_id>/inbox/references.md
```

It is **never** treated as a document to ingest — it is a manifest of external paths.

### Format

```markdown
# Label for the next path (optional comment)
/absolute/or/relative/path/to/file.sql

# Another label
c:\path\to\another\file.md
```

- One file path per line.
- Lines starting with `#` are comments.
  A comment immediately before a path acts as a **label** for that file.
- Blank lines are ignored and reset the current label.
- Both absolute paths and paths relative to the engine root are accepted.

### Template

Create a global template file at:

```
.codex_library/REFERENCES_TEMPLATE.md
```

with full instructions and examples. This file is created automatically when the
library is bootstrapped (i.e. when the first mod is registered).

Every new mod should also receive a pre-populated stub at:

```
.codex_library/mods/<mod_id>/inbox/references.md
```

The stub is empty (references section commented out) but includes a pointer to the
template so users know what format to use.

---

## 2. Extended supported formats

When processing files listed in `references.md`, accept these additional formats
beyond the existing inbox formats (`.md`, `.txt`, `.html`, `.pdf`):

```
.sql  .xml  .json  .yaml  .yml  .py  .csv
```

These formats are treated as plain text — extract and process their content directly.

They are only valid as **referenced** files; inbox drop detection continues to require
the original supported extensions.

---

## 3. Change detection

### Inbox files (physically present in inbox/)

- Track both **content hash** and **modification time** (mtime) for each file.
- A file is considered changed if either its hash or its mtime has changed since
  last processing.
- Store both fields in `manifests/state.json` under `processed_docs`.

### Referenced files (listed in references.md)

- Track **modification time only** (hash would require reading the full file every run,
  which is expensive for large external files).
- A referenced file is considered changed if its mtime has changed since last processing.
- Store mtime in `manifests/state.json` under a new `referenced_files` section.

---

## 4. Artifact invalidation

When a file is detected as changed (inbox or referenced), before reprocessing:

1. Delete all notes that were previously generated from that specific file.
2. Delete all summaries that were previously generated from that specific file.
3. Delete all index contributions from that file (or regenerate the index cleanly).

**Do not delete artifacts from other files in the same mod.**

The previous processing record for the changed file must include enough metadata
to identify which artifacts belong to it. Store `note_files`, `summary_files`, and
`index_files` as lists in the state entry for each file.

---

## 5. State schema additions

Extend `manifests/state.json` to support the new tracking fields:

```json
{
  "last_processed": "...",
  "processed_docs": {
    "<inbox_filename>": {
      "status": "processed",
      "hash": "<md5>",
      "mtime": 1742123456.789,
      "processed_at": "...",
      "source_name": "<safe_filename_stem>",
      "note_files": ["notes/..."],
      "summary_files": ["summaries/..."],
      "index_files": ["indices/..."]
    }
  },
  "referenced_files": {
    "<absolute_path>": {
      "status": "processed",
      "mtime": 1742123456.789,
      "label": "<comment from references.md>",
      "processed_at": "...",
      "source_name": "<safe_filename_stem>",
      "note_files": ["notes/..."],
      "summary_files": ["summaries/..."],
      "index_files": ["indices/..."]
    }
  }
}
```

---

## 6. `process` command behaviour changes

### Before processing a mod

1. Read `inbox/references.md` if it exists. Extract the list of referenced paths.
2. For each inbox file (excluding `references.md` itself):
   - Compare current hash and mtime to stored state.
   - If changed or new: add to pending list, delete old artifacts.
3. For each referenced file:
   - Check the file exists. If not: warn and skip.
   - Compare current mtime to stored state.
   - If changed or new: add to pending list, delete old artifacts.
4. Report pending count before processing.

### During processing

Process inbox files and referenced files through the same pipeline stages:
extract → normalize → split → topics → notes → summaries → indices → manifests.

For referenced files, derive the `source_name` from the file stem (sanitised). The
user-provided label is stored in state but does not affect artifact naming.

### After processing

Update `manifests/state.json` with new hash/mtime and artifact lists.
Update `mod.json` with `last_processed`, `inbox_count`, and a new `referenced_count` field.

---

## 7. Mod runtime changes (Iteration 11 extension)

### `learn` command

After creating a new mod and its subdirectories:

1. Create `inbox/references.md` stub with mod-specific header and instructions comment.
2. Ensure `REFERENCES_TEMPLATE.md` exists in the library root. Create it if missing.

The stub must contain:
- A `# Knowledge References — <mod_name>` heading
- A comment explaining the format
- A pointer to `REFERENCES_TEMPLATE.md`
- An empty/commented example block

### `list` and `status` commands

No required changes, but `status` may optionally report the number of tracked
referenced files and their staleness.

---

## 8. Migration guide for existing mods

Mods created in Iteration 11 continue to work without change.

On the first `process` run after Iteration 14 is installed:

1. The state file gains `mtime` fields for each already-processed doc (inferred from
   the stored hash or set to 0 to force one-time reprocessing).
2. The `referenced_files` section is added (empty initially).
3. `references.md` stubs are **not** created retroactively for existing mods — the user
   must create them manually or by running `learn <mod>` on an already-existing mod
   (the command creates the stub only if the file is absent).

Providing migration instructions in the impl log is sufficient. No
destructive operations on existing artifacts should be performed during migration
unless a file is explicitly changed.

---

## 9. Design rules

- `references.md` must be excluded from the processing pipeline's document list
  (it is a manifest, not a knowledge document).
- Invalidation must be per-file, not per-mod: changing one referenced file must not
  delete artifacts from other files in the same mod.
- Mtime comparison should use a small tolerance (< 1 second) to avoid spurious
  invalidation on filesystems with coarse timestamps.
- If a referenced file no longer exists (removed from disk), warn the user but do not
  delete its existing artifacts automatically. The user should explicitly `clean` the mod.
- Remain idempotent: running `process` twice on unchanged input produces the same
  state without regenerating artifacts.

---

## 10. Documentation updates

Create or update:

1. `codex/iterations/14/readme.md` — purpose and migration summary
2. `codex/iterations/14/codex_context_referenced_files.md` — this file (implementation prompt)
3. `codex/iterations/14/root_readme_update.md` — README section to add

Update existing documentation references where they describe the inbox as the only
way to add source documents, replacing or extending them to mention `references.md`.

---

## 11. Detection signals (for `detect_installed_iteration`)

An engine has Iteration 14 installed if any of the following are present:

- `.codex_library/REFERENCES_TEMPLATE.md` exists
- any `inbox/references.md` file exists inside a mod directory
- `manifests/state.json` of any mod contains a `referenced_files` key
- processing logic parses `references.md` and tracks `mtime` in state

---

## 12. Acceptance criteria

Implementation is correct if all of the following are true:

- `REFERENCES_TEMPLATE.md` is created in `.codex_library/` root on first install
- new mods receive an `inbox/references.md` stub automatically
- paths in `references.md` are resolved and ingested by the pipeline
- inbox files are tracked by hash + mtime; referenced files are tracked by mtime
- changing a referenced file causes its artifacts to be deleted and regenerated
- changing an inbox file causes its artifacts to be deleted and regenerated
- unchanged files are skipped (idempotent)
- `state.json` stores `referenced_files` section alongside `processed_docs`
- referenced files in `.sql`, `.xml`, `.json`, `.yaml`, `.py`, `.csv` formats are ingested as plain text
- existing mods from Iteration 11/12/13 continue to work without breaking
