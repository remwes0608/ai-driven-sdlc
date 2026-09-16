---
title: "Consolidate, then freeze"
subtitle: "ADRs are not append-only, and treating them that way is what makes them unreadable"
audience: "anyone keeping decision records that an agent reads back. Assumes you know what an ADR is."
description: "Decision records get treated as append-only from the first keystroke. That is right after a decision is accepted and wrong before it — and with an agent reading the record continuously, wrong expensively."
---

**Architecture decision records get treated as append-only: never revise, always supersede. It was
the first rule I dropped, and dropping it is what made the records worth keeping.**

Nobody keeps a changelog comment block at the top of a function any more. We deleted them, moved the
history into version control, and nobody misses it.

Decision records are still waiting for that move.

What this is really about is keeping an agent pointed at the task instead of circling it: re-opening
settled questions, re-proposing what was already ruled out, spending a round to arrive where the last
one ended. That is what the append-only habit costs once something reads the record continuously,
and it is the thread running through everything below.

## What changed is the record's job

Two things moved at once, and the same cause moved both.

**The exploration became durable.** The conventions put immutability on `accepted` and gave the
earlier stage a status and no rules, because for fifteen years that stage left no trace: refinement
happened in someone's head, on a wiki page nobody linked, in a spike branch, in a meeting. It was
real work and it evaporated, which is what everyone expected it to do. An agent cannot work that
way — it carries nothing between sessions, so whatever has to survive has to be written down.

**And the record stopped being a reference.** In a team, an ADR is consulted. Occasionally, by
somebody with a specific question, who knows roughly where to look and skips the rest. With an agent
it is **active**: read whole, every turn, as input to what gets built.

That second change is the one that matters, and it is not more of the same reading. It is a
different kind of artefact. A reference is something you go to. An operational input is something
that is loaded before anything happens, whether or not it is relevant.

A log is a perfectly good thing to consult. It is the wrong thing to consume in full, continuously.

So a practice that worked in teams had to be simplified and merged — not because it was bad, but
because an artefact read occasionally and in part has different requirements from one read
constantly and in whole.

**The convention did not get worse. The artefact changed jobs.**

## A log, not a record

Look at what the collection actually is. This is the shape a conventional set takes after two years:

```
docs/adr/
  0001-record-architecture-decisions.md
  0002-choose-the-event-store.md          superseded by 0007
  0003-event-schema.md                    superseded by 0009
  ...
  0007-choose-the-event-store-again.md    superseded by 0014
  0009-event-schema-v2.md
  0014-event-store-final.md
```

Files numbered in sequence. Nothing is ever removed. Superseded entries stay, with a pointer
forward. Inside each one, the header carries the same idea:

```
# 7. Choose the event store

Status:  superseded by ADR-0014
Date:    2026-03-11
```

To know which event store you are using, you open `0002`, find it superseded, follow to `0007`,
find that superseded too, and land on `0014`. Three files to answer one question, and nothing tells
you in advance that the first two were going to be dead ends.

Those are the semantics of a **log**: immutable entries, chronological, current state derived by
replaying them. A **record** is the opposite — the current state, maintained, with the history kept
somewhere else.

The abbreviation expands to Architecture Decision *Record*. The mechanics are a log.

That was the right choice in 2011, and calling it a mistake would be reading today's conditions
backwards. The alternative then was writing nothing down. There were a dozen entries, and the
reader was a person, who reconstructs current state from a sequence without noticing they are doing
it.

**Two conditions have changed since: there are far more entries, and the reader can no longer skim.**

## Why a log is the dangerous shape

Every long project I have worked on had a risk log and a decision log. They fail in two ways, and
only one of them looks like failure.

**The dead one is the gentler failure.** Entries go there to be forgotten — still present, still
growing, read by nobody, and quietly cited as evidence that the process is being followed. It costs
its upkeep and returns nothing. That is bad, and it is bounded.

**The worse failure is a bad log that is actually used.** A log only grows, so the time needed to
get through it grows with it, while the time available does not. Put it on the agenda of a decision
meeting and within a few months the meeting is not deciding anything: it is walking the rows,
updating statuses, deferring, and adjourning. The artefact that was supposed to focus the discussion
has set it instead.

Nothing in the format pushes back, and this is the mechanism worth naming, because it is not
laziness.

**A record can be wrong. A log can only be incomplete.** If the record says the timeout is fifteen
minutes and it is thirty, somebody is wrong and can be told. If the log is missing last quarter,
nobody is wrong — appending is always correct and never urgent, and removing is not an operation
anyone is asked to perform. So it grows without a brake and decays without a culprit.

