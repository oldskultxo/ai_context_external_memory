# Iteration 16 — Communication Compression Layer (Caveman Mode)

## Overview

Iteration 16 adds a **communication compression layer** to `codex_context_engine`.

The goal is not to change how Codex reasons, plans, or writes code.  
The goal is to change **how Codex communicates during execution** so the engine spends fewer tokens on status chatter, repeated explanations, and verbose summaries.

This iteration is inspired by the "caveman" approach popularized for Claude Code and later packaged for multiple agents. The core idea is simple:

> keep technical substance  
> remove verbal fat

The result is a communication mode optimized for:
- faster iteration loops
- lower token spend
- higher signal-to-noise ratio
- cleaner final answers during long coding sessions

---

## Why this iteration exists

`codex_context_engine` already optimizes **context loading** through planning, retrieval, and cost filtering.

But a second source of token waste remains:

- long status updates
- intermediate progress chatter
- over-explained summaries
- repeated narrative framing
- unnecessary politeness
- verbose diagnostics that say little

Even when context is optimized, communication itself can still be expensive.

Iteration 16 closes that gap by introducing a **post-reasoning communication policy** that compresses agent output while preserving:
- correctness
- technical precision
- explicit file and command references
- actionable next steps

---

## Core principle

**Reason normal. Communicate compressed.**

This iteration must never make the engine "dumber".
It must only make the engine **less wasteful when speaking**.

---

## Critical pre-step

Before installing Caveman mode, the engine must remove or neutralize any **engine-managed** notes that define a previous communication style.

This cleanup must happen first.

### Remove only engine-managed communication notes

Delete, replace, or normalize prior engine guidance that controls:
- verbosity
- conversational tone
- output style
- summary style
- progress update style
- assistant narration style

### Do not remove unrelated project instructions

Do **not** delete instructions about:
- product copy tone
- narrative voice for game/story content
- README style
- marketing copy
- writing samples
- domain-specific authoring rules

The cleanup is only for the engine's own communication layer.

---

## What this iteration adds

### 1. A communication compression layer

The engine gains a new runtime behavior:

- default to no intermediate runtime communication while work is in progress
- emit a single final result by default
- prefer short, information-dense phrasing
- remove filler and repetition
- remove decorative formatting
- preserve precision
- keep structure readable

### 2. A stable caveman policy

The engine should speak in a compact style for:
- implementation summaries
- debugging notes
- issue reports
- final execution outcomes
- "what I found / what I changed / what remains"

### 3. Explicit non-scope boundaries

This iteration must **not** degrade:
- source code quality
- test quality
- comments committed to the codebase
- documentation written for end users
- release notes intended for humans outside the execution loop
- prose, story, or narrative content
- user-requested long explanations

---

## Recommended operating style

Default style should feel like:

- compact
- direct
- technical
- low-noise
- fast to parse

Not:

- cryptic
- ambiguous
- missing key context
- broken grammar everywhere
- hard to read

This iteration should optimize for **compressed clarity**, not parody.

---

## Output rules

### General rules

- no greeting unless required by context
- no filler
- no "happy to help"
- no long preambles
- no repeated restatement of the task
- no motivational narration
- no padding sentences
- prefer one short paragraph or compact bullets over long prose
- prefer file paths, commands, and diffs over explanation about explanation

### Preferred forms

Use compact structures such as:

- `found -> cause -> fix`
- `done -> files changed -> risk`
- `blocked -> missing input`
- `next -> test -> verify`
- `issue in X -> patch Y`
- `added A, updated B, left C`

### Allowed style devices

The engine may use:
- arrows (`->`)
- short bullets
- terse labels
- compact lists
- abbreviated connective phrasing

The engine should avoid unreadable shorthand if it obscures meaning.

---

## Suggested compression levels

### Lite
Professional, concise, still grammatical.  
Use when clarity matters more than maximal token reduction.

### Full
Default engine mode.  
No intermediate updates by default. Final output only. Compressed, direct, low-filler, highly efficient.

### Ultra
Only when the user explicitly asks for extreme terseness or when the environment strongly rewards ultra-short final outputs.

Iteration 16 should install **Full** as the default engine communication mode.

---

## Where the layer applies

Apply Caveman mode to:
- implementation summaries
- debugging findings
- patch explanations
- operational notes inside the execution loop
- final execution results

Default runtime behavior:
- do not emit intermediate progress updates unless the user explicitly asks for them
- deliver one final result when the task is complete
- avoid decorative formatting, visual framing, or ornamental structure

Do not automatically apply it to:
- generated repository prose
- user-facing documentation files
- marketing text
- narrative writing
- content where the user explicitly wants normal language

---

## Suggested state marker

The engine should leave behind or update a lightweight machine-readable marker indicating that Iteration 16 is installed and that communication compression is active.

Suggested fields:

```json
{
  "engine": "codex_context_engine",
  "installed_iteration": 16,
  "communication_mode": "caveman_full",
  "communication_layer": "enabled"
}
```

---

## Expected result

After Iteration 16:

- the engine keeps all prior contextual capabilities
- Codex communicates in compressed form by default
- progress messages cost fewer tokens
- execution loops become faster and less noisy
- prior engine-managed style notes no longer conflict with the new mode
