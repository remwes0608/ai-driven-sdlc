---
title: "New tools, same circle"
subtitle: "The tools are genuinely new. The loop they are running is one the industry has already been round once"
description: "The tools are genuinely new. The shape of what happens next is not: competing approaches, consolidation, maturity, rigidity, correction. Where agentic delivery sits on that loop, and what to build knowing how the last turn ended."
---

**"Forget the old process — we are building something new." It is 2026 and you hear this in every
second room, usually from people who are right about the tools and wrong about the shape of what
happens next.**

The tools genuinely are new. The loop they are running is not.

## What actually drives the loop

The phases are easy to name — competing approaches, consolidation, maturity, rigidity, correction,
competing approaches again. The interesting question is what turns it, and the answer is not
fashion. Each turn is a different answer to one question: **who pays for change, and how.**

**Waterfall assumed resources were the answer.** Behind schedule? Add engineers, re-align the plan,
deliver. That works until you scale, and complexity does not scale linearly with size — it
compounds. Twice the people is not twice the throughput; it is twice the people and four times the
conversations.

**The response was standardisation.** Everyone works the same way, with ISO as the common reference,
and the compounding is supposed to be contained. It did not hold, for a structural reason rather
than a failure of execution: every client runs a similar process in their own way, and ISO assesses
whether you comply with the process you declared — it does not make two organisations work alike.
Certification is not shared practice.

**What was left was continuous adaptation, so: Agile.** It did not solve the problem either, and
this is the part people still get wrong — the problem is not solvable. Continuous change is not a
defect in the environment to be engineered away. It is a property of the environment. What Agile did
was **distribute the cost of change differently**: smaller pieces, absorbed continuously by the team
rather than periodically by a re-planning cycle. That redistribution is what let delivery scale
again.

**Then the rate of change went up again.** A method that absorbs change in two-week increments
becomes the constraint when the environment moves faster than two weeks. Agile became the
bottleneck, in the same way and for the same reason as the standardised methodologies it replaced —
not because it was wrong, but because the thing it was built to absorb started arriving faster than
it could absorb it.

That is the engine, and nobody is stupid at any point in it.

Agentic delivery is at the top of a new turn, and the reason is the same: generation got cheap, so
the cost moved again. There are several serious, independent proposals for what to do about it —
[Spec Kit](https://github.com/github/spec-kit) covering specification and code,
[ai-sdlc](https://github.com/ai-sdlc-framework/ai-sdlc) covering execution and governance, Amazon's
Kiro doing its own version of both — and they do not share a vocabulary. Spec Kit renamed its own
commands inside a year.

That is what the first phase looks like from the inside: not chaos, just several good answers and no
agreed one.

## I have been both people in this story

**First time round, I was the one holding on.**

Phoenix was a mature delivery methodology built on ISO, and its strength was the opposite of what
people assume when they hear that. It was **lightweight, and genuinely used**. It had been designed
to be effective first and compliant as a consequence, rather than compliant first and effective if
there was time left — which is why people followed it instead of working around it.

I was a mid-level developer and it helped me in a way I have not seen anything replicate since: it
let me switch between customers without re-learning how to work. Everything was unified. Only the
code and the product were different.

Then a new CEO arrived and purged it, as far as I could tell without looking at it properly. From my
desk that was vandalism. What I could not see from my desk — and this is the part worth admitting —
was that the market had already changed, and he could see it.

We went into Agile dragging as much of the old methodology behind us as we could, to survive. Very
few people noticed what we were doing: **the old method was not a lifebuoy, it was a weight.** The
goal was never to preserve what worked before. It was to start swimming faster.

**Second time round, I was the other one.**

A few years ago a colleague gave me a book about scaling an organisation with agile methods —
generously, to show me how good this was and why we should be doing more of it. He was making
exactly the argument I would have made about Phoenix, with the same sincerity and the same evidence.

And the same thing had already happened. The market had moved again — not because of AI, which had
not arrived yet, but because of what customers had started demanding. AI turned up afterwards, into
a turn that was already underway.

I have made that mistake from one side and argued against it from the other, and both times the
person holding on was right about the method and wrong about the moment.

## What the new thing actually changed, and what it did not

**Changed, genuinely.** The implementer stopped absorbing ambiguity. A person hitting an
underspecified requirement notices and resolves it, hundreds of times per feature, almost never
writing any of it down. An agent does not stop — it resolves the gap too, plausibly and silently.
That is a real break with everything the old practice assumed, and it is why the old documents need
a different kind of precision rather than a revival.

**Did not change.** People still have to agree on what they are producing before they can work in
parallel. I ran delivery on a programme spanning Europe, North America, the Middle East and Asia —
different companies, time zones and working cultures — and what unlocked it was the programme
manager saying, more or less: *guys, we all know what an HLD is, and an IDD, and an IA. Use them.*

Nothing was invented in that sentence. He was pointing at knowledge every engineer in the room
already had and nobody had thought to invoke, and it worked immediately: people from different
organisations started talking to each other **at a different level** — not about who owned what and
who to ask, but about the content of the interfaces. The names did the coordinating, so the
conversation could be technical.

That is the mechanism standardisation-by-certification never had. Nobody adopted anyone else's
process and nobody was audited. They only had to agree on what the artefacts were called and what
belonged in them — a far smaller thing to agree on, and one that survives the parties working
differently in every other respect.

An agent is one more party arriving with no shared context, so the new-thing claim is half right:
new reader, same requirement — **a form everyone recognises.** And right now nobody can say *"let's
use X"* about agentic delivery and have the room nod.

## What to build, knowing how the turn ends

Consolidation is coming and it is worth having. On the previous run it took a couple of years to
produce a usable answer and another decade before the organisations that needed it most adopted it.
Waiting is not a strategy.

So derive your own from the practices that already work — they were not invalidated, only left with
a reader that cannot ask questions. But derive it knowing how the last turn ended, and build for
amendment rather than permanence:

- **Machine-readable and versioned with the code**, so changing it is a merge request rather than a
committee.
- **Decisions consolidated while they are draft and frozen once accepted**, so the record stays
small enough to read and stable enough to rely on. Append-only from the first keystroke sounds
disciplined and produces a chain of corrections nobody can follow.
- **Separable artefacts with stated non-goals**, so a piece that has stopped earning its place can
be dropped without reopening the rest.

The goal is not to guess the standard early. It is to adopt it cheaply when it arrives, and to let
go of it without a fight when it stops working — because the hard part is never recognising that
something worked. It is recognising that it has stopped, while it is still comfortable.

What that looks like on an ordinary Tuesday is the next piece: [what I write down, and when]({{
"/posts/what-i-write-down-and-when/" | relative_url }}).

---

*References: [Spec Kit](https://github.com/github/spec-kit) and
[ai-sdlc](https://github.com/ai-sdlc-framework/ai-sdlc) for the two halves currently being built;
[EARS](https://alistairmavin.com/ears/) for requirement syntax that predates all of this by fifteen
years and still works; [adr.github.io](https://adr.github.io/) for decision records. The life-cycle
standards behind the nineties consolidation are ISO/IEC/IEEE 12207 and MIL-STD-498, cited by
designation because publisher URLs rot.*