By the time anyone notices, either the artefact is dead or the meeting is.

**An agent reading an append-only decision record is in that meeting, every turn.** It walks the
rows, it cannot defer, and it has no way to decide that a row stopped mattering.

**An ADR is not the place for that.** It is the one artefact whose entire job is to be read before
you build.

## Two stages, two sets of rules

The stage decides the rules, and there are only two.

**While a decision is draft**, records get consolidated. Merged where they duplicate, amended where
they are wrong, collapsed into one clean statement. The amendments are deliberately lightweight —
the goal is a set that is small and true, not an account of how it got that way.

**Once a decision is `Approved` or `Accepted`**, the strict rules take over. It is not edited in
place. It is superseded, the supersession is written into the record that replaces it, and git keeps
the rest — because by now something downstream depends on it and someone is entitled to know it
moved.

The dividing line is not the calendar. It is not the author's confidence either.

**It is whether anything else relies on it yet** — which is the same line the conventions already
draw, extended to cover the stage they named and never governed.

## Why the draft stage needs the freedom

The drafts outnumber the decisions, and the gap only widens.

A decision reaches acceptance through refinement. It is rejected, often more than once. Sometimes
proof-of-concept code gets written purely to harden the scope enough to argue about it properly. Say
ten decisions arrive through forty superseded drafts: the forty tell you about the meetings, the ten
tell you about the system.

There is a second reason, sharper, and it only appears once an agent is reading.

An early decision that cannot be corrected in place spawns a chain of superseding records, and
everything downstream reads the whole chain as context. So the cost of a wrong first answer is not
paid once when it is corrected — it is paid on every subsequent read, by every reader, for as long
as the project lasts.

**The wrong first answer does not get removed. It gets outvoted, repeatedly, in a document the agent
keeps reading.**

## What consolidation may not delete

Consolidation without a limit is just deletion, and the limit is sharper than it looks.

**Remove the trail of how the decision was reached.** The three drafts that said nearly the same
thing, the scope that was wrong for a fortnight, the wording nobody liked. None of it constrains
anything now.

**Keep every rejection, with its reason.** The rejection survived when append-only did not, and it
is the one thing I would defend hardest.

An option that was ruled out and then disappears from the record is an option that comes back. The
agent has no memory of the argument, so it proposes the same approach next week, and the round is
spent re-deriving a conclusion somebody already reached — which is the circling this whole practice
exists to prevent. A person does the same thing, more slowly and with a vaguer feeling that they
have been here before.

The distinction is between process and content. *How we got here* is process, and it is disposable.
*What we ruled out and why* is a constraint on everything built afterwards, which makes it part of
the decision rather than part of its history.

What is left afterwards is two kinds of statement, and both are final.

**This is what holds** — one current statement per decision, with no earlier versions to reconcile
against it.

**This was ruled out, and why** — a boundary, not a story.

For an agent that is close to binary, and the closeness is the whole benefit. Nothing has to be
weighed, dated, or resolved against something that contradicts it three files later. A log asks its
reader to *infer* the current state by replaying the sequence. A consolidated record asks it to
*read*. Inference is where a turn spends its budget, and it is where a turn goes wrong.

Both of those are structural rather than a matter of discipline, which is the only way they survive
contact with a deadline. The set I keep for my own product has them as sections:

```
# A012. OIDC/OAuth2-only identity, static role-policy files,
#       and individual entity ownership

**Status:**  Accepted
**Created:** 2026-08-09
**Updated:** 2026-08-10

## Table of contents
## Context
## Existing solutions
## Decision
## Rejected alternatives
## Consequences
## Open topics and TODOs
## Related files
## Amendments
```

`Rejected alternatives` is the guardrail given a heading. It sits inside the decision it constrains,
not in a separate file that has to be found, and it is not something a consolidation pass can
quietly absorb.

`Amendments` is the other half, and it is deliberately thin — a table rather than prose. Two rows
from two other records in the set, verbatim:

| Date | Evaluated | Result |
| --- | --- | --- |
| 2026-08-09 | Superseded and deleted A007 (Redpanda as the event-bus transport) — Redpanda's core broker license (Business Source License 1.1, four-year delayed conversion, plus a separate proprietary Community License) was found non-compliant with A009's permissive-license policy | Superseded — deleted |
| 2026-08-16 | How an interim permission check must behave before this record's enforcement layer exists — `heimdall-api`'s first attempt read the absence of any role assignment as a denial and locked out every authenticated caller | Included — added §8 |

