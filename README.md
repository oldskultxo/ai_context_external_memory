# Codex Context External Memory

This repository contains prompt specifications for building, evolving, and validating an external-memory workflow for Codex.

The latest iteration (`codex/iterations/three`) defines a more advanced system that combines:

- **Codex Ultra Memory System (CUMS)** for persistent, low-token memory
- **Deterministic Context Packets** for stable, inspectable task-scoped context injection
- **Context Relevance Scoring** for selective retrieval of high-value memory records
- **Memory Compaction Engine** for deduplication, pruning, and long-term memory health
- **Telemetry tracking** for estimated context/token/latency/cost savings
- **Validation workflow** for functional and performance verification

---

## What This Project Is For

The project is used to make Codex more efficient across sessions by:

- reducing repeated context loading
- reusing persistent user preferences and validated project learnings
- retrieving only the smallest relevant memory slice per task
- preferring deterministic context packets over ad hoc memory injection
- falling back to normal repo inspection when memory is missing, stale, or contradicted
- tracking estimated optimization impact over time
- keeping memory compact and maintainable as the system evolves

---

## Repository Layout

- `codex/iterations/one/`: first generation prompt (`codex_context` model)
- `codex/iterations/two/`: second generation prompts (stable baseline)
  - `1_codex_ultra_memory_system_prompt.md`
  - `2_codex_context_telemetry_prompt.md`
  - `3_codex_ultra_memory_system_validation_checklist.md`
- `codex/iterations/three/`: current advanced iteration
  - `1_codex_iteration_three_prompt.md`
  - optional telemetry / validation additions if you choose to extend iteration three in the target repo

---

## Evolution by Iteration

### Iteration One
The first iteration established the basic idea:
- external memory outside the live prompt
- persistent project learnings
- preference reuse across sessions
- fallback to normal Codex behavior

This version proved the concept, but context injection and memory retrieval were still relatively loose.

### Iteration Two
The second iteration introduced a more complete workflow:
- persistent ultra-memory
- delta context retrieval
- telemetry for estimated savings
- formal validation checklist

This became the recommended baseline because it made the system measurable and more structured.

### Iteration Three
The third iteration upgrades the system in three key ways:

#### 1. Deterministic Context Packets
Instead of injecting memory in a loosely assembled way, Codex builds a stable task packet with a predictable schema, typically including:

- task summary
- task type
- repo scope
- user preferences
- constraints
- architecture rules
- relevant memory
- known patterns
- fallback mode

This makes context injection more predictable, easier to inspect, and easier to keep small.

#### 2. Context Relevance Scoring
Memory retrieval is upgraded from loose matching to scored selection.

Records can include metadata such as:
- relevance score
- last used time
- usage count
- success rate
- context cost
- staleness

This allows Codex to prefer a few high-value memory records instead of broad retrieval.

#### 3. Memory Compaction Engine
As memory grows, the system needs maintenance.

Iteration three introduces compaction behaviors such as:
- duplicate detection
- near-duplicate merging
- stale low-value pruning
- boot summary rebuilding
- compaction reporting

This keeps the memory layer healthy over time and reduces drift.

---

## How It Works (Latest Iteration)

After you execute the **iteration three prompt** in Codex, the expected flow is:

1. **System detection**
   - Codex inspects the target repository.
   - If an existing context system is found, it upgrades it in place.
   - If no system exists, it installs the full structure from scratch.

2. **Memory system bootstrap**
   - Codex creates or upgrades a persistent memory workspace (for example `.codex_memory/`).
   - Small boot artifacts are generated for fast startup (`derived_boot_summary`, user preferences, packet schema, memory index).

3. **Session auto-init**
   - On each new session, Codex loads only tiny boot files first.
   - User preferences and runtime defaults are applied automatically.
   - `AGENTS.md` remains the authoritative runtime policy layer.

4. **Task execution with deterministic packets**
   - Codex classifies the task.
   - It selects the likely repo scope.
   - It retrieves candidate memory records.
   - It scores them by relevance and context cost.
   - It builds a minimal deterministic context packet.
   - It uses this packet before broad repository scanning.

5. **Safe fallback**
   - If memory is stale, missing, or contradicted by code/tests/runtime, Codex falls back to standard reasoning and direct repo inspection.

