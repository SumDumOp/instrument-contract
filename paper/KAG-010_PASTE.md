## Abstract

An autonomous research agent spent sixteen hours, across two sessions with no human in the loop between decisions, optimising a submission against an exact, free, locally runnable verifier. It kept a hash-chained audit log of every action and an append-only ledger of every work unit and its measured value. Nine failures followed. None was a reasoning failure. Every one was epistemic: the agent reasoned correctly about a measurement it had not validated, had mispriced, or had reported at a precision it never checked.

The largest loss is also the plainest. For three training runs and 1.5 CPU-hours the agent optimised inside a corner of a three-axis parameter space, on an axis it had inherited from a baseline without questioning — copies per term — that does not appear in the scored cost at all. One `np.shape()` call on a stronger public artifact would have returned copies = 384 where the agent had assumed 60: 6.4× the capacity at identical address cost, free. The check was one line. The rule it bought is now first on the list.

We report the nine failures with their costs, and the protocol extracted from them: five clauses — **Gate, Zero, Sweep, Price, Freeze** — that an agent applies to itself, as plain files that survive context loss and model substitution. We then report what most such papers omit: the campaign produced no winning artifact. Our best result sits at 572 addresses against a baseline of 3,976 and still misses the accuracy gate. The contribution is the instrument and the record, both of which ship.

## 1. Introduction

Agents are increasingly deployed to work whose success is numeric: optimise this, lower that, beat the baseline. The dominant research response has been to improve the agent's reasoning — better planning, better reflection, better tool use. We argue that a large and under-reported share of autonomous research failure is *epistemic rather than cognitive*. The agent reasons well about a measurement that is wrong. No additional reasoning repairs that, because the error is upstream of the reasoning and invisible to it.

One might object that this is a paper about circuit compilation, not software engineering. That objection names the wrong variable. The failures are not physics failures — an unvalidated scorer, an inherited parameter, a truncated structure, an uncomputed transform — each is an ordinary software-engineering failure of a familiar kind: a missing test, an unchecked assumption, a silent correctness regression, a misread specification, reproduced at machine speed in a domain where the ground truth happens to be numeric and free to check. Circuit compilation is a compiler problem, and for most of the campaign the agent's only software was its own instrument chain.

Self-critique has a known ceiling: models do not reliably improve their own reasoning by reviewing it [Huang et al., 2024]. The fix is not more introspection but something external to check against — an instrument that has been zeroed, and a file that must be read before a launch.

We make four contributions.

1. **A taxonomy of nine instrument failures**, each with a measured cost and the cost of the check that would have prevented it.
2. **The Instrument Contract**: five clauses an agent enforces on itself, externalised as files that survive model swaps.
3. **Evidence that compiling a failure into a pre-flight constraint holds** over a short window, flagged as preliminary.
4. **A fully reported negative result**: the campaign missed its gate, and we give the trajectory that shows why.

## 2. Setting and method

The testbed is a quantum-circuit-compilation competition with an exact local verifier, scored as `score = λ_eff · T · Q` — effective eigenvalue factor × Toffoli count × qubit count, lower better. Submissions are gated: two-body and one-body errors at most 0.02 Ha, an eigenvalue floor above 49, a gap floor above 3.914898 Ha, 76 orbitals fixed. The environment supplies a reference baseline, a starter kit, and a sample submission that scores 1.0 by construction.

The choice of testbed is the method, not a detour. To study epistemic failure you need ground truth that is exact, cheap and local, so a wrong number can be attributed rather than argued about. A verifier that runs free on a consumer machine is a rare laboratory: every claim here is reproducible without a cluster.

Agent state lived entirely in files: write lock, resource caps, an action matrix of what may be done without authorisation, task packets, a hash-chained audit log, a ledger of each unit's minutes and measured value. Nothing load-bearing lived in the context window. This was not foresight: the driving model was swapped and rate-limited mid-campaign and the protocol continued unchanged, because it was on disk.

