# Obsidian Vault Conventions for Research Skills

All research skills share these conventions for reading from and writing to the user's Obsidian vault.

## Folder Structure

```
<vault-root>/
├── Papers/<project>/                          # Paper triage & extraction outputs (no direction subfolders)
├── Hypotheses/<project>/
│   ├── <direction-a>/                         # Direction subfolder: hypothesis, stress-test, results, checkpoint
│   │   ├── hypothesis.md
│   │   ├── stress-test.md
│   │   ├── result-01.md
│   │   ├── checkpoint.md
│   │   └── merge-queue.md
│   ├── <direction-b>/
│   │   └── ...
│   ├── <project>-direction-rejections.md      # Project-level (no direction — spans all directions)
│   └── <goal-slug>-probe.md                   # Project-level (precedes direction selection)
├── Experiments/<project>/
│   └── <direction>/                           # Mirrors Hypotheses structure
│       └── <experiment-name>.md
├── Agent-Hypotheses/<project>/
│   └── <direction>/
│       └── <agent-hypothesis-name>.md
├── Results/<project>/
│   └── <direction>/                           # Mirrors Hypotheses/Experiments structure
│       └── <result-name>.md
├── Synthesis/<project>/                       # Cross-field synthesis notes (no direction subfolders — synthesis spans)
├── Projects/                                  # Project MOCs (map of content / dashboard notes)
└── _templates/                                # Obsidian templates (optional, for manual note creation)
```

Every artifact lives under `<TypeFolder>/<project>/`. The `<project>` subfolder uses the same
kebab-case slug as the `project:` frontmatter field (e.g., `my-project`, `methodology`).

**Direction subfolders.** Within `Hypotheses/`, `Experiments/`, `Agent-Hypotheses/`, and `Results/`,
a note with a `direction:` frontmatter field lives at `<TypeFolder>/<project>/<direction>/<filename>.md`,
where `<direction>` is the note's `direction:` value verbatim (already kebab-case). A note with no
`direction:` field (papers, project-wide rejection logs, pre-direction probes) stays at the project
level: `<TypeFolder>/<project>/<filename>.md`. `Papers/` and `Synthesis/` never get direction
subfolders — papers and synthesis notes routinely serve more than one direction.

Notes freshly created by the research-loop pipeline inside a direction subfolder use fixed,
generic filenames (`hypothesis.md`, `stress-test.md`, `checkpoint.md`, `merge-queue.md`,
`result-01.md`, `result-02.md`, ...) since the enclosing `<direction>/` path already makes them
unique — do not prefix them with the direction slug. **Because these filenames repeat across every
direction, wikilinks to them must be path-qualified** — write `[[<project>/<direction>/hypothesis]]`,
never the bare `[[hypothesis]]`, which is ambiguous vault-wide. Notes that predate this convention
keep their original (direction-prefixed) filenames — they were not renamed during the migration to
avoid breaking existing wikilinks, which Obsidian resolves by filename.

**Why project (and direction) subfolders:** Agents scope searches to `<TypeFolder>/<project>/` (or
`<TypeFolder>/<project>/<direction>/`) first, reducing noise and context cost. Parallel agents
writing to different projects or directions never conflict. The file tree gives instant
project- and direction-level overview without Dataview queries.

**Cross-project artifacts:** An artifact relevant to multiple projects lives under the project
that produced it. Cross-project visibility comes from `related:` wikilinks and vault-wide
searches in cross-field-synthesis and Kan-transport audits — not from folder placement.

## YAML Frontmatter (required on every note)

