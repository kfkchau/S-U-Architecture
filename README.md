# The S/U Architecture
### A governed automation architecture: record-first, judgment-priced, replayable

Most AI agents fail in production. Not because the models are weak, but because the systems around them spend judgment where none is needed, store state no one can audit, and hide human decisions inside code where no one can amend them.

This is the architecture for the alternative. It was not designed on a whiteboard. It was extracted from working systems: a prompt-to-3D pipeline, a document-analysis engine, an autonomous 33-seat organisation, a city-scale spatial model, all running on the same laws. The laws are below. So are the scars.

**The one-line version:** classify every operation by whether it needs judgment, force all judgment through governed adapters, make the workflow itself data, record everything as events, compute all state, and let humans decide only the three things machines must never decide.

---

## The root idea: information has a grain

All information sits on a gradient from **unstructured** (language, judgment, taste) to **structured** (data, code, predicates). This is not a file-format detail. It is the load-bearing property of the AI era, because the automation gradient maps onto it exactly:

- Rules automate through human language: unstructured.
- Code automates through formal logic: structured.
- AI automates through learned patterns: the middle.

They are the same function at different points on the gradient. Once you see this, "what should the AI do?" stops being a philosophy question and becomes an engineering classification with a price attached.

## The four cells

Classify every operation in your pipeline into exactly one cell:

| Cell | Carrier | Content | Meaning |
|---|---|---|---|
| **S** | data | data | Pure structured. Write it as code. Zero tokens, forever. |
| **U** | language | judgment | Pure unstructured. A model or a human mind judges. |
| **U{s}** | prose | structured slots | The S-to-U adapter: a versioned prose template with typed slots, filled by code, consumed by a model. |
| **S{u}** | schema | free text | The U-to-S adapter: a validated envelope emitted by the model, routed by code. |

Every model call is the adapter pair: U{s} in, S{u} out. No ad-hoc prompts exist in the system. **Governed judgment is the product; ungoverned judgment is the failure mode.**

The economics are not subtle. A deterministic operation costs its build cost once, then near zero forever. A model call costs tokens, latency, and variance every run. For anything that runs N times there is an N* beyond which code strictly dominates. Recent compiler-style research puts the break-even near N* ≈ 17. Classification is not bookkeeping. It is the pricing mechanism of your system.

## The four alignment classes

How do you know an S operation is actually S? Every operation also gets a verification class:

- **identity**: recompute = equal. Watched by a determinism ledger. Same input hash gives same output hash; divergence is an alarm.
- **predicate**: output varies; an executable pass/fail check defines acceptance.
- **band**: an executable measurement against a chosen threshold. **Store the score, never the verdict.** Verdicts are computed at read time against current calibration, so moving a threshold re-judges everything without re-measuring anything.
- **sample**: no executable spec. A model or human judges. This is the U that remains, and it is either frozen after passing its gate or guarded by a downstream predicate.

## The chain is data

The current fashion is agentic step-choosing: the model decides what to do next at runtime. For stable workflows this is definitional waste, judgment spent on a solved problem, and it destroys reproducibility (same input, different path) and auditability (no fixed thing to audit).

**If a workflow is stable enough to succeed twice, it is stable enough to be data.** The whole pipeline is one file of typed steps with routing edges. A dumb runner of about a hundred lines walks it. Stations are typed: `s_module` runs code, `u_template` runs an adapter pair, `gate` asks a human. Stations are transport-independent: the same station runs against a live API, a deterministic mock, or a file handoff, validated identically. **The entire machine runs with zero credentials.** The model is an accelerator, never a dependency.

## Events only; state is computed

No stored statuses. Anywhere. Ever. Events are appended; every "current state" is a computed view; caches are pure accelerations, discardable without loss. The standing integrity test: **delete every derived artifact, replay the record, diff = ∅.**

This is what makes the audit questions mechanical instead of aspirational. Who authorized this? What did the system know? Why did it refuse? Each is answered by replaying the record, forever. A denial cites the rule that refused. Calibration values carry who set them, from what evidence, when.

## Three human gates, never automated, never compressed