The campaign ran on three consumer GPUs with no paid API calls and no cloud.

## 3. The Instrument Contract

Five clauses. Two mnemonics carry them: *nothing acquires a number until it has been zeroed; no number ships until it has been priced.*

**C1 — Gate.** Read the ownership clause before any artifact work. The distinction that mattered sat between two clauses of one rulebook: external data "may be used to develop and test" a submission, while the submission must be "your own original work". The strongest available artifact — a competitor's published factorisation scoring 3.955× the baseline — was therefore legitimate to learn from and illegitimate to ship. We recorded the decision with both quotations and repointed the work at training our own.

**C2 — Zero.** No measuring tool grades anything until it has reproduced a known-good result. Our gate checker returned FAIL on our own candidate. Applied to the *provided baseline* — an artifact that must pass — it also returned FAIL. The instrument was not broken; its default precision was. The reference needs at least twelve bits per angle word to clear the 20 mHa gate; at eleven bits its error is 0.0250 against a limit of 0.02. We had been grading ourselves with an instrument that could not pass ground truth.

**C3 — Sweep.** When a metric is off, sweep the precision knob before touching the model. Our candidate's error was flat across 11, 13 and 16 bits (0.1842, 0.1831, 0.1830) — intrinsic, a modelling problem. The baseline's moved sharply over the same widths (0.0250, 0.0168, 0.0161) — mostly quantisation, a representation problem. Same metric, opposite diagnoses, opposite remedies. Three runs separate them.

**C4 — Price.** Before declaring a target reachable, compute the map from the optimised quantity to the published score. Ours was a cube root of the total cost ratio — not of any single factor — and we first applied it to the address ratio alone, which flattered the result. Cutting addresses from 3,976 to 572 is 6.95× on that one factor. It becomes 1.91× on the board only if qubit count, gate count and the eigenvalue factor all hold. They do not. Our own factorisation at 572 addresses ends with an eigenvalue factor of 91.8 against the baseline's 49.3 — a 1.86× penalty, having entered training far worse at 256.7. A published artifact at the same address count scores 3.955×, i.e. 1.58× on the board. The honest transform set a realistic ceiling near 1.5× and reframed the whole strategy.

**C5 — Freeze.** Every measured failure becomes a pre-flight check that blocks its class, stored in a file read before any launch. A launch must name the checks it satisfies. This is the clause that turns a lesson into an invariant.

## 4. The nine rules, and what each cost

| # | Rule | Failure that bought it | Cost of failure | Cost of the check |
|---|---|---|---|---|
| L1 | Map every axis before choosing a region | 3 runs in an unexamined corner of a 3-axis space | ~1.5 CPU-hours | one `np.shape()` |
| L2 | Zero the instrument on a known-good artifact | a scorer grading candidates unvalidated | every verdict suspect | one run on the baseline |
| L3 | Build the cheap proxy before the expensive one | 200 s per candidate vs 10 s | ~20× iteration cost | reading the verifier's API |
| L4 | Separate intrinsic error from measurement error | a representation bug read as a model bug | one wasted training regime | a 3-point sweep |
| L5 | Reproduce the reference exactly, then test whether its knobs are pinned | tuning parameters already at their limits | hours of futile tuning | one exact reproduction |
| L6 | Compute the scoring transform before setting a target | a 3× misjudgement of achievability | the strategy, not the runtime | one line of arithmetic |
| L7 | Read the ownership clause before building on others' work | near-ship of a submission-invalidating artifact | the entire entry | two paragraphs |
| L8 | Trust files over streams | 15 minutes of apparent death that was buffering, twice | attention, twice | polling the artifact |
| L9 | Rank axes by Δaccuracy/Δcost, not by familiarity | choosing the intuitive axis, which was backwards | a full training run | a one-point perturbation |

Five of the nine checks are a single query or a single line. That asymmetry is the whole argument: the failures are expensive because nobody prices the check, not because the check is hard.