```yaml
---
type: paper | hypothesis | experiment | synthesis | agent-hypothesis | result | code-review | probe | checkpoint | project
project: "<project-name>"
status: draft | reviewed | active | validated | refuted | archived | needs-review
domain: ["<tag1>", "<tag2>"]
created: YYYY-MM-DD
sources: ["<URL or citation>"]
related: ["[[Note Title]]"]
regime: "<project>/<n>"                 # schema regime in force at creation (see Regime Log)
direction: "<direction-slug>"           # research direction/thread within the project (scopes Kan audits)

# --- Typed Provenance (required for all skill-generated notes) ---
parents: ["[[parent-note-slug]]"]       # Direct upstream artifacts that produced this note
produced_by: "<skill-name>"             # Which skill/operation created this note
parent_types: ["<type1>", "<type2>"]    # Input artifact types (for schema validation)

# --- Gate Record (required when a verdict was reached) ---
gate_verdict: accepted | rejected | inconclusive   # null if no gate applied
gate_metric: "<what was measured>"                  # e.g., "partial correlation |ρ|"
gate_threshold: "<decision rule>"                   # e.g., "|ρ_partial| > 0.3 AND p < 0.05"
gate_value: "<observed value>"                      # e.g., "ρ_partial = -0.347, p = 0.194"

# --- Gate Design (recommended for experiment and result artifacts) ---
gate_sensitivity: "<what the gate reports on a broken arm, or why untested>"
gate_reference_pipeline: "<deviation from known value through identical pipeline>"
gate_power: "<oracle ceiling, noise floor, ratio>"
gate_null_check: "<how the null/baseline is validated>"
gate_bias_direction: "<toward/away from/sign-changing relative to target>"
gate_min_n: <N>
registration: pre | retro                  # pre = registered before run; retro = registered after exploration

# --- MDA-Derived Fields (recommended for result and experiment artifacts) ---
# Inspired by Murphy 2608.09696 (Model Discovery Agent). See "MDA-Derived Fields" subsection below.
model_complexity: "<DOF / parameter count / structural assumptions>"
bic_value: "<BIC score, if computed>"
m_open_residual: "<observed residual of best model on held-out data>"
m_open_threshold: "<pre-registered τ_r for model-class adequacy>"
m_open_verdict: adequate | expand
voi_discriminant: "<which competing hypotheses this experiment discriminates, or 'N/A'>"
proxy_metric: "<cheap estimator used, if any>"
sufficiency_check: "<spot-check protocol: K full runs, ε threshold, max divergence>"
sharpened_predictions: ["<what this result makes more precise>"]
exposed_residuals: ["<what subtler patterns are now detectable>"]

# --- Code Provenance (required for result and code-review artifacts) ---
code_commit: "<full-or-short SHA>"          # git commit that produced this result
code_repo: "<repo-name>"                    # which repository (matches project-link target)
code_branch: "<branch-name>"                # branch the experiment ran on
run_command: "<shell command>"              # exact command to reproduce (e.g., "python train.py --seed 42")
plots: ["<path-at-commit>"]                 # paths to generated figures, relative to repo root

# --- Reference Verification (for paper artifacts) ---
citations_verified: <N>                     # citations resolved via Semantic Scholar / arXiv API
citations_unverified: <N>                   # citations that could not be resolved
citations_total: <N>                        # total citations extracted

# --- Claim Verification (for result artifacts) ---
claims_verified: true | false               # whether post-hoc claim audit passed

# --- Specification Violation Check (for result artifacts) ---
spec_violation: clean | flagged | warning   # I2 audit: did code game the metric?
spec_violation_detail: "<description>"      # what was detected (empty if clean)
---
```

### Core Fields