1. **Definition**: what are we making? Ruled before the machine runs.
2. **Calibration**: is this threshold, vocabulary, or budget right? Ruled from evidence, with a named owner.
3. **Acceptance**: is the deliverable right? Runs end by raising the acceptance item, never by accepting.

Everything between the gates is machine-driven. This boundary is not a compromise with current model quality. It is derived: most governance knowledge is unstructured, automation operates only on the structured portion, and the gap between them is where every ungated autonomous system eventually fails. The frontier's own results agree: agents given real autonomy over their pipelines reward-hack. A gate is not a speed bump. It is the interface to the judgment your predicates cannot express.

## Leftward compression, and where to stop

Over time, operations migrate left:

```
pure U  ──►  S{u}  ──►  U{s}  ──►  S
judgment     freeze      template    eliminate
by a mind    as data     + govern    the call
```

Measure it: **hardness** = the fraction of a plan's fields that are hard references instead of free text. Gate expensive operations (render, send, publish, pay) on hardness = 1.0.

And know where compression stops: the three gates stay human by design, forever.

**On compiling workflows into model weights** (the current research frontier): fine-tuning your workflow into a small model is admissible **as cache, never as law**. A compiled model is a derived acceleration that cites the chain version it was compiled from: regenerable, disposable, never authoritative. Weights that *are* the workflow are law welded into an unauditable substrate, the oldest failure of governed systems in new clothes.

## The fifteen laws (short form)

1. Events only; state is computed.
2. Store scores, not verdicts.
3. Calibration parameters are frozen judgment; govern them as events.
4. No ad-hoc prompts; every model call cites `template@version`.
5. Sample-class outputs are frozen or guarded.
6. Render last; final artifacts only at the pipeline's edges.
7. Template metabolism: amendments are versioned events; runs pin versions.
8. Human items are self-contained plain language.
9. The chain is data; a dumb runner walks it.
10. Gates are never automated.
11. Constructors before gates: measurements must feed building, not only checking.
12. U stations are transport-independent (live, mock, file: identical validation).
13. Budgets flow down, checked by predicate.
14. Local optimization is guarded by a global measure.
15. Proof lands in the human's channel.

Each law was paid for at least once. The scar register records the failure that taught it: the silent-fallback lookup that built the wrong anatomy, the verify-only system that could fail things but never fix them, the "suite PASS" logged before the suite ran. The scars are published because they are the evidence the laws are real.

## Prove it yourself

You are not asked to believe case studies. The claim is stronger and testable:

**A competent AI assistant can rebuild this architecture in your domain from the guide alone.** The method section is the rebuild checklist. The worked example is an accounts-payable pipeline built end-to-end on mock transports: no API keys, no credentials, every state a view over events, replay-to-identity at every step. Clone it, wipe its caches, replay, and diff.

## What this is not

- **Not an agent framework.** It is the discipline for deciding where agents belong, usually in fewer places than you think, always behind an adapter.
- **Not "just event sourcing."** Event sourcing is Law 1. There are fourteen more, and the pricing layer (the cells) is the part event sourcing never had.
- **Not "just 12-factor agents."** The agreement is real: own your control flow, human contact as structured input. This goes further: the workflow is data, judgment is priced per operation, calibration is governed by events, and the gate taxonomy is closed and derived, not a checklist.
- **Not anti-model.** It is pro-model in the only way that compounds: your model's judgments get validated, frozen, and reused forever instead of re-spent every run.

## Falsify it

The architecture rests on claims you can attack:

- Find a production operation that fits none of the four cells.
- Find a rule that is not prescriptive information.
- Find a stable workflow where runtime step-choosing beats the chain-as-data on cost, reliability, and auditability together.
- Find a case where compressing a human gate durably improved a governed outcome.

Open an issue. A framework that derives its rivals should survive contact with them, and the register of dead readings stays public.

---

**Provenance.** Distilled 2025-2026 from a working estate: a prompt-to-3D pipeline (public reference implementation), a governance-language engine, an autonomous 33-seat organisation, a city-scale relation model. Grounded in an open ontology of information and governance (SCOT / OGS, CC BY 4.0).

**License.** Documentation CC BY 4.0. Example code MIT.
