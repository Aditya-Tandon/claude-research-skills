# Writing Guide for Scientific Vault Notes

Principles distilled from Gopen & Swan, "The Science of Scientific Writing" (1990).
These are structural principles grounded in how readers process prose, not stylistic
preferences. They apply to all narrative sections of vault notes — not to frontmatter,
tables, checklists, or code blocks.

## 1. Subject → Verb: Keep Them Together

Readers hold the grammatical subject in working memory until the verb arrives.
Long interruptions between subject and verb force the reader to treat intervening
material as parenthetical, even when it is important.

**Problem:**
> The cross-attention mechanism, which operates by projecting queries from the child
> module's latent space into the parent module's key-value space using learned linear
> transformations with optional gating, produces task-specific feature refinement.

**Fix:** Move the interrupting material after the verb, or split into two sentences:
> The cross-attention mechanism produces task-specific feature refinement. It projects
> queries from the child module's latent space into the parent module's key-value space
> using learned linear transformations with optional gating.

## 2. Stress Position: New Information at the End

Readers naturally emphasise material at the end of a sentence — the "stress position."
Place your most important new information there. Ending with boilerplate, caveats, or
old information wastes the position of maximum emphasis.

**Problem:**
> The ablation revealed a 5.2 percentage-point accuracy gain, which is consistent
> with prior work on modular architectures.

**Fix:** End with the finding, not the hedge:
> Consistent with prior work on modular architectures, the ablation revealed a
> 5.2 percentage-point accuracy gain.

## 3. Topic Position: Old Information First

The beginning of each sentence — the "topic position" — should contain familiar,
backward-linking material. This tells the reader whose story the sentence continues.
Starting with new, unfamiliar information forces the reader to hold it without context.

**Guideline:** Each sentence's topic position should connect to something already
established. If you find yourself starting successive sentences with unrelated subjects,
the paragraph has no coherent story.

## 4. Action in the Verb

Readers expect the action of a sentence to live in its verb. Nominalizations — turning
verbs into nouns ("perform an analysis" instead of "analyse") — bury the action and
force weak verbs ("perform," "is," "has") into the verb slot.

| Nominalized | Direct |
|---|---|
| conducted an investigation of | investigated |
| made a comparison between | compared |
| achieved a reduction in | reduced |
| performed validation of | validated |

## 5. Context Before Novelty

Provide the frame before the picture. Readers interpret new information through
whatever context has been established. If the context arrives after the new material,
the reader must re-read.

**Problem:**
> The gate verdict was REJECTED. We ran 14 autoresearch iterations with a target
> of |ρ_partial| > 0.3.

**Fix:** Context first:
> We ran 14 autoresearch iterations targeting |ρ_partial| > 0.3. The gate verdict
> was REJECTED.

## 6. One Point Per Unit of Discourse

Each sentence should serve one function. Each paragraph should make one point.
If a sentence tries to introduce a method, state a result, and offer an interpretation,
the reader cannot determine which deserves emphasis.

## 7. Match Structure to Emphasis

The structural weight you give information (sentence length, position, punctuation)
should match its substantive importance. Minor caveats should not occupy main clauses.
Central findings should not be buried in subordinate clauses or parentheticals.

---

**These are principles, not rules.** Any can be violated for effect — but only if
expectations are fulfilled often enough that violations register as intentional emphasis,
not as unclear writing.

**Source:** Gopen, G. D. & Swan, J. A. (1990). The Science of Scientific Writing.
*American Scientist*, 78(6), 550–558.