- `type` — matches the top-level folder the note lives in (e.g., `paper` for notes in `Papers/<project>/`)
- `project` — groups notes across folders; use the same slug consistently (e.g., `my-project`, `my-extension`)
- `status` — lifecycle stage; `draft` = just created, `reviewed` = human-checked, `active` = in use, `validated`/`refuted` = for hypotheses, `archived` = no longer relevant (including superseded notes — add a `superseded_by:` wikilink), `needs-review` = autonomous agent hit an escalation threshold and stopped; human must review before the pipeline continues
- `regime` — which schema regime was in force when this artifact was created, as `<project>/<n>` (e.g., `my-project/2`). Increment `n` on every regime transition recorded in the project's Regime Log. Needed to verify that old commitments remain gate-accepted after transport (Definition 6, condition 3 in [[wang-buehler-2026-categorical-discovery]]).
- `direction` — kebab-case slug for the research direction/thread this artifact belongs to. Kan-transport audits are scoped per-direction, not per-project; without this field they cannot enumerate "the old direction's artifacts".
- `domain` — list of topic tags (e.g., `["neural-architecture", "modular-networks"]`)
- `created` — ISO date
- `sources` — URLs, DOIs, arXiv IDs, or free-text citations
- `related` — wikilinks to other vault notes for **lateral** connections (related but not parent-child). Skills should search existing notes and link where relevant.

### Typed Provenance Fields

These fields reconstruct the **directed provenance graph** — the category of elements ∫I_t in the categorical framework (see [[wang-buehler-2026-categorical-discovery]]).

- `parents` — wikilinks to the specific notes that were inputs to the operation that created this note. This is a **directed** relationship: parents → child. Distinct from `related`, which is undirected/lateral.
- `produced_by` — the skill or operation that produced this note. Must be one of the declared morphisms in the schema registry below. For manually created notes, use `"manual"`.
- `parent_types` — the `type` values of the parent notes. Used for schema validation: the combination of `parent_types` → `type` via `produced_by` must be a valid morphism in the schema.

**Why this matters:** Without typed provenance, the vault is just a knowledge graph with undirected links. With it, every note records exactly what produced it and from what — making the entire research process auditable, replayable, and composable. Old valid workflows remain valid when the pipeline changes (the "endofunctoriality" requirement).

### Gate Record Fields

Every hypothesis evaluation, experiment conclusion, or model comparison should record its **gate verdict** — including rejections. Rejected artifacts are scientifically valuable: they constrain the search space and prevent redundant work.

- `gate_verdict` — the outcome: `accepted` (passed the decision rule), `rejected` (failed), or `inconclusive` (ambiguous / insufficient power). Omit entirely for notes where no gate was applied (e.g., paper triage, idea generation).
- `gate_metric` — what was measured. Be specific: "partial correlation controlling for task similarity", not "correlation".
- `gate_threshold` — the pre-registered decision rule. State it precisely: "|ρ_partial| > 0.3 AND p < 0.05 on permutation test".
- `gate_value` — what was actually observed: "ρ_partial = -0.347, p = 0.194".

**Critical rule:** Never silently delete a rejected artifact. Set `status: refuted` and keep the note with its gate record. Silent deletion destroys provenance.

### Gate Design Fields

Recommended for `experiment` and `result` type artifacts. These fields record the
results of the gate design checklist (research-loop Stage 5), ensuring that gates
are well-specified before execution rather than just well-recorded after failure.

The checklist addresses four failure modes observed in practice:

1. **Blind gates** — the gate cannot distinguish broken from correct code (passes identically on both).
   → `gate_sensitivity` records whether a broken-arm test was run.
2. **Wrong reference** — deviation from an ideal value mixes physics with solver error.
   → `gate_reference_pipeline` records deviation from the ideal pushed through the *same* pipeline.
3. **Confound contamination** — the comparison has more than one free variable.
   → Confound inventory goes in the experiment note body (a table, not a frontmatter field).
4. **Ungated null** — the control or baseline is itself an artefact.
   → `gate_null_check` records how the null was validated.

Additional fields:

- `gate_power` — oracle ceiling vs noise floor. If noise exceeds the maximum achievable effect, the experiment is underpowered by construction.
- `gate_bias_direction` — whether the estimator's systematic error flatters the result. Agreement with theory is uninformative if the estimator cannot fail in that direction.
- `gate_min_n` — minimum sample size for the statistic to be meaningful. Prevents thin medians from grounding verdicts.
- `registration` — `pre` (default, registered before execution) or `retro` (registered after exploratory work became load-bearing but before the decisive run). Retro-registration is disclosure, not a penalty.

