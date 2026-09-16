---
title: "When the truth is in the code"
subtitle: "Spec-driven delivery in a migration, where the specification is recovered rather than written"
audience: "engineers facing a migration where no document describes the system. No prior reading needed."
description: "A migration starts from the opposite condition to a greenfield build: the system exists, no document describes it, and the code is the only authority. What that changes about the specification, and how to validate one you did not write."
---

**Spec-driven delivery assumes the specification comes first. A migration starts from the opposite
condition: the system exists, it has run for years, and what describes it is the code — which is
authoritative about *what* and silent about *why* — plus whatever the team carried in procedures and
in their heads.**

The case below is a single-sign-on system I migrated for a client: an extension of an open-source
SSO framework, several major versions behind current, on the Java release that came out in 2014.

| | |
|---|---|
| Hand-written Java | 168 files, six Maven modules, after nearly a decade of customisation |
| Coupling to the framework | 73 of those files import it; 33 extend an upstream class, across 31 distinct ones |
| Automated tests | 14 classes, about 7% line coverage — functional flows, not lines |
| Per-tenant configuration | The live values are tenant-private, in no repository I could read |

Nothing about that shape is unusual. It is what a product looks like after nearly a decade of
delivering what customers asked for — well run, by a team with real practices, none of which
happened to be machine-checkable. That is the normal starting condition for a migration.

## One layer already works the way the rest should

The persistence module is the smallest in the project: eight hand-written Java files, 137 lines.
Next to them sits a 1,414-line XML schema, and at build time that schema generates **354 classes** —
entities, beans, mappings, type definitions — none of which is checked in, read, or hand-edited.

That is the whole thesis of this site, shipped long before anyone involved was thinking about AI at
all. The schema is the artefact. The code is a build product. Delete the generated tree and nothing
is lost, because the description it came from is complete.

It is also, in this codebase, the **only** layer with that property. For the other 160 files there
is no description. The truth is the code, and nothing written beside it.

## What the code is authoritative about, and what it is not

Thirty-three classes extend an upstream framework class, and they extend **31 distinct ones**. That
distribution is the interesting part: the coupling is not one deep integration point, it is
thirty-one shallow ones — a ticket exception here, an access strategy there, a webflow configurer,
an OAuth configuration.

Each of those is a point where the real specification is the framework's internal contract at one
specific version. Not the documented API — the internals, as they behaved in that release. Nothing
in the repository records which of those behaviours are depended on deliberately and which were
inherited by accident. A major-version migration changes some unknown subset of the thirty-one, and
the code cannot tell you which ones mattered, because a subclass that relies on a superclass's
undocumented ordering looks exactly like one that does not.

Fourteen test classes across 168 files — about 7% line coverage — invites the wrong conclusion, so
here is the right one. This system was **stable**, and it was **tested**. The unit tests targeted
functional flows rather than lines, because coverage was never the goal. The framework underneath
was left untested on purpose: it is a widely used open-source project, tested by its community, and
re-testing it would have been work with no return. Beyond the unit tests sat manual test scripts
with a per-tenant variant of each, run against installations composed and installed by hand.

That was a coherent strategy, and the evidence it worked is in where the defects landed: in
integrations and in configuration, not in the custom code the tests covered.

So the specification was not missing. It was in the flows the unit tests asserted, in the manual
scripts, in the installation procedure, and in the people who ran them. What was missing was any of
it being **executable end to end** — and that is a different problem with a different cost.
Discipline expressed as human procedure works, and bills for itself every release, forever.

### What changed about the tests

Coverage later went to 89.9%, which looks like a verdict on the old practice and is not one. Nothing
changed about who tests the framework — still the community, upstream, exactly as before. What
changed is what unit tests are *for*: they used to assert functional flows through the custom code,
and they are now the net that catches what an agent disturbed three files away from the change you
asked for. That argument, and why exclusions become indefensible under it, is in [what I write down,
and when]({{ "/posts/what-i-write-down-and-when/" | relative_url }}).