6. **Learning updates**
   - After meaningful validated outcomes, Codex writes compact structured records (not transcripts).
   - Older records are normalized when needed.

7. **Memory compaction**
   - Compaction utilities can detect duplicates, merge compatible entries, prune low-value memory, and refresh compact summaries.

8. **Telemetry compatibility**
   - If telemetry exists, iteration three keeps it compatible.
   - Packet-size and scoring-aware metrics can be added without redesigning the whole system.

9. **Validation**
   - Existing validation checklists can be preserved or minimally extended.
   - The system should verify auto-boot, preference persistence, deterministic packet generation, relevance scoring quality, compaction safety, fallback robustness, and upgrade/install compatibility.

---

## Recommended Prompt Execution Order

### If you want the stable baseline
1. Run `codex/iterations/two/1_codex_ultra_memory_system_prompt.md`
2. Run `codex/iterations/two/2_codex_context_telemetry_prompt.md`
3. Run `codex/iterations/two/3_codex_ultra_memory_system_validation_checklist.md`

This installs the second-generation system, adds measurement, then validates behavior.

### If you want the latest advanced iteration
1. Run `codex/iterations/three/1_codex_iteration_three_prompt.md`

This prompt is designed to:
- complement and upgrade an existing installation if found
- install the system from scratch if no prior installation exists

Optional:
- keep or extend telemetry from iteration two if your target repo already uses it
- keep or extend validation material if your target repo already includes it

---

## Quick Start

### Stable baseline (iteration two)

From the project root, open each prompt and execute it in Codex in this order:

```bash
cat codex/iterations/two/1_codex_ultra_memory_system_prompt.md
cat codex/iterations/two/2_codex_context_telemetry_prompt.md
cat codex/iterations/two/3_codex_ultra_memory_system_validation_checklist.md
```

### Latest advanced iteration (iteration three)

From the project root:

```bash
cat codex/iterations/three/1_codex_iteration_three_prompt.md
```

If needed, copy the file content into Codex and execute it directly.

---

## Recommended When to Use Each Iteration

### Use iteration one if:
- you only want the original proof of concept
- you want to inspect the simplest version first

### Use iteration two if:
- you want the most established baseline
- you want telemetry and validation included from the start
- you want the clearest measured workflow

### Use iteration three if:
- you already have a system and want to improve it safely
- you want deterministic context injection
- you want more selective retrieval
- you want long-term memory maintenance via compaction
- you want a system that can both upgrade in place and install from scratch

---

## Estimated Savings

These are **estimated** ranges from practical usage in the iepub project (not exact telemetry export numbers).

### Iteration that produced the original baseline estimate
`iterations/two` (Ultra Memory + Telemetry workflow)

| Metric | Estimated range | Visual |
|---|---:|---|
| Context reduction | 35-55% | `████████░░` |
| Total token reduction | 25-45% | `███████░░░` |
| Latency improvement | 15-30% | `█████░░░░░` |
| Cost reduction | 20-40% | `██████░░░░` |

Confidence: **medium** (experience-based estimate; exact values should come from `.context_metrics/` checkpoints in the target repo).

### Expected effect of iteration three
Iteration three is designed to improve the *quality* and *stability* of retrieval more than to change the philosophy of the system.

Expected benefits compared with iteration two:
- lower retrieval noise
- better consistency between sessions
- better long-term memory hygiene
- less context drift over time
- more explainable memory selection

Exact gains should be validated in the target repository after deployment.

---

## Current State of This Repository

This repository stores the prompt definitions, architecture direction, and validation criteria.

It does **not** include the generated memory/telemetry implementation by default; those files are created in the target project when Codex executes the prompts.

---

## Design Principles

Across all iterations, the project follows these principles:

- memory should be compact
- policy should live in `AGENTS.md`
- memory artifacts should support policy, not replace it
- context retrieval should stay task-scoped
- fallback must always preserve normal Codex usability
- telemetry should help measure value without bloating the system
- upgrades should preserve useful learnings whenever possible

---

## Future Direction

Potential future iterations may include:
- adaptive retrieval strategies
- stronger scoring based on observed usefulness
- richer telemetry for packet quality
- self-diagnosis / health reporting
- cross-project memory layers
- context cost optimization before injection