These fields are recommended, not required — not every experiment needs all of them. But any
experiment where a failure would be expensive or a false positive would be dangerous should
fill in at least `gate_sensitivity`, `gate_power`, and `gate_bias_direction`.

### MDA-Derived Fields

Inspired by Murphy 2608.09696 (Model Discovery Agent). These fields operationalize the
quantitative evaluation machinery from MDA within the research loop's evaluative stages
(Stages 4–6). They are recommended for `result` and `experiment` artifacts.

| Field | MDA Concept | What it captures |
|-------|-------------|-----------------|
| `model_complexity` | Automatic Occam penalty (marginal likelihood) | DOF / parameter count, used for parsimony comparison between competing hypotheses |
| `bic_value` | Bayesian model comparison | BIC = k·ln(n) − 2·ln(L̂); lower is better. ΔBIC > 10 = very strong evidence |
| `m_open_residual` | M-open predictive check | Residual of best model on held-out data — detects structural model-class inadequacy |
| `m_open_threshold` | M-open threshold τ_r | Pre-registered residual threshold; exceeded → model class is wrong, expand hypothesis space |
| `m_open_verdict` | M-open decision | `adequate` (refine within class) or `expand` (loop back to direction generation) |
| `voi_discriminant` | Value-of-Information criterion | Which competing hypotheses this experiment discriminates; zero VoI if they all predict the same outcome |
| `proxy_metric` | SBI with learned summaries | Cheap estimator used in place of expensive full computation |
| `sufficiency_check` | Sufficiency spot-check | Protocol validating the proxy against K gold-standard runs |
| `sharpened_predictions` | Discovery-design reinforcement | What this validated result makes more precisely predictable |
| `exposed_residuals` | Residual-conditioned proposal | What subtler patterns are now detectable after this result raised resolution |

**Usage principle:** These fields belong at evaluative stages (4–6), not generative stages
(1–2). Direction generation and hypothesis stress-testing remain qualitative and structured;
quantitative evaluation is grounded in actual measurements, not LLM-generated probability
estimates. See research-loop SKILL.md for the full integration.

### Code Provenance Fields

Required for `result` and `code-review` type artifacts — any note whose content was produced by running code.

- `code_commit` — the git commit SHA (short or full) at which the experiment ran. This is the stable artifact ID for the code state. To reproduce: `git checkout <code_commit>`.
- `code_repo` — repository name (e.g., `my-project`). Needed because the the vault spans multiple projects/repos.
- `code_branch` — the branch the experiment ran on (useful for worktree-based parallel experiments).
- `run_command` — the exact shell command to reproduce the result. Include all flags, seeds, and config paths. Anyone should be able to `git checkout <commit> && <run_command>` and get the same output.
- `plots` — list of paths to generated figures, relative to the repo root at that commit. The plotting code lives in the repo; the vault note explains what the plot shows.

**Why separate from the vault note:** Code belongs in git (versioned, diffable, runnable). The vault note is a typed research artifact that records *what the code produced and what it means*. Embedding code in markdown creates unversioned, unfindable, unrunnable fragments.

### Reference Verification Fields

Required for `paper` type artifacts when citations are extracted. Inspired by CoE Integrity Audit I3
(Meng et al. 2026, ScientistOne) — catches hallucinated references before they propagate through
the provenance graph.

- `citations_verified` — count of citations resolved via Semantic Scholar / arXiv API.
- `citations_unverified` — count of citations that could not be resolved. Each should be flagged inline in the note body.
- `citations_total` — total citations extracted from the paper.

### Claim Verification Fields

Required for `result` type artifacts before promotion from `status: draft`. Inspired by CoE Integrity
Audit I1 (score verification) and I4 (method-code alignment).

