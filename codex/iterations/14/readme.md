# Iteration 14 — Referenced File Tracking & Inbox Invalidation

## Purpose

Extend the knowledge mod system introduced in Iterations 11–13 to support:

1. **External file references** — a `references.md` manifest inside each mod's inbox that
   lists paths to files outside the inbox (SQL scripts, XML specs, Markdown docs, etc.)
   so they can be ingested without copying them.

2. **Modification-time change detection** — all tracked files (inbox drops and referenced
   externals) are monitored by modification date, so the pipeline can detect when a
   source has changed and invalidate the derived artifacts automatically.

3. **Artifact overwrite on change** — when a tracked file changes, its previously
   generated notes, summaries, and indices are deleted and fully regenerated.

This iteration is a quality-of-life and correctness improvement over Iterations 12 and 13.
No new mod commands are added; existing commands gain smarter invalidation behaviour.

## Problems solved

| Problem | Solved by |
|---|---|
| Source files live outside the project repo and can't be copied to inbox | `references.md` pointer file |
| Pipeline silently skips changed files (hash-only comparison) | Mtime + hash dual signal |
| Stale artifacts persist after a source file is updated | Deletion of old artifacts before reprocessing |
| Referenced files in exotic formats (`.sql`, `.xml`, `.json`) are ignored | Extended supported extensions |

## Affected iterations

| Iteration | Component | Change |
|---|---|---|
| 11 | Mod runtime | On `learn`: create `inbox/references.md` stub; create `.ai_library/REFERENCES_TEMPLATE.md` |
| 12 | Knowledge pipeline | Parse `references.md`; track mtime for all files; delete + regenerate on change |
| 13 | Knowledge retrieval | No functional change; retrieval still operates on `indices/` output from iter 12 |

## Migration from existing mods

Mods created before this iteration continue to work without change.
On the next `process` run the pipeline will:
- add `mtime` fields to existing `state.json` entries
- create `referenced_files` tracking section in `state.json`
- process referenced files if `inbox/references.md` is present

No destructive changes occur unless a file is detected as changed.