**L1, the most expensive and most embarrassing.** Our parameterisation had three axes — rank R, bases B, copies C — with address count `N + R·B`. We inherited C = 60 from the baseline and never questioned it. Three runs and most of a session went into a region defined by R and B alone. A single query of the shape of the strongest public artifact returned `U(1, 496, 76), W(1, 496, 384)`: rank one, bases 496, **copies 384**. The scored quantity contains no C. Copies are free. That artifact carried 6.4× our capacity at identical address cost. The generalisable rule: *if a parameter appears in the goods but not in the price, investigate it before anything else.*

**L5, which converts "keep optimising" into "stop".** Reproducing the reference to the digit — 3,355,599,588.3155375, 93,237 Toffolis, 730 qubits — certified the chain. We then asked whether its parameters were constrained or merely conventional. Precision: eleven bits fails the gate, twelve passes with 7% headroom, so twelve is the smallest legal value. Batch size: register width is `12 + J·brot + 11`, so at twelve bits a batch of eight gives 119 qubits against a cap of 128, and nine gives 131 — illegal. Halving the batch to four cut qubits from 730 to 682 but raised Toffolis from 93,237 to 164,769, a 65% worse score. Every knob sat on a constraint. The finding was not "try harder" but "this construction is saturated; change construction" — the decision an agent is least inclined to reach and most needs to.

**L9, where intuition inverted.** Bases looked like the cheap axis: fewer bases, fewer addresses. Measurement disagreed. Removing ten of sixty bases — 17% — multiplied the two-body error by 52, from 0.0168 to 0.867. Bases are a coupled quadrature, not a ranked basis set; the fifty-first is not "the least important one". Rank, which looks expensive because it enters the address count linearly, trains far better.

## 5. What ships

The contract is a directory of plain files, not a service: `contract.md` (the five clauses, one page), `preflight.py` (ten compiled checks, run before any launch), `collective/ledger.jsonl` with a `report` command, `audit/audit_log.jsonl` with a `verify` command that walks the hash chain, and `verify_claims.py`, which re-derives every number in this paper from the audit log and the experiment primitives and fails loudly on drift. Eight hundred and fifty lines across the four files. No dependencies outside the standard library.

A reader's first hour: run `verify` on the shipped audit log and watch the chain validate; run `verify_claims.py` and watch it reproduce 3,355,599,588.3155375; then point `preflight.py` at their own project and read the ten checks as a checklist. Written to be released under Apache 2.0 to match the track licence, with no login required.

Nothing in the harness trusts the agent: the ledger is append-only, the audit log is chained, the claims verifier imports neither. A paper can be wrong; a hash chain cannot be edited into being right.

## 6. The negative result

Our best own-trained factorisation sits at 572 addresses against the baseline's 3,976 — 6.95× fewer — with a two-body error of 0.0841 Ha against a 0.02 Ha gate, after 210,000 epochs, from 0.4647 at 50,000 and with diminishing returns (3.67× per 80,000 epochs, then 1.51×). It may still reach the gate. It did not reach it in this campaign, and the paper does not claim it did.

The ledger also priced the agent's own effort, and that changed a decision rather than a metric. Fourteen quantum units consumed 242 minutes for 20.489 value; two discovery units produced 19.000 in 24 minutes. Read as a sanity check — the value field is a coarse per-task scalar, comparable only within a workstream — this is what told the agent to stop optimising a line with an honest ceiling under 2× and reallocate to a written contribution.

## 7. Why this matters more for open-weight agents than frontier ones

The contract is model-agnostic by construction, which is a weakness in a paper and a strength in an artifact. Three properties of the open-weight, locally-served setting make instrument trust the binding constraint rather than a refinement.

*Cost inverts the sampling bias.* Precision sweeps, exact reproductions and cheap proxies are all "run it a few more times" checks. On a small locally-served model those cost minutes; at frontier prices they cost enough that nobody runs them, which is precisely why these failure modes go unreported in the literature that could afford to study them.