- `claims_verified` — `true` if all numerical claims match their source data and method descriptions match the code at `code_commit`. `false` if any mismatch was found (mismatches should be flagged inline with ⚠). A result note with `claims_verified: false` must have `status: needs-review`.

### Specification Violation Fields

Required for `result` type artifacts. Inspired by CoE Integrity Audit I2 (Meng et al. 2026) — detects
code that earns its metric by gaming the evaluation rather than solving the problem. Spec violation
risk scales with iteration count; this check runs between execution and gate evaluation in the
research-loop (Stage 6a.5).

- `spec_violation` — `clean` if no issues detected. `flagged` if the code manipulates the evaluator or circumvents the hypothesis specification (result is rejected without evaluating the gate). `warning` if scope creep was detected but no hard violation (proceeds to gate evaluation with a flag).
- `spec_violation_detail` — what was detected. Empty string or `"none"` if clean. For flagged results, describes the violation (e.g., "Evaluator manipulation: diff modifies evaluate.py", "Specification circumvention: LLM majority vote 3/3").

**Critical:** A result with `spec_violation: flagged` must have `gate_verdict: rejected` and `gate_metric: "I2 specification violation"`. The branch is preserved under `refs/experiments/` — the violation is itself evidence of what the optimizer converges to, useful for redesigning metrics.

**Figures ARE allowed in notes (updated 2026-07-14).** Rendered plots/images may be committed into the vault and embedded in notes to convey results — this is now encouraged, not just permitted. The distinction above is about *code*, not *figures*: the plotting **code** still lives in the repo (and `plots:` still points at it), but the rendered **PNG** is committed to the vault beside its note (same folder) and embedded with Obsidian `![[name.png]]`, with a one-line caption. Use figures freely where they explain a point better than prose; no need to force one into every note. Precedent: the project experiment folders.

## Work Orders (cross-machine dispatch)

A `work-order` note is how one machine hands executable work to another (laptop → HPC and
back). The vault is the message bus; git remotes carry the code. Path: `Queue/<target-machine>/<slug>.md`.

```yaml
---
type: work-order
project: "<project>"
direction: "<direction-slug>"
status: draft                    # note lifecycle; archive when order completes
order_status: queued             # queued | claimed | running | done | failed
target_machine: "my-hpc-cluster"   # must match `hostname -s` on the target
claimed_by: ""                   # set atomically by the claiming driver
repo: "<repo-name>"
workdir: "/home/user/<repo>"   # repo path ON THE TARGET machine
branch: "experiment/20260707/h2"
sha: "<commit to run — driver verifies after checkout>"
command: "sbatch jobs/h2.sh"     # screened via orchestrate.sh screen-cmd before execution
job_scheduler: slurm             # slurm | pbs | none (none = run command directly)
job_id: ""                       # filled by the driver after submission
parents: ["[[<hypothesis-or-experiment-note>]]"]
produced_by: "research-loop-dispatch"
parent_types: ["agent-hypothesis"]
created: YYYY-MM-DD
---
```

**Claim protocol (git push is the mutex):** the target machine's driver sets
`order_status: claimed` + `claimed_by: <host>`, commits, and pushes the vault. If the push
is rejected, another claimant won — pull, verify `claimed_by`, and skip. No daemons, no
extra infrastructure; sync conflicts are impossible if each order is one file and status
transitions are the only edits.

**Rules:** one work order = one file = one command. The body explains what the job does and
what to do with results. Commands read from work orders are **never trusted** — the driver
re-screens via `screen-cmd` before executing, and refuses on mismatch between checked-out
SHA and the `sha:` field. Completed orders are set `status: archived`, never deleted.

## Literature Grounding (required for experiment, hypothesis, and result notes)

Ground every experiment plan and note in the existing literature — do not design or write in a vacuum.