The functional flows did not disappear when they left the unit tests. They moved to smoke scripts
run against a deployed system, which is where a flow belongs once it has to prove anything.

## The obvious first move, and what it actually produces

The obvious move now is to point an agent at the history and ask it to reconstruct what the system
does. I did. Out of a decade of commit history came a tidy document, several hundred lines,
organised by module: ACL types added, profile extensions, token handlers, theme work, language
support.

It is genuinely useful, and it is not a specification. It is an inventory of **changes**, which is a
different object:

- It records **intent, not outcome** — its source is commit messages, and a commit message is what
someone hoped they were doing. Lines like *"context assignment rewritten for better password reset
handling"* tell you a file changed and somebody was optimistic.
- It has **no edge cases, no invariants, no non-goals** — the three things an implementation
  actually has to honour.
- It **cannot distinguish a behaviour from a bug that shipped**, because both arrive as commits and
both are still running in production.
- It **flatters the codebase**, because failed approaches leave no trace in a summary of what was
added.

What it is good for is deciding where to dig. A map of where a decade of effort went is a decent
proxy for where the behaviour worth recovering is concentrated — in this case token handling and
tenant configuration, not the data layer. Treat it as a priority list for archaeology and it earns
its keep. Treat it as a requirements document and you have produced the most dangerous artefact in
this whole practice: a document that agrees with the code perfectly and therefore constrains
nothing.

## An extracted spec is a hypothesis, not a decision

The difference changes how the document has to be validated.

A greenfield specification is a **decision**. It is validated by review, because the people
reviewing it are the people with the authority to decide, and if they agree, it is right by
construction.

A specification extracted from a running system is a **hypothesis** — a claim about what the
software does, made by reading it. Review cannot validate that. The reviewers are working from the
same memory the extraction is trying to replace; their agreement is evidence about what the team
recalls, not about what the system does. The only validation that means anything is **execution
against the thing itself**.

| | Greenfield spec | Extracted spec |
|---|---|---|
| Nature | A decision | A claim about an existing system |
| Validated by | Review | Running it against the current build |
| Failure mode | Wrong thing built | Confident description of behaviour that isn't there |
| Written | Before the code | Before the *next* code |

Writing it after the code also changes what the document is. The vehicle for a recovered behaviour
turned out to be an ADR — not the familiar "we chose X over Y", but *this is what the system does
here, this is the context it was recovered from, and these are the consequences of keeping it*. Of
the 48 decision records the project produced, **32 were functional**: one recovered behaviour each.

That format earns its place because of the one thing it lets a later reader do — tell an intentional
decision apart from a defect. Thirty-three subclasses, reaching into thirty-one distinct upstream
classes, look identical in the source whether the behaviour they lean on was chosen or absorbed.
Written down with their context, they stop being identical.

A record is still only a claim until something runs it. That is what the end-to-end scenarios are
for: a recovered behaviour has to be asserted against a deployed system rather than a mocked one,
which is also what makes backward compatibility something verified continuously instead of asserted
in a status report.

And the acceptance criteria for a migration are the old behaviour, which forces a decision nobody
enjoys: bug-for-bug compatibility, per behaviour, explicitly. Not as a blanket policy in either
direction. Some of those inherited behaviours are load-bearing for a customer somewhere, and
finding out which ones is the actual project.

## The part you cannot read at all

Everything above assumes the archaeology has a subject: source you can point something at. The
hardest part of this project had no subject.

Defects landed in integrations and configuration. The configuration was tenant-specific and
tenant-private — not in the repository, not in any repository, and not available to me. Each tenant
is a theme, a set of message bundles and a stack of property files, and what is in the repository is
the default. What actually ran at a tenant was theirs, held by them, and the layer where the
behaviour differed was therefore the one layer that could not be read.

