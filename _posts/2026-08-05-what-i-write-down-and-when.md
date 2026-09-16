---
title: "What I write down, and when"
subtitle: "The documents an AI-assisted project actually needs, and the speed each one is allowed to move at"
audience: "leads and architects setting the process up. The lightest entry point here; assumes only a delivery life cycle."
description: "The artefacts an AI-assisted project needs, walked through in order: vision, design, development, testing, delivery — what each phase owns and the cadence it runs at."
---

**Most process pain is a cadence mismatch, not a missing document. One pass through the whole life
cycle, phase by phase: the artefact each one owns, what it is allowed to decide, and how often it
moves.**

Two things to say before the phases. The cadence claim is the one the post rests on: these artefacts
run at **different speeds**, and the table at the end puts them side by side. And the order of the
phases is the order information is allowed to travel: each phase constrains the next, and nothing
flows back up.

## The agent's own files

`CLAUDE.md`, the rules beside it and a `SKILL.md` per skill are the one part of this that is about
configuring the agent rather than shipping software, and they have a post of their own: [why the
agent seems scatterbrained]({{ "/posts/why-the-agent-seems-scatterbrained/" | relative_url }}). Two
things from it that the phases depend on. The agent never maintains its own instruction file,
because a file the agent writes agrees with whatever it already believes. And a skill is an activity
that helps do something specific, not a description of your software — I use three or four, not a
catalogue.

## 1 · Vision

Product vision and requirements are tracked separately, the way Microsoft's Solutions Framework had
them. They answer different questions, they move at different speeds, and collapsing them into one
document costs you the ability to say no — a backlog cannot refuse anything, a vision can.

Vision moves rarely and deliberately. Requirements move per release train.

**And none of this depends on which agent you use.** The agent's instruction files are a product's
furniture and they churn; a vision does not, which is most of why it is worth keeping in a form you
can point at.

It earns its place by being consulted, and the moment to consult it is **before a decision
changes** — not while writing it. A proposed change the vision does not cover is either out of
scope, or a sign that the vision moved and nobody wrote it down. Both are worth knowing before the
change lands, and neither is visible from inside the change itself.

**The most common failure here, and the earliest warning a project gives: the product vision is
written as a feature description.** Usually a good one — detailed, agreed, signed off, and useless
in the role.

When that surfaces it reads as a scope problem, or a stakeholder problem, and it is neither. The
*why* was never settled, and every artefact after it inherited the gap. **Which is what makes it
worth checking on day one: it is visible before anything has been built, and it never gets cheaper
to fix.**

## 2 · Design

Two things get written here, and one gets derived from them.

### Feature descriptions, with no technology in them

Detailed enough to build from, containing nothing about *how*. The moment a description names a
framework it stops being a statement of what the product does and becomes a design nobody reviewed.

**Never regenerated from the implementation** — nor from the ADRs that followed it. A description
rebuilt downstream describes what was built, including its defects, and then agrees with it forever.
The direction of flow is the whole rule: descriptions constrain code, never the reverse.

The opposite direction is where an agent earns its place, and it is the part people skip.
**Challenging the description before anything is built** — against the rules you have written down —
finds the case with no stated outcome, the two paragraphs that quietly contradict each other, the
criterion that cannot fail. That is a different operation from rewriting, and it is the one that
changed how much a description is worth.

### Interface artefacts, at the boundary

**IDD** (Interface Design Description) and **IA** (Interface Agreement) sit next to the life cycle
rather than inside it: what crosses the boundary, in what shape, and who owns which side. HLD and
LLD are the inside-the-SDLC pair, describing how the system is built.

The distinction predicts cadence. An HLD moves with the design. An IDD or an IA moves when the
relationship between two parties changes, which is rare — and for the IA, usually needs signatures.

**The reason to write them early is not the one the names suggest.** They sound like integration
paperwork — documents two parties sign somewhere near a delivery date — and read that way they can
wait until there is something to integrate.

They cannot, because an interface that exists early stops being a document and starts being an
input. Mocks get generated from it instead of hand-written against a guess. Smoke scenarios can be
written before either side is built. Two teams work in parallel against the same shape rather than
against each other's assumptions.

**So the payoff lands in development, not at integration** — which is the opposite of where the
name puts it, and the reason the artefact keeps being scheduled late.

### Test scenarios, derived from the descriptions

From the feature description, never from the implementation. A scenario derived from the code cannot
fail; one derived from the description can, which is the entire point of having it.

**With an agent it is worse than that.** When a scenario derived from the description does
eventually fail, the agent may well fix it — the test is the artefact in front of it, and making the
test agree with the code is the smallest available change. It may go a step further and update the
description, or the decision behind it, so those agree too.

Nothing about that repeats, which is what makes it hard to catch. Each step is one small
reconciliation in one direction, and when the last artefact has been brought into line with the
implementation, nothing in the project can contradict it any more — the defect has become the
specification.

It is the same failure as regenerating a description from the code, arriving through a door nobody
guards. **The rule is the same in both cases: knowledge flows one way, and a test is not an
exception to it.**