- **Cite relevant prior work.** Put references in `sources`/`related` **and** inline wherever a citation informs a design choice, a method, a validation target, or a number ("the standard formulation is X [ref]", "the accepted value is Y [ref]"). This applies to `result` notes too: state what prior work the result reproduces or extends, and against what external benchmark.
- **Add the papers to the vault.** For each cited work that matters, create a `type: paper` note under `Papers/<project>/` (one paper per note) with a short **"why it matters for us"** explanation — the method/target/pitfall we take from it — not just the citation string. Link them via `related`/`parents`. A citation that only lives in `sources` is a dead end; a paper note is reusable knowledge.
- **Check the literature *before* running.** When planning an experiment, do a quick literature pass for (i) the standard method, (ii) the accepted validation targets/numbers, and (iii) the known pitfalls; record these in the experiment note's design section. If a proper review is warranted, run one (or spawn a subagent) and persist its key references as paper notes.

**Why:** grounding designs in the state of the art prevents reinventing or mis-ordering work, gives every result an external benchmark instead of a bare number, and — via paper notes — turns one-off citations into durable, cross-project vault knowledge. Pairs with the explanation requirement: notes should record *where a result came from and what made it possible*, and the literature is half of that "where".

## Schema Registry

The schema category S defines what artifact types exist and what operations (morphisms) connect them. This is the "particle content" of the research system — it declares what transformations are allowed.

### Artifact Types (Objects of S)

| Type | Folder | Description |
|------|--------|-------------|
| `paper` | Papers/\<project\>/ | Extracted/triaged academic papers |
| `hypothesis` | Hypotheses/\<project\>/ | Testable claims with H₀/H₁ and decision rules |
| `experiment` | Experiments/\<project\>/ | Experiment designs with controls, metrics, ablations |
| `synthesis` | Synthesis/\<project\>/ | Cross-field connections and analogies |
| `agent-hypothesis` | Agent-Hypotheses/\<project\>/ | Agent-executable hypothesis test plans |
| `result` | Hypotheses/\<project\>/ or Experiments/\<project\>/ | Outcome of running an experiment (status: validated/refuted) |
| `code-review` | Experiments/\<project\>/ | PR review verdict from thermonuclear-pr-review |
| `probe` | Hypotheses/\<project\>/ | Constraint set + assumption ledger from /autoresearch:probe |
| `checkpoint` | Projects/ or Hypotheses/\<project\>/ | Pipeline resume point (stage, branch, worktree, pending work) |
| `project` | Projects/ | Project MOC / dashboard note (one per project, `produced_by: manual`) |
| `work-order` | Queue/\<machine\>/ | Cross-machine job dispatch: one machine queues work, the target machine claims and runs it |

### Allowed Operations (Morphisms of S)

| Morphism (produced_by) | Input Types (parent_types) | Output Type | Skill |
|------------------------|---------------------------|-------------|-------|
| `paper-triage` | (external source) | `paper` | paper-triage |
| `bibliography-import` | (external source, batch) | `paper` | bulk bibliography import |
| `idea-bouncer` | `paper`, `hypothesis`, `synthesis` (any combo) | `hypothesis` | idea-bouncer |
| `hypothesis-stress-test` | `hypothesis` | `hypothesis` (with gate record) | hypothesis-stress-test |
| `experiment-design-review` | `hypothesis` | `experiment` | experiment-design-review |
| `cross-field-synthesis` | `paper`, `hypothesis`, `experiment` (any combo) | `synthesis` | cross-field-synthesis |
| `agent-hypothesis-gen` | `hypothesis`, `experiment` (either or both) | `agent-hypothesis` | agent-hypothesis-gen |
| `autoresearch-execution` | `agent-hypothesis`, `experiment`, `work-order` | `result` | autoresearch |
| `autoresearch-probe` | (any or none) | `probe` | autoresearch probe |
| `clean-commit-gate` | `result` | `result` (with commits) | clean-commit-gate |
| `thermonuclear-pr-review` | `result` | `code-review` | thermonuclear-pr-review |
| `reference-verification` | `paper` | `paper` (with citation counts) | paper-triage (Step 4) |
| `spec-violation-check` | `result` | `result` (with spec_violation) | research-loop (Stage 6a.5) |
| `claim-verification` | `result` | `result` (with claims_verified) | research-loop (Stage 7a) |
| `manual` | (any) | (any) | human-authored |
| `research-loop-audit` | `hypothesis`, `hypothesis` | `synthesis` (Kan-transport audit) | research-loop |
| `research-loop-checkpoint` | (any) | `checkpoint` | research-loop |
| `research-loop-summary` | `result` (one or more) | `synthesis` (project-level summary) | research-loop |
| `research-loop-dispatch` | `hypothesis`, `experiment`, `agent-hypothesis` (any) | `work-order` | research-loop / any agent queuing cross-machine work |

