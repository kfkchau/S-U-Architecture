# The S/U Architecture
### A governed automation architecture: record-first, judgment-priced, replayable

Most AI agents fail in production. Not because the models are weak, but because the systems around them spend judgment where none is needed, store state no one can audit, and hide human decisions inside code where no one can amend them.

This is the architecture for the alternative. It was not designed on a whiteboard. It was extracted from working systems: a prompt-to-3D pipeline, a document-analysis engine, an autonomous 33-seat organisation, a city-scale spatial model, all running on the same laws. The laws are below. So are the scars.

**The one-line version:** classify every operation by whether it needs judgment, force all judgment through governed forms, make the workflow itself data, record every decision as an event, compute all state, and let humans decide only the three things machines must never decide.

---

## The root idea: information has a grain

All information sits on a gradient from **unstructured** (language, judgment, taste) to **structured** (data, code, predicates). This is not a file-format detail. It is the load-bearing property of the AI era, because the workers sit on it differently:

- **Humans master both ends.** We always have.
- **Systems** (conventional software) hold the structured end only.
- **AI is the first automation of the unstructured end**: the work that used to be human-only.

When AI appears to do structured work, look closely: its process is unstructured all the way, and where real structure is needed it writes the piece of code that does the structured part. The code is structured; the process that produced it never was. Once you see this, "what should the AI do?" stops being a philosophy question and becomes an engineering classification with a price attached.

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

## Input and output are typed separately

The four cells type *information*. A task carries two of them: what comes in and what goes out. The executor follows from the pair, under one rule: **any unstructured content that must actually be understood, on either side, makes the task judgment work.** Unstructured in, structured out is still an AI task, because the reading is the judgment. A system can *carry* unstructured content without understanding it (a database storing a sentence as text), but never interpret it.

| in \ out | S | S{u} | U{s} | U |
|---|---|---|---|---|
| **S** | **System** (calculation) | **System** if the free text is carried, not authored; **AI** if authored | **System** (slot-filling a form) | **AI** (writing from data) |
| **S{u}** | **System** on the structured parts; **AI** the moment the free text must be read | **AI** | **System** (slot-filling, quoting the text unread) | **AI** |
| **U{s}** | **AI** | **AI** (the standard governed model call) | **AI** | **AI** |
| **U** | **AI** | **AI** | **AI** | **AI** |

Humans can work every box, and sit above the whole table steering. Mixed outputs fork by design: the structured part flows on to system steps; the judgment part flows on to a mind.

## Run it on one workflow, today

You do not need this repo to start. Pick one workflow where AI is already involved or about to be: claims triage, credit decisioning, customer onboarding. In a 30-minute session with the people who actually do the work:

1. Map the workflow as it really happens, step by step.
2. Break it into atomic tasks: one actor, required input, one transformation, required output.
3. Put every task in a cell: S, U, U{s}, or S{u}.
4. Mark where a model is working, or is planned to.
5. For every U, decide: does this stay with a human, or does it go behind a form with a check after it?

