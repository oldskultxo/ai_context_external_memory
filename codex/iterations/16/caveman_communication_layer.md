# Iteration 16 — Communication Compression Layer (Execution-Ready Prompt)

You are implementing **Iteration 16 — Communication Compression Layer** for `codex_context_engine`.

Your job is to add a **Caveman-style communication policy** to the engine so Codex suppresses intermediate runtime chatter and returns a plain, compressed final result by default, while preserving full technical accuracy.

This iteration must improve **how the engine speaks**, not how it reasons.

---

## Core intent

The engine already optimizes context selection and retrieval.

This iteration adds a second optimization layer:

1. remove wasteful communication
2. suppress intermediate execution updates by default
3. reduce output-token spend
4. make the final result plain, direct, and undecorated
5. keep code, tests, and artifacts high quality

The target outcome is:

> same work  
> fewer words  
> same correctness  
> one final answer

---

## Critical mandatory pre-step

Before adding the new communication layer, inspect the target repository and remove any **engine-managed communication-style instructions** that conflict with Caveman mode.

This cleanup is mandatory.

### Remove or normalize engine-managed guidance related to:
- verbosity
- response style
- summary style
- progress update style
- narration style
- assistant tone for execution updates

### Do not remove:
- project-specific product tone guidance
- documentation style guides for repository files
- narrative or literary voice rules
- marketing copy instructions
- domain-specific writing requirements unrelated to the engine's own runtime communication

If an existing `AGENTS.md` contains a managed engine block, update that block in place.
Do not destroy unrelated project instructions.

---

## Non-goals

Do **not**:
- degrade reasoning quality
- degrade source code quality
- degrade tests
- turn repository documentation into caveman speech
- rewrite README prose into caveman style unless explicitly required
- remove useful technical details
- become cryptic for the sake of brevity
- hide uncertainty when uncertainty matters

This iteration is about **communication compression**, not anti-clarity.

---

## Implementation scope

Implement the following capabilities.

### 1. Install a default communication mode

The engine must default to a compressed communication policy for execution-time messages.

Default installed mode:
- `caveman_full`

Supported conceptual modes:
- `caveman_lite`
- `caveman_full`
- `caveman_ultra`

You do not need to build an elaborate UI for mode switching unless the current engine architecture already has a natural place for it.

At minimum, the installed policy must clearly define that **Full** is the default mode.
It must also clearly define that `caveman_full` means:
- no intermediate outputs while processing
- one final output by default
- no decorative formatting in the final result
- concise, direct phrasing

---

### 2. Define the communication contract

For engine-runtime communication, the installed rules should enforce:

- no intermediate progress output by default while work is in progress
- final output only unless the user explicitly asks for step-by-step updates
- no filler
- no greeting unless context requires it
- no repeated restatement of the task
- no long motivational or conversational framing
- no verbose "what I am about to do" narration
- no bloated wrap-up paragraphs
- no decorative formatting, ornamental headers, or presentation fluff
- compact wording
- explicit technical nouns
- preserve file paths, commands, errors, risks, and decisions

Preferred response patterns:

- `found -> cause -> fix`
- `done -> files -> tests`
- `blocked -> reason -> need`
- `next -> verify -> continue`
- `changed A, updated B, left C`

The policy may use:
- arrows
- terse bullets
- compact labels
- short sentences

Do not force unreadable broken language if it harms clarity.
Do not require headers, markdown framing, or visual decoration in the final result.

---

### 3. Limit where the policy applies

Apply Caveman mode to:
- implementation summaries
- debugging reports
- patch explanations
- execution-loop diagnostics
- final execution results

Default application rule:
- suppress intermediate status reporting while the agent is still working
- return one final answer when the task is complete
- keep the final answer plain and direct unless the user asks for a different format

Do not automatically apply Caveman mode to:
- committed source code comments
- generated documentation files
- marketing copy
- narrative content
- prose requested in normal style by the user

If the user explicitly requests a normal long-form explanation, obey the user.
If the user explicitly requests intermediate updates, obey the user.

---

### 4. Add an engine-visible state marker

Ensure the target repository retains a machine-readable signal that Iteration 16 is installed and communication compression is active.

Use an existing engine metadata file if one already exists.
Otherwise add a lightweight compatible marker.

Recommended minimum fields:

```json
{
  "engine": "codex_context_engine",
  "installed_iteration": 16,
  "communication_mode": "caveman_full",
  "communication_layer": "enabled"
}
```

---

### 5. Preserve upgrade safety

When upgrading an existing installation:

- preserve memory
- preserve telemetry
- preserve `.codex_library/`
- preserve references and remote sources
- preserve project instructions unrelated to engine communication
- prefer in-place updates
- remove only conflicting engine-managed communication notes

Do not perform destructive cleanup outside the engine communication layer.

---

## Suggested AGENTS.md behavior

If `AGENTS.md` exists, merge a communication block carefully.

The managed engine block should make these rules explicit:

- no intermediate execution updates by default
- final output must be concise
- final output must be plain and direct
- avoid filler
- prefer compact structure
- preserve precision
- avoid decorative formatting
- do not compress repository prose unless asked
- user request overrides default mode when needed

If no `AGENTS.md` exists and the engine conventions require one, create or update it accordingly.

---

## Suggested installed behavior examples

Good:

- `done -> updated packet builder, added state marker, adjusted AGENTS block`
- `found auth bug -> cause expiry check wrong -> fix patched`
- `done -> files updated -> tests passed`
- `blocked -> no iteration marker found -> inferring from installed capabilities`
- `done -> prompt updated -> policy explicit -> no tests needed`

Bad:

- `Absolutely! I'd be happy to help with that. First, I'm going to inspect the repository and then I will carefully update the necessary files for you.`
- `I have made several improvements that should hopefully make the engine more concise while still maintaining clarity and readability.`
- `**Progress**` followed by styled status notes during processing

---

## Acceptance criteria

Iteration 16 is complete only if all of the following are true:

1. previous engine-managed communication-style notes are removed or normalized
2. the engine has a clear default compressed communication policy
3. the policy applies to execution-time communication, not all repository prose
4. the installation leaves behind a reliable Iteration 16 marker
5. existing engine capabilities remain intact
6. `AGENTS.md` integration is careful and non-destructive
7. the resulting communication style is shorter, denser, and still technically clear
8. intermediate runtime outputs are suppressed by default
9. final results are plain, direct, and non-decorative
10. `caveman_full` explicitly means no intermediate output and final-only response by default

---

## Final instruction

Implement Iteration 16 so the engine becomes:

- context-aware
- cost-aware
- communication-efficient

**Reason full. Speak lean.**