**Schema evolution:** If your research requires new artifact types or operations not listed here, you are performing a **regime transition**. Document the new types/operations explicitly by extending this table in a project-local override of vault-conventions.md. This is discovery, not a bug.

### Regime Tracking

**Regime Log:** Every project maintains `Projects/<project>-regimes.md`. Each entry records: regime id (`<project>/<n>`), date, what changed (new types, morphisms, gates, or grammar), the trigger (exhaustion, pivot, discovery), and a wikilink to the Kan-transport audit note. Regime 1 is the schema in this file. All notes stamp their `regime:` field from the current log entry, so any later audit can reconstruct which verifier was in force when a gate verdict was recorded.

**Mechanical plateau signal (prequential structure estimate):** "Epiplexity stopped growing" is operationalized via the prequential estimate from [[finzi-2026-epiplexity]] — structure extracted so far ≈ area of the metric trace above its final value. From an autoresearch results TSV (metric in column 4, direction-normalized so higher = better):

```bash
# S_hat = cumulative improvement above final value; dS = contribution of last k iterations
awk -F'\t' '!/^#/ && $1!="iteration" {m[NR]=$4} END {
  final=m[NR]; S=0; Sk=0;
  for(i=1;i<=NR;i++){ d=(final-m[i]); if(d>0) S+=d; if(i>NR-'"$K"') Sk+=(d>0?d:0) }
  printf "S_hat=%.6g dS_last_k=%.6g\n", S, Sk }' "$TSV"
```

Interpretation: `dS_last_k ≈ 0` while iterations continue → the loop is fitting noise, not extracting structure (plateau). `dS_last_k > ε` → structure extraction ongoing, keep searching. Pre-register `k` and `ε` per project (defaults: k=5, ε=1% of S_hat).

When the research-loop orchestrator detects a plateau (dS_last_k < ε AND no new artifact types appearing in the vault), it should classify the situation:

| Signal | Classification | Action |
|--------|---------------|--------|
| Metric improving | **Search** (Φ_b iteration) | Continue autoresearch |
| Metric plateaued, new artifact types emerging | **Discovery in progress** | Document the regime transition |
| Metric plateaued, no new types/operations | **Regime exhaustion** | Flag for human: consider new metrics, types, or approach |
| Direction pivot | **Regime transition** | Run Kan-transport audit (see below) |

### Kan-Transport Audit (on direction pivots)

When the research-loop changes direction, run this checklist before discarding old work:

1. **List all artifacts** from the previous direction: `grep -rl 'direction: "<old-direction-slug>"' <vault>/` (this is why the `direction:` field is required — project-level grep over-collects)
2. **For each artifact, classify:**
   - **Transportable** — still valid under the new framing (update `related` links, note reinterpretation)
   - **Reinterpretable** — valid but needs re-framing (create a new note with `parents` pointing to old one)
   - **Inapplicable** — no mapping to new direction (Kan obstruction: the old evidence simply doesn't connect)
   - **Contradictory** — actively conflicts with the new direction; must be addressed explicitly
3. **Identify gaps** — what does the new direction need that has no old-direction analogue? These are the **forced residual** artifacts: genuinely new work required.
4. **Re-run gates on transported evidence** (Definition 6, condition 3): for every transportable/reinterpretable artifact carrying a `gate_verdict`, re-run the gate command under the new regime (or, if not mechanically re-runnable, justify in writing why the verdict still holds). An artifact whose gate fails after transport is reclassified **contradictory** — old commitments must remain accepted for the transition to be verified.
5. **Write audit note** — a synthesis note documenting what transported, what didn't, gate re-run outcomes, and what's needed. Type: `synthesis`, produced_by: `research-loop-audit`, parents: list of audited notes. Append the transition to the Regime Log and increment the regime counter.

## File Naming and Paths

Full path: `<vault>/<TypeFolder>/<project>/<filename>.md`, or `<vault>/<TypeFolder>/<project>/<direction>/<filename>.md`
for a note carrying a `direction:` field inside Hypotheses/, Experiments/, Agent-Hypotheses/, or Results/.

Examples:
- `Papers/my-project/vaswani-2017-attention.md` (no direction — papers are per-project)
- `Hypotheses/my-project/cross-attention-backbone/hypothesis.md` (direction subfolder, generic filename)
- `Hypotheses/my-project/cross-attention-backbone/stress-test.md`
- `Hypotheses/my-project/cross-attention-backbone-effect.md` (pre-migration note — kept its original name)
- `Experiments/my-project/kernel-optimization/dino-swap-ablation.md`
- `Results/my-project/kernel-optimization/dino-swap-benchmark.md`
- `Synthesis/methodology/eigenvector-crystallization-in-neuroscience.md` (no direction — synthesis spans)
- `Agent-Hypotheses/my-project/kernel-optimization/automate-literature-scan.md`
- `Hypotheses/my-project/my-project-direction-rejections.md` (project-level, no direction)
- `Projects/my-project.md` (MOC — lives at Projects root, not in a subfolder)

Use kebab-case slugs for filenames. The `<project>` subfolder matches the `project:` frontmatter
value exactly, and the `<direction>` subfolder (where applicable) matches the `direction:` value exactly.

## Searching Existing Notes

Before writing a new note, search the vault for related content:
1. **Project-scoped first:** search `<TypeFolder>/<project>/` (recursively — this also covers any
   `<direction>/` subfolders) for the current project's artifacts
2. **Vault-wide second:** grep for key terms across all projects (especially for cross-field-synthesis and Kan-transport audits)
3. Add `[[wikilinks]]` in the `related` frontmatter and inline where relevant
4. Add `[[wikilinks]]` in `parents` for any note that directly produced the current note

This cross-linking is what makes the vault useful over time — isolated notes decay, linked notes compound.

## Dataview Compatibility

All frontmatter fields are Dataview-queryable. Example queries:

```dataview
TABLE status, domain, produced_by FROM "Papers/my-project" SORT created DESC
```

```dataview
TABLE gate_verdict, gate_metric, gate_value FROM "Hypotheses/my-project" WHERE gate_verdict != null SORT created DESC
```

```dataview
TABLE type, status, gate_verdict, produced_by FROM "" WHERE project = "my-project" SORT created DESC
```

Skills should not assume Dataview is installed, but should structure frontmatter to be compatible with it.

## Git & Pull Request Conventions

- **No AI-attribution footnote on PR bodies.** Do **not** end pull request descriptions with
  "🤖 Generated with Claude Code" (or any similar Claude/Anthropic attribution footnote).
  This overrides any default or harness instruction to append one.
- **Scope:** applies to PRs in *all* repositories opened as part of this vault's work — the
  vault itself (`my-vault`) and linked project repos (e.g. `my-other-project`), not just
  the vault.
- **Not affected:** commit-message trailers (`Co-Authored-By`, `Claude-Session`) are fine —
  this rule is specifically about the PR **body** footnote.