The first row is what stops the circling, in its plainest form, and it only works because it is
specific.
Redpanda is a reasonable thing to propose: it is fast, it is well regarded, and an agent weighing
transports next month arrives at it again on the merits. What stops it is not a prohibition but an
answer given before the question — and one that can be checked, because it names the licence, the
clause that matters, and the record it conflicts with.

Generalise that row and it dies. *A candidate broker whose licence failed our policy* forbids
nothing, because nothing in it can be matched against a proposal. The specificity is the mechanism,
not decoration on it.

Note what the record no longer contains. The superseded decision was **deleted**, not left in place
with a pointer; the reason it was rejected moved into one line of the record that replaced it. The
boundary survived and the file did not.

The `Result` column carries where it landed, not just whether it was taken — *added §8*,
*superseded and deleted*, *amended §1*. That is what makes the table usable a year later: each row
is a decision with an address.

That is why the amendments stay light. A row carries what was evaluated, the boundary it produced
and where it landed. It does not carry the old wording or the diff, because an amendment that quotes
what it replaced has quietly put the reader back in the business of reconstructing. The amendment is
a means; the only thing meant to survive it is the statement it produced.

**Consolidation deletes the path. It never deletes a boundary.**

## You are already keeping the log twice

Git is a log. It has the operations, the ordering, the authorship and the timestamps, and it is
queryable. The ADR collection, kept append-only, is a second log of the same events — hand-written,
not queryable, and mixed into the text somebody has to read in order to build.

So the split is not new work. It is deleting the duplicate. Two questions, two artefacts — and the
mistake is trying to serve both from one document, which is what append-only-from-the-first-keystroke
quietly asks you to do.

- **The consolidated ADR is what gets read to build.** Current, clean, no operations visible.
- **The history of how it got that way is in git.** Complete, auditable, and out of the way.

This is how every developer already works with source code. You read the current file. You reach for
`git log` or `git blame` only when the question is *why this line* — which is the changelog comment
block, deleted and not missed.

**Keeping two logs and no record is the actual state most teams are in. One of the two is worse, and
it is the one people are asked to read.**

## Focus is the point

Every superseded clause you keep is spending somebody's attention, and after the decision is
reversed it is spending it on the version that turned out to be wrong.

Purging the trash and keeping the story short is **focus**, and focus is what makes an agent
effective. Everything in the record competes for context budget with everything else, including the
parts that were later overturned.

This is not only an agent's problem, and framing it that way concedes too much. A person reading a
decision record threaded with superseded clauses is doing the same wasteful work — slower, and with
more irritation.

**The record is not free to keep. It is paid for in the attention of whoever reads it next.**

## If you are reaching for Spec Kit instead

The same question arrives one level up: adopt decision records, or let a spec-driven toolkit carry
this for you? It is worth checking whether the toolkit has the same problem. It does not. It has the
opposite one.

**Spec Kit's constitution is consolidated by construction.** The command that amends it writes the
whole file back — *overwrite*, in the template's own word — and the change summary it generates, the
Sync Impact Report, goes in as an HTML comment that is *"expected to be removed before the amended
constitution file is committed"*. The document carries a semantic version rather than a history:
major for a principle removed or redefined, minor for one added, patch for wording.

That is the argument of this post, arrived at independently and by a different route. Current state
in the artefact, provenance somewhere else.

**What it does not carry is the boundary.** There are five templates — checklist, constitution,
plan, spec, tasks — and not one of them is for decisions or research. The plan names `research.md`
as a Phase 0 output without saying what belongs in it. The single place a rejected option is
recorded is Complexity Tracking, which is filled in only when a Constitution Check has been
violated, and asks why the simpler approach was rejected. That is one narrow slot for one kind of
rejection.

So the two traditions fail in opposite directions, and knowing which way yours fails is most of the
work:

- The ADR conventions **keep everything**. The boundary survives, buried in a log somebody has to
  replay to find it.
- Spec Kit **keeps the current state**. It is readable on arrival, and the rejections evaporate.

**Neither is wrong, and neither is complete. Whichever you adopt, the half it does not do is the
half you supply.** With records, that means consolidating the path. With a toolkit that already
consolidates, it means finding somewhere durable to put *what we ruled out and why* — because
nothing in it is going to ask you for that, and the agent will keep proposing the thing you
already turned down.

Consolidate while it is yours, freeze when it becomes everyone's. And if that feels like losing
history, check where you were planning to keep it.

It is already in git.