## 3 · Development

### ADRs: decide everything, or the agent decides it for you

Project, module and library level decisions — technical, functional and non-functional. The list of
what has to be decided explicitly is longer than people expect:

- Version control, and the repository layout — `adr/A*`.
- Base architecture and technology — `adr/A*`.
- Coding practices. SCM flows. SAST rules — `adr/A*`, and a rules file the agent loads.
- Non-functional requirements: observability, performance, security — `adr/N*`, or `adr/S*` if you
keep fewer categories.
- Unit testing rules **and their exclusions**, generated code being the usual case — `adr/A*`.
- **The API, where there is one** — decided in `adr/A*`, specified in `spec/`.
- How features get implemented — `adr/F*`, with the descriptions themselves in `features/`.
- The test environment and what the agent is allowed to touch — a **skill**, not an ADR.

**Those prefixes are a departure from the published conventions, and they cost nothing.** A letter
before the number and a separate sequence per category: `A001`, `F001`, `S001`. Chronology survives
inside each group, the records cluster themselves without anybody maintaining an index, and `adr/A*`
becomes a readable subset rather than a search. Mine are Architecture, Feature and System; the
letters are a per-project choice, not a standard.

It is also not a private invention. [MADR's own decision on
categories](https://adr.github.io/madr/decisions/0010-support-categories.html) weighed seven ways of
doing this and this is their option four — the category encoded in the filename — with per-category
sequences added. MADR picked subfolders instead, at the same trade-off: identifiers unique within a
category rather than across the repository. The prefix wins here for one reason, which is that it
survives `ls`, `grep -r` and a filename pasted into a chat window, and those are how these records
actually get read.

**Some of those live in two places, and that is not duplication.** A coding practice, an SCM flow
or a SAST rule is a decision, so it belongs in a record — and it is also something the agent has to
obey while it edits, which a record read once at the start does not achieve. The ADR carries the
decision and the reasoning; the rules file carries the operative line, scoped to the files it
applies to. Anything a linter or a formatter can enforce belongs to neither: put it in the tool and
let the tool be the one that is always right.

One of them is worth more room than a list gives.

**Take the API specification from an established standard** rather than letting it emerge. I used
JSON:API for a long time and teams struggled to deliver every requirement — but switching to bare
OpenAPI and letting the developer or the agent decide freely is much worse. Deciding the spec first
turns API generation into a fully automated step.

**Where that specification lives is a separate decision, and the answer is not "in the agent's
context".** It sits in `spec/` and stays out of the prompt. What reaches the agent is a reference
from a rule, pointed at the part that governs the decision or the scenario actually in front of it.

The distinction only shows up at scale, which is why it gets skipped. JSON Schema or JSON:API are
small enough that loading them costs nothing and the question never comes up. TM Forum's API
guidelines, or a 3GPP protocol specification, are a different order of thing — and turning one of
those into something an agent can use is a task in its own right, not a preprocessing step you slot
in before the real work.

There are projects that convert specifications into skills wholesale. What I have seen of that has
not worked, and the mechanics are against it: a skill's body enters the conversation on first use
and stays for the rest of it. **A standard is not a procedure you run. It is a reference you consult
at one specific point**, and anything that keeps it permanently in front of the model is charging
every turn that did not need it.

Two rules around the records themselves. **ADRs are hierarchical** — project root, then subproject,
then below. And **do not leave any requirement unattended**: the agent will decide it otherwise,
quietly and plausibly. Better to tell it explicitly what it is allowed to decide.

On form: keep them human-first and clean. Sequence diagrams are fine. Block diagrams mislead more
often than they explain. Mermaid or draw.io both work, and recent models read diagrams far better
than they did a year ago. Do not shy away from decisions about the interface either — WCAG
conformance is a decision an agent can now check, because it can see and measure the UI.

There are several ADR conventions published; you will end up with your own. Besides the category
prefixes, mine grew one more addition that earns its keep: **automatic research into existing
solutions, because reuse beats implementation** and the research is now cheap.

**ADRs are not append-only from the first keystroke.** A draft gets consolidated; once it is
`Approved` or `Accepted` the strict rules take over. Why that is a departure from the conventions
rather than a contradiction of them takes a post of its own: [consolidate, then
freeze]({{ "/posts/consolidate-then-freeze/" | relative_url }}).

### The generated ADR-to-source mapping

Interim files mapping decisions to the code that implements them, generated rather than written. The
ADR itself may decide class structures or patterns — it must not contain code or environment
details, or it stops being readable by the people who need it most.

### Unit tests, as the net under generated change

The agent writes most of them and runs them every turn. The reason to have them is not the one
people assume.

An agent's edit has a blast radius that is invisible in the diff it hands you: the change you asked
for reads correctly, and the thing it disturbed three files away does not appear at all. It breaks
something elsewhere on essentially every iteration. The cheapest detector is a test that already
existed and now fails.

So **coverage stops being a quality metric and becomes sensor density** — how much of the system is
in a position to report damage. Which is why exclusions are indefensible here: an excluded package
is a blind spot exactly where something may be editing unwatched.

The claim that testing AI-written code is pointless has it backwards. The tests are not checking the
model's work on the line it just wrote — reading the diff does that.

## 4 · Testing

### Environments the agent can drive

Automatic environments, via testcontainers. Kubernetes if you can, Docker Compose if you cannot; a
raw host works too, with more pain.

**Sandbox properly**: a dedicated system account for the agent, and dedicated accounts in databases
and third-party systems. Kubernetes earns its weight here for a reason people underrate — you can
trace service interaction, eBPF events, and everything else DevSecOps will want later.

Define the test environment and the agent's permissions in a **skill**, so the boundary is written
down rather than remembered.

**And be careful what is sitting on the disk it can read.** The models have got noticeably better at
refusing the obvious things, and that improvement does not cover the two cases that actually happen.
An agent working across your home directory finds credentials that were never meant for it — not by
hunting for them, simply by reading what is there. And no safety rule catches you pointing it at the
wrong environment: *clean up the database* is a legitimate instruction, correctly executed, against
production.

The second one is not a model problem, which is why hardening the model does not address it. The
sandbox is what stands between a mistyped target and a real system, so it is worth the layers that
implies: separate accounts, separate credential storage, and no path from the workspace to anything
you would mind losing.

### Smoke scenarios, against something real

The scenarios written in design now run against a deployed system rather than a mocked one, at
browser and protocol level. That is where a functional flow belongs once it has to prove anything —
and it is a different job from the unit tests in development, which never leave the build.

### Coverage of requirements, as an artefact

The other coverage, and it answers a different question. Development measures how much of the code
is watched; this measures how much of what was asked for can be shown to work — rendered as
something a person can look at and argue with, and generated from the feature descriptions, the
scenarios and the ADR-to-source mapping, which is the only reason it stays true.

## 5 · Delivery

### Deployment: the agent in place of a CD tool

Helm and the agent. The agent prepares the environment, deploys, runs the scenarios and produces the
evidence — the same sequence a CD product runs, at a fraction of the cost.

That is the argument, and it is narrower than it sounds. CD tooling was not wrong. What it charged
for was scheduling, glue, and knowing the state of what is deployed where — real work, competently
done, and now work you can ask for directly instead of buying.

**Configuration management is where it stops being a substitution and starts being a gain.** The
agent does not only apply a configuration. It verifies what is actually deployed where, reconciles
that against the repository, and keeps the repository current. Drift stops being something you
discover during an incident and becomes something you can ask about on any ordinary afternoon.

### Compliance, evidence, and known-broken things

Compliance checks can be fully automated, but only against specifications precise enough to check
against. Without that, what passes the gate is a person reading and judging. That works — it is how
compliance has always been done — and no tooling in front of it changes what it costs.

Known problems — CVEs, dependencies — belong in an **index the agent reads as an input**, not in a
report a human reads afterwards.

And every gate leaves an **artefact rather than a status**. Pass/fail is the least durable thing a
check knows. Trivy will scan whatever you point it at; the real questions are which artefact, at
which moment, and what you keep. Cosign or Notary for signatures, because compliance is a question
about evidence. Keep it in the CI artefact store, not in the repository: the repository holds what
was authored, the store holds what a run produced.

### The software card, and everything after release

One file combining release notes, evidence, environment and software information — generated, never
hand-written, and loadable into a GraphRAG. It answers questions that cross artefact boundaries:
*which release introduced this dependency, into which environment, and what did the gate say about
it at the time?*

It has two readers. At release, someone deciding whether to accept it. At three in the morning,
someone who did not build it — increasingly an agent acting on it. The old HOWTO and runbook rotted
because they were written by hand, separately from the thing they described, and nothing failed when
they drifted.

## What the folders look like

```
adr/          decisions, hierarchical
spec/         may be git-linked from a separate repository
features/     may be git-linked from a separate repository
tests/        smoke test scenarios
```

No folder for evidence or deliverables — the right place to generate and keep those is the CI
artefact store.

## The cadences, together

| Artefact | Moves |
|---|---|
| Product vision | Rarely, deliberately |
| Requirements | Per release train |
| Feature description | Per feature |
| IDD / IA | When the relationship between parties changes |
| ADRs | Consolidated freely while draft; superseded, never edited, once accepted |
| Code and tests | Many times a day |
| Release evidence | Per release, immutable |
| Software card | Per release, read at 3am |

Treat any one of them at the wrong speed and it fails in a predictable way: a vision rewritten
quarterly constrains nothing, an ADR treated like a ticket loses the *why*, a hand-maintained
software card is stale on the first ship.

If there is a single rule under all of this, it is the one from the ADR section: **do not leave a
requirement unattended.** Everything above is machinery for making sure the decision gets made by
someone who is accountable for it.

All of it also assumes you are building something new, where the description can exist before the
code. When the system already runs and nobody wrote any of this down, the same practice has to be
run backwards — and that is a different problem, with a different failure mode: [when the truth is
in the code]({{ "/posts/when-the-truth-is-in-the-code/" | relative_url }}).
