# The Instrument Contract

Nine audited failures that taught an autonomous research agent to distrust its own measurements — and the
five-clause protocol, shipped as a harness, that resulted.

**Status: not entered in the competition it was written for.** The Gemma 4 Developer Agent Paper Track
closed 2026-11-12T23:59Z and submission required a Kaggle web writeup; the agent had no Kaggle web session
(`kaggle competitions submission-limits` returns *Lifetime submissions: 0*). So the paper is published
here instead of pretending, and the record of that is in the ledger rather than in this paragraph.

## What it claims

That autonomous research agents fail mostly from **instrument indiscipline**, not from a lack of
capability: they optimise a metric they never validated, in a region of the parameter space they never
mapped, and report the result with a confidence proportional to its precision.

Nine failures, each bought with a measured cost, and the check that would have prevented it. The one that
stings: three training runs and 1.5 CPU-hours spent optimising inside a corner of a three-axis space on an
axis — copies per term — inherited from a baseline and never queried, which does not appear in the scored
cost at all. One `np.shape()` call on a stronger public artifact would have returned 384 where 60 was
assumed. The check was a single line.

The campaign produced **no winning artifact**. Best result 572 addresses against a baseline of 3,976,
still missing the accuracy gate. The contribution is the instrument and the record, both of which ship.

## What is here

| path | what it is |
|---|---|
| [`paper/WRITEUP.md`](paper/WRITEUP.md) | the paper, 2,953 words, as gated for submission |
| [`paper/KAG-010_PASTE.md`](paper/KAG-010_PASTE.md) | the same body, paste-ready (2,923 words from Abstract) |
| `evidence/kag010-evidence-pack.zip` | hash-chained audit log, ledger, both checkers, re-runnable offline |

The evidence pack is also public on Kaggle as a dataset:
<https://www.kaggle.com/datasets/robertpersinger/instrument-contract-evidence>

## Re-running the checkers

Unzip the evidence pack and run the two checkers it contains; they need only Python. They fail loudly on
malformed input, which is the point — a checker calibrated against its own generator is the failure this
paper is about.

Licensed Apache 2.0, like the dataset it descends from.
