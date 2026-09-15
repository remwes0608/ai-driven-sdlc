---
title: "Consolidate, then freeze"
subtitle: "ADRs are not append-only, and treating them that way is what makes them unreadable"
description: "Every ADR convention says never revise, always supersede. That rule is right after acceptance and wrong before it — and with an agent reading the record, wrong expensively."
---

**Every convention for architecture decision records says the same thing: never revise, always
supersede. It was the first rule I dropped, and dropping it is what made the records worth
keeping.**

The rule is not wrong. It is applied at the wrong time — and with an agent reading the record, being
wrong about this is expensive in a way it was not before.

## What the record is for

An ADR answers *why*. That is the whole job, and it is the one thing the code cannot do: an
implementation states what happens with perfect fidelity and says nothing about what was rejected,
what constrained the choice, or what would have to change for the decision to be revisited.

So the record is only worth keeping if someone reads it, and everything below follows from taking
that seriously.

## Two stages, two sets of rules

**While a decision is draft**, records get consolidated. Merged where they duplicate, amended where
they are wrong, collapsed into one clean statement. The amendments are deliberately lightweight —
the goal is a set that is small and true, not a documented account of how it got that way.

**Once a decision is Approved or Accepted**, the strict rules take over. It is not edited in place.
It is superseded, the history is kept, and the supersession is explicit — because by now something
downstream depends on it, and someone is entitled to know it moved.

The dividing line is not the calendar or the author's confidence. It is **whether anything else
relies on it yet**.

## Why the draft stage needs the freedom

Look at how a decision actually reaches acceptance. It goes through refinement. It is rejected,
often more than once. Sometimes proof-of-concept code gets written purely to harden the scope enough
to argue about it properly. All of that is real work, and almost none of it is worth carrying
forward.

The record of *how we got here* is weight, and it grows far faster than the set of decisions it
explains. Ten decisions might arrive through forty superseded drafts. The forty tell you about the
meeting; the ten tell you about the system.

There is a second, sharper reason, and it only appears once an agent is in the loop. **An early
decision that cannot be corrected in place spawns a chain of superseding records, and everything
downstream reads the whole chain as context.** The wrong first answer does not get removed; it gets
outvoted, repeatedly, in a document the agent keeps reading. That is a mistake-loop, and it
compounds quietly: each turn spends more of its attention on the history of an error than on the
decision that replaced it.

## The precedent is legislative, not technical

Borrow the argument from a field with no freedom to be casual about this.

Legislative drafting records every amendment as an operation — deleted, added, replaced. Tracking is
a legal obligation, not a preference, and the result is exactly what an audit requires and exactly
what nobody can read. So the end product is a **consolidated text**: the law as it now stands, with
none of the operations visible.

The amendment history is not destroyed. It lives somewhere else, and you go there when that is the
question you actually have.

Sometimes you need one and sometimes the other. **The mistake is trying to serve both from a single
document** — which is what append-only-from-the-first-keystroke quietly asks you to do.

## We already have the second half

The split costs nothing to implement, because version control is the amendment history and always
was:

- **The consolidated ADR is what gets read to build.** Current, clean, no operations visible.
- **The history of how it got that way is in git.** Complete, auditable, and out of the way.

An agent troubleshooting goes to the second one. An agent building reads the first. This is exactly
how every developer already works with source code: you read the current file, and you reach for
`git log` or `git blame` only when the question is *why this line*. Nobody keeps a changelog comment
block at the top of a function any more, and nobody misses it.

## Focus is the point

Purging the trash and keeping the story short is **focus**, and focus is what makes an agent
effective. Everything in the record competes for context budget with everything else, including the
parts that turned out to be wrong — and an agent cannot skim past a bad paragraph the way a person
can.

But this is not only an agent's problem, and framing it that way concedes too much. A person reading
a decision record threaded with superseded clauses is doing the same wasteful work. Slower, and with
more irritation.

## Where this parts company with the templates

The published conventions are not wrong; they were written for a different reader and a different
pace. They give a proposal a status and say very little about what you may do to a proposal while it
is still one, because when a decision took three weeks to ratify and was read by six people, the
draft stage was short and the cost of carrying its debris was low.

Neither of those holds now. Decisions arrive faster, the record is read continuously rather than
occasionally, and the reader cannot skip. The template survives; the assumption underneath it does
not.

So: consolidate while it is yours, freeze when it becomes everyone's. And if that feels like losing
history, check where you were planning to keep it. It is already in git.