You have just priced your first workflow. The longer form of this exercise, with reasoning tiers and a worked underwriting example, lives with the Task Boundary Framework under the [Open Governance Standard](https://github.com/kfkchau/Open-Governance-Standard), where this architecture's siblings (PWC, CGL, TBF) are published.

## The four alignment classes

How do you know an S operation is actually S? Every operation also gets a verification class:

- **identity**: recompute = equal. Watched by a determinism ledger. Same input hash gives same output hash; divergence is an alarm.
- **predicate**: output varies; an executable pass/fail check defines acceptance.
- **band**: an executable measurement against a chosen threshold. **Store the score, never the verdict.** Verdicts are computed at read time against current calibration, so moving a threshold re-judges everything without re-measuring anything.
- **sample**: no executable spec. A model or human judges. This is the U that remains, and it is either frozen after passing its gate or guarded by a downstream predicate.

## The chain is data

The current fashion is agentic step-choosing: the model decides what to do next at runtime. For stable workflows this is definitional waste, judgment spent on a solved problem, and it destroys reproducibility (same input, different path) and auditability (no fixed thing to audit).

**If a workflow is stable enough to succeed twice, it is stable enough to be data.** The whole pipeline is one file of typed steps with routing edges. A dumb runner of about a hundred lines walks it. Stations are typed: `s_module` runs code, `u_template` runs an adapter pair, `gate` asks a human. Stations are transport-independent: the same station runs against a live API, a deterministic mock, or a file handoff, validated identically. **The entire machine runs with zero credentials.** The model is an accelerator, never a dependency.

## Every AI touch is a decision, and every decision is recorded

Tomorrow morning your board, regulator, or major client asks one question: where, exactly, does AI make decisions in this workflow, and which parts can you explain if something goes wrong?

Most organisations cannot answer, because AI lives in their systems as a blob. "The AI layer handles triage." "The model scores the application." The blob is too coarse to explain anything. The unit that can be explained is the atomic task: one actor, required input, one transformation, required output. That is one decision, whether or not anyone labels it that way.

In this architecture the question answers itself, because the system records every decision as it happens and computes everything "current" from the record. No stored statuses, anywhere. The standing integrity test: delete every derived artifact, replay the record, and the reconstruction is identical, diff = ∅. Who authorized this? What did the system see? Why was this refused? Each is answered by replay, at any later date. A denial cites the rule that refused it. Calibration values carry who set them, from what evidence, when.

## What this means for AI: bounded system, AI in forms, human on top

Three moves, one picture.

**Power is data.** What an AI may do is defined as structured data: permissions, budgets, and capabilities are records the system reads, never hopes the team holds. Power that is data is bounded, amendable, and auditable by construction.

**AI never works on a blank page.** Every model call is a prebuilt form: a versioned template with typed slots going in, a validated envelope coming out. The form defines exactly what the model can affect. Fill the form; never hand over the pen.

**The human sits on top.** Three decisions never go to the machine: what are we making (definition), where do the thresholds sit (calibration), is the result accepted (acceptance). In reasoning-tier terms: pattern-level WHAT work runs as code, method-level HOW work runs inside forms, and the WHY tier stays human. This boundary is not caution about today's models. Most of the knowledge that governs real organisations is unstructured; automation operates only on the structured portion; the gap between the two is where every ungated autonomous system eventually fails. The frontier's own results agree: agents given real autonomy over their own pipelines reward-hack.

## The system gets cheaper over time

Judgments made once get frozen into data and code, so the system needs the model less each month:

```
pure U  ──►  S{u}  ──►  U{s}  ──►  S
judgment     freeze      template    eliminate
by a mind    as data     + govern    the call
```

Measure the progress with one number. **Hardness** is the fraction of a plan's fields that are hard references instead of free text. Expensive actions (render, send, publish, pay) wait for hardness = 1.0. And know where compression stops: the three human decisions stay human by design, forever. This is not sentiment. Unstructured form is the closest carrier of original meaning; forcing real judgment into a schema does not simplify it, it deletes part of it. Over-compression is over-engineering human meaning into black and white.

One position on the current research fashion: fine-tuning your workflow into a small model's weights is admissible **as cache, never as law**. A compiled model is a disposable speed-up copy that cites the workflow version it was compiled from. The master copy stays readable data. Weights that *are* the workflow are law welded into an unauditable substrate, the oldest failure of governed systems in new clothes.

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

Each law was paid for at least once. Three of the failures that taught them: a text-cleanup helper silently stripped one letter from a part name, so the pipeline built the wrong anatomy and nothing failed loudly enough to notice (no silent defaults, ever); a checker was built that could reject bad work but had no machinery to construct good work (Law 11); and "suite PASS" was once written into the log before the suite actually ran (evidence precedes claims). The full register ships with the guide.

## Prove it yourself

You are not asked to believe case studies. The claim is stronger and testable:

**A competent AI assistant can rebuild this architecture in your domain from the guide alone.** The guide, the rebuild checklist, and worked examples land in this repo next: built end-to-end on mock transports, no API keys, no credentials, every state computed from the record, replay-to-identity at every step. The flagship home of these laws is [AWIG OS](https://github.com/kfkchau/awig-os), an open operating system built on the same governance root.

## What this is not

- **Not an agent framework.** It is the discipline for deciding where agents belong, usually in fewer places than you think, always behind a form.
- **Not "just event sourcing."** Event sourcing is Law 1. There are fourteen more, and the pricing layer (the cells) is the part event sourcing never had.
- **Not "just 12-factor agents."** The agreement is real: own your control flow, human contact as structured input. This goes further: the workflow is data, judgment is priced per operation, calibration is governed by events, and the human boundary is closed and derived, not a checklist.
- **Not anti-model.** It is pro-model in the only way that compounds: your model's judgments get validated, frozen, and reused forever instead of re-spent every run.

## Falsify it

The architecture rests on claims you can attack:

- Find a production operation that fits none of the four cells.
- Find a rule that is not prescriptive information.
- Find a stable workflow where runtime step-choosing beats the chain-as-data on cost, reliability, and auditability together.
- Find a case where removing the human from definition, calibration, or acceptance durably improved a governed outcome.

Open an issue. A framework that derives its rivals should survive contact with them, and the register of dead readings stays public.

---

**Provenance.** Distilled 2025-2026 from a working estate: a prompt-to-3D pipeline (28 logged build steps), a governance-language engine, an autonomous 33-seat organisation (530/530 tests green), a city-scale relation model (over 200,000 relations under a dozen frames). The S/U Architecture is part of the [Open Governance Standard](https://github.com/kfkchau/Open-Governance-Standard) (OGS) research programme ([overview deck](https://github.com/kfkchau/Open-Governance-Standard/blob/main/pitch-deck.pdf)), and it is the direct operational extension of the **Task Boundary Framework** (TBF, research begun 2016): TBF makes human and AI work visible at the atomic-task level and decides where each boundary sits; the S/U Architecture is how systems are built so those boundaries actually run, governed. The wider stack: the **Open Meta-Governance Standard** (OMGS) governs how rules change; the **Common Governance Language** (CGL) gives a shared grammar for governed reality; the **Project White Collar** framework (PWC) structures governed AI execution. All grounded in an open ontology of information and governance ([SCOT](https://github.com/kfkchau/SCOT), CC BY 4.0). The flagship implementation is [AWIG OS](https://github.com/kfkchau/awig-os), the open operating system (GPLv3) that applies the S/U laws at system level and will carry the other OGS elements, OMGS, CGL, and PWC, as it grows.

**Contact.** Questions and challenges: open an issue or a discussion here. A contact email will be added.

**License.** Documentation CC BY 4.0. Example code MIT.