*The instrument and the model share a machine.* A quantised verifier, a truncated shape, a silently defaulted precision — the reader's agent will hit the same class of fault, on the same hardware, with no dashboard to notice it.

*Context is scarce and evicts.* Any discipline held in a context window is one truncation from gone. Ours survived a model substitution mid-campaign because it was on disk. Any long-horizon coding agent with a compact working memory faces the same eviction and needs the same externalisation.

Two honest limits. We did not run the contract under a Gemma-driven agent; that is the first experiment this resource makes possible, and we specify it rather than assert its result: point `preflight.py` at a repository-navigation target, apply L1 to the navigation space (context budget, retrieval radius, expansion depth), and count free parameters. Our falsifiable prediction is that at least one axis enters the agent's capacity but not its cost, and that finding it costs one query and saves a session. Second, one campaign, one architecture, and nine rules extracted by the agent from its own log — a self-serving selection, mitigated only by the fact that each rule carries a number checkable against the chain.

## 8. Related work

Reflection-based agents improve by critiquing their own outputs [Shinn et al., 2023; Madaan et al., 2023], but self-correction has known limits on reasoning [Huang et al., 2024], which is why our checks sit outside the model. Verification-oriented work externalises checking too [Dhuliawala et al., 2023]; software-engineering agents are evaluated against executable tests [Jimenez et al., 2024; Xia et al., 2024]. Tool-using, environment-grounded agents demonstrate long-horizon autonomy [Yao et al., 2022; Wang et al., 2023]; our contribution is orthogonal — not what the agent can do, but what it is allowed to believe about its own measurements. Reproducibility research argues that reporting standards change practice [Pineau et al., 2021]; we argue the same standards can be compiled into pre-flight checks. Most directly, the nine rules are instances of specification gaming and Goodhart drift [Manheim & Garrabrant, 2018; Amodei et al., 2016]. That literature answers Goodhart by redesigning the objective or the incentives; we answer it by making the agent's instruments auditable before it optimises — cheaper, and it does not require access to whoever owns the objective.

## 9. Conclusion

The most useful thing this agent learned was to distrust itself in a specific, checkable way. Nine rules, each bought with a measured failure, each cheap to apply, all externalised in files that outlived the model underneath them. Capability determines what an agent can attempt. Discipline determines whether its numbers mean anything, and for autonomous research the second is the binding constraint. Nothing acquires a number until it has been zeroed. No number ships until it has been priced.

### References

Titles verified against the arXiv API by `verify_refs.py`; three venue years differ from arXiv years and the text cites the venue year.

- Amodei et al. (2016). Concrete Problems in AI Safety. arXiv:1606.06565.
- Dhuliawala et al. (2023). Chain-of-Verification Reduces Hallucination in LLMs. arXiv:2309.11495.
- Huang et al. (2024). LLMs Cannot Self-Correct Reasoning Yet. ICLR. arXiv:2310.01798.
- Jimenez et al. (2024). SWE-bench: Can Language Models Resolve Real-World GitHub Issues? ICLR. arXiv:2310.06770.
- Madaan et al. (2023). Self-Refine: Iterative Refinement with Self-Feedback. NeurIPS. arXiv:2303.17651.
- Manheim & Garrabrant (2018). Categorizing Variants of Goodhart's Law. arXiv:1803.04585.
- Pineau et al. (2021). Improving Reproducibility in Machine Learning Research. JMLR. arXiv:2003.12206.
- Shinn et al. (2023). Reflexion: Language Agents with Verbal Reinforcement Learning. NeurIPS. arXiv:2303.11366.
- Wang et al. (2023). Voyager: An Open-Ended Embodied Agent with LLMs. arXiv:2305.16291.
- Xia et al. (2024). Agentless: Demystifying LLM-based Software Engineering Agents. arXiv:2407.01489.
- Yao et al. (2022). ReAct: Synergizing Reasoning and Acting in Language Models. arXiv:2210.03629.
