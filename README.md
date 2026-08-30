# claude-research-skills

A collection of Claude Code skills for structured, agent-driven scientific research. These skills chain together into a full investigation pipeline — from literature review and hypothesis generation through adversarial stress-testing, experiment design, autonomous execution, and vault-persisted results with formal provenance tracking.

The pipeline is grounded in the categorical discovery framework of [Wang & Buehler (2026)](https://arxiv.org/abs/2606.01444), incorporating typed artifact provenance, formal gate records, search/discovery distinction, and evidence transport on direction pivots. Quantitative evaluation machinery draws on the [Model Discovery Agent (Murphy et al., 2026)](https://arxiv.org/abs/2608.09696) for Bayesian experiment design and M-open predictive checks, and on [Finzi et al. (2026)](https://arxiv.org/abs/2601.03220) for the prequential structure estimate used in plateau detection.

---

## Skills

### Core Research Pipeline

| Skill | Description |
| :-- | :-- |
| **research-loop** | The orchestrator. Chains all other skills into a staged investigation pipeline (probe, literature grounding, direction generation, stress test, experiment design, execution, synthesis). Two modes: *immersive* (user in the loop at every decision gate) and *autonomous* (agents execute independently, user reviews results). Includes gate design checklists, specification violation detection (CoE I2), Kan-transport audits on direction pivots, M-open predictive checks, and discovery-design reinforcement. |
| **paper-triage** | Structured extraction and triage of academic papers (PDF, arXiv URL, or pasted text) into Obsidian-compatible vault notes. Extracts title, authors, core claim, method, key results, baselines, limitations, and open questions. Includes citation verification against Semantic Scholar/arXiv APIs. |
| **idea-bouncer** | Structured brainstorming with four modes: research directions, architecture/design, feature scoping, and problem decomposition. The research directions mode scans a graveyard of rejected results, performs structural analysis (minimal cases, essential invariants), and generates candidates through multi-framing. Maintains a direction rejection log. |
| **agent-hypothesis-gen** | Decomposes experiment plans into 3-7 independently testable sub-hypotheses with H_0/H_1/test statistic/decision rule structure. Generates ready-to-use agent prompts or autoresearch configs. Includes gate design checklist, Occam penalty for competing hypotheses, and git worktree isolation for parallel execution. |
| **hypothesis-stress-test** | Adversarial hypothesis critique. Formalises claims, generates 3-5 counterarguments with severity ratings, searches for contradictory evidence, and rates robustness (robust / promising-but-fragile / weak / undetermined). Verdict is blind-judged, not self-graded. |
| **experiment-design-review** | Reviews experimental designs: checks controls, suggests ablations ordered by information value, reviews metrics. Includes Value-of-Information discriminant analysis for competing hypotheses, sufficiency-checked proxy metrics for HPC, and a reproducibility checklist. |
| **cross-field-synthesis** | Translates concepts across fields. Formalises in field-neutral language, builds translation tables, requires four-level formalisation (shared math, quantitative correspondence, testable prediction, qualitative only). Identifies transferable theorems and methods. |
| **autoresearch** | Autonomous goal-directed iteration loop: modify, verify, keep/discard against any metric. 14 subcommands including core metric loop, debug, fix, security audit, ship, scenario generation, adversarial debate, and orchestrator mode for natural-language goals. **Forked from [karpathy/autoresearch](https://github.com/karpathy/autoresearch) and substantially extended** with orchestrator routing, goal archetypes, predicate-bearing loops, independent verify & overfit guards, and integration with the research-loop pipeline. |

### Code Quality

| Skill | Description |
| :-- | :-- |
| **clean-commit-gate** | Pre-commit quality gate that catches AI-generated bloat, unfocused changes, dead code, debug artifacts, and poor commit messages. Proposes commit splits for multi-concern diffs. Three strictness levels. |
| **thermonuclear-pr-review** | Aggressively thorough PR review that checks diffs against the full codebase for structural regressions, missed simplifications, architectural drift, spaghetti growth, and maintainability problems. Reviews at two levels simultaneously: the diff and its interaction with the rest of the codebase. |

### Reference Files

| File | Description |
| :-- | :-- |
| **vault-conventions.md** | The schema backbone. Defines the Obsidian vault folder structure, YAML frontmatter fields (typed provenance, gate records, gate design, MDA-derived fields, code provenance, claim verification), the schema registry of allowed artifact types and morphisms, regime tracking, Kan-transport audit protocol, and work order format for cross-machine dispatch. |
| **writing-guide.md** | Scientific writing principles from Gopen & Swan (1990): subject-verb proximity, stress position, topic position, action in the verb, context before novelty. Applied to all narrative sections of vault notes. |

---

## Installation

These skills are designed for [Claude Code](https://docs.anthropic.com/en/docs/claude-code) (Anthropic's CLI for Claude). To install them:

### Option 1: Copy into your project

Copy the `skills/` directory into your project's `.claude/skills/` folder:

```bash
cp -r skills/* /path/to/your/project/.claude/skills/
```

The reference files (`references/vault-conventions.md` and `references/writing-guide.md`) are referenced by several skills. You can either place them alongside the skills or update the reference paths in the SKILL.md files that use them (research-loop, paper-triage, cross-field-synthesis, experiment-design-review).

### Option 2: User-level skills

For skills you want available across all projects, copy them to your user-level Claude Code skills directory:

```bash
cp -r skills/* ~/.claude/skills/
```

### Vault setup (optional but recommended)

The research pipeline writes structured notes to an [Obsidian](https://obsidian.md/) vault. If you want to use the vault integration:

1. Create or designate an Obsidian vault for research artifacts
2. Create the folder structure described in `references/vault-conventions.md`
3. Symlink the vault as `.vault/` in your project root, or configure the absolute path

Without a vault, the skills still function for analysis and brainstorming — they just won't persist structured notes.

---

## How the Pipeline Fits Together

```
Stage 0    Probe (autoresearch:probe)
             |
Stage 0.5  Literature Grounding (paper-triage)
             |
Stage 1    Direction Generation (idea-bouncer)
             |
Stage 2    Stress Test (hypothesis-stress-test + autoresearch:reason)
             |
Stage 3    Cross-Field Synthesis (cross-field-synthesis) [optional]
             |
Stage 4    Experiment Design (experiment-design-review)
             |
Stage 5    Hypothesis Decomposition (agent-hypothesis-gen)
             |
Stage 6    Execution (autoresearch) + I2 Spec Violation Check
             |--- REJECTED: preserve evidence, log to vault, clean up
             |--- ACCEPTED: clean-commit-gate -> push -> thermonuclear-pr-review
             |
Stage 6.5  M-Open Check + Plateau Detection + Regime Classification
             |
Stage 6.75 Kan-Transport Audit [on direction pivots]
             |
Stage 7    Vault Write + Claim Verification + Synthesis
```

Each stage produces typed artifacts with formal provenance (parents, produced_by, parent_types) that form a directed provenance graph. Gate records (verdict, metric, threshold, observed value) are required on all evaluated hypotheses, including rejections.

---

## Key Concepts

**Two modes:** *Immersive* mode (default in interactive sessions) keeps you in the loop at every decision gate — you build understanding alongside the investigation. *Autonomous* mode (default for `claude -p`) lets agents execute independently with mechanical verification, surfacing only final results and genuine decision points.

**Gate design, not just gate recording:** The pipeline's gate design checklist (Stage 5) addresses four failure classes: blind gates that can't see the defect they test for, incomparable reference values, uncontrolled confounds, and underpowered experiments. Gates are designed before execution, not just recorded after failure.

**Specification violation detection:** After autonomous execution, a two-layer check (mechanical diff analysis + LLM majority vote) detects code that earns its metric by gaming the evaluation. Violations are rejected without evaluating the gate.

**Evidence preservation:** Rejected hypotheses are never deleted. Branches are preserved under `refs/experiments/`, vault notes record what was tried and why it failed. Failed experiments constrain the search space.

**Regime transitions:** When the current framework exhausts what it can find, the pipeline distinguishes search exhaustion from discovery opportunity. Direction pivots trigger a Kan-transport audit that classifies which old evidence carries forward, what needs re-framing, and what's genuinely new work.

---

## References

1. Wang, F. & Buehler, M. J. (2026). [Self-Revising Discovery Systems for Science: A Categorical Framework for Agentic Artificial Intelligence](https://arxiv.org/abs/2606.01444). *arXiv:2606.01444*.
2. Murphy, K. et al. (2026). [Model Discovery Agent: LLM-assisted Bayesian experiment design for data-efficient discovery of mechanistic world models](https://arxiv.org/abs/2608.09696). *arXiv:2608.09696*.
3. Finzi, M. et al. (2026). [From Entropy to Epiplexity: Rethinking Information for Computationally Bounded Intelligence](https://arxiv.org/abs/2601.03220). *arXiv:2601.03220*.
4. Gopen, G. D. & Swan, J. A. (1990). The Science of Scientific Writing. *American Scientist*, 78(6), 550-558.
5. Karpathy, A. (2025). [autoresearch](https://github.com/karpathy/autoresearch) — the original autonomous iteration loop that the autoresearch skill in this repo was forked from and extended.
6. Cranmer, K., Brehmer, J. & Louppe, G. (2020). The frontier of simulation-based inference. *PNAS*, 117(48), 30055-30062.

---

## License

MIT