So the extraction had a hard boundary, and it sat exactly where the defects were. That inverts the
usual advice about tooling. No model, no context window and no amount of patience reaches a file
that is not there; the only instruments that work across that boundary are the ones that do not
require reading — a behaviour recovered by asking someone, by running the system, or by inferring it
from what the defaults have to be compatible with.

Stated as a rule, it survives whatever the tooling does next:

> **An extracted specification is bounded by what you can point the extraction at, and the
> boundary is invisible from inside the result.**

A document reconstructed from the readable 160 files will describe them accurately and say nothing
at all about the tenant configuration — not "unknown", not "gap", just silence in the shape of a
complete-looking document. Anything that reads it afterwards, human or otherwise, has no way to tell
the difference between a system that has no per-tenant behaviour and a system whose per-tenant
behaviour was unreadable. That distinction has to be written in by hand, by the one person who knew
to look for it.

## The question a customer actually asks

*"We have an old system. Twenty-odd modules, parts of it with no source any more, and it works. How
is AI supposed to help us?"*

It was put to me in a meeting and it was not really a question — it was a conclusion with a rising
tone at the end. I had five minutes, and I gave the safe half of the answer: recovering the
knowledge is worth something on its own. Refinement gets better once the team can state what the
system does. Confidence goes up, and confidence is what decides whether anyone dares to touch a
module nobody has opened in six years.

True, and it undersells it.

The fuller answer is that a system like that can be brought under end-to-end automation, including
the parts with no source. Those parts are unreadable in the same way the tenant configuration was,
and the instruments are the same: run it and observe, ask the people who operate it, infer the
contract from what everything around it has to stay compatible with. **What you cannot read, you can
still characterise.** A module with no source and a suite of tests describing what it does from the
outside is no longer a black box. It is a component with a recovered specification, and it can be
changed.

The room is usually asking something else, so it is worth answering the unasked question directly.
The fear is that this is a headcount argument, and that fear is not paranoid: organisations do cut,
and some of them will use this to do it.

But the safe position is not the one it looks like. Sitting on a twenty-year-old system until
somebody finally switches it off is not job security — it is a bet that the switch-off is further
away than your career. It rarely is. The switch-off tends to arrive sooner than the people working
on the system expect, and on that day what you are holding is expertise in a system that no longer
runs.

Starting the automation is the lower-risk move, and it is lower-risk for you, not only for the
organisation. You stop being the only copy of what the system does — which is the job that has been
keeping you from learning anything else — and what you pick up on the way is what transfers to
whatever comes next.

## What it cost, and what the defects were

Full scope in under four months — and the compression is the part that looks implausible from
outside. It did not come from generating the new code faster; that was never the expensive step. It
came from the archaeology: reading unfamiliar code at volume, reconciling it against live behaviour,
and challenging every gap. That work normally consumes the schedule before a line is written.

The migration's defects were not instability inherited from the legacy system, which was stable.
They came from incompatibilities with the new version's specification — the places where the
upstream contract had moved underneath a behaviour that had been relied on. The same thirty-one
superclasses from the start of this article, arriving as a bill.

The tenant configuration stayed where it was, per tenant, and produced no defects. The layer that
could not be read was not the layer that broke.

## What survives

The artefact is the same shape as in a greenfield build: context, scope and non-goals, interface,
behaviour, acceptance criteria, failure modes. What changes is where it comes from and what makes it
trustworthy — recovered rather than decided, and confirmed by execution rather than by agreement.

A migration is the only moment when anyone will fund the archaeology, which makes it the one
opportunity to convert a specification that lived in scripts and in people into one that executes.
If the migration finishes and the document does not survive it, the budget went on a version bump
and the code is the truth again — which is where you started, one major version later.

Here it survived: 32 functional records, and a suite that fails when any of them stops being true.
That, rather than the version number, is what the next decade will be built on.

*All figures in this article are counted from the legacy repository or measured on the delivered
project. The system, the client and the framework are deliberately unnamed.*
