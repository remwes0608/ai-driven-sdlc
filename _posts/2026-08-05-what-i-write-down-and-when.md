---
title: "What I write down, and when"
subtitle: "One pass through the life cycle — the artefact each phase owns, what it is allowed to decide, and how often it moves"
description: "The artefacts an AI-assisted project needs, walked through in order: vision, design, development, testing, delivery — what each phase owns and the cadence it runs at."
---

**One pass through the whole life cycle. Not a template guide and not a deep dive on any of it — the
artefact each phase owns, what it is allowed to decide, and how often it moves.**

Two things to say before the phases. These artefacts run at **different cadences**, and most process
pain is a cadence mismatch rather than a missing document. And the order below is the order
information is allowed to travel: each phase constrains the next, and nothing flows back up.

## Before the phases: the four files

`README.md`, `SKILLS.md`, `CLAUDE.md`, `AGENTS.md`. Every repository has some of these now. Two
things about them cost me something to learn.

**CLAUDE.md and AGENTS.md must not be updated automatically by the agent.** A file the agent
maintains is a file that agrees with whatever the agent already believes, which makes it useless as
a constraint at exactly the moment you need one.

A **skill** is an activity that helps do something specific. It says nothing about your software.
That distinction keeps the file small: I use three or four, not a catalogue. A skill nobody invokes
is a skill nobody maintains.

## 1 · Vision

Product vision and requirements are tracked separately, the way MSF had them. They answer different
questions, they move at different speeds, and collapsing them into one document costs you the
ability to say no — a backlog cannot refuse anything, a vision can.

Vision moves rarely and deliberately. Requirements move per release train.

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

The distinction predicts cadence. An HLD moves with the design. An IA moves when the relationship
between two parties changes, which is rare and usually needs signatures.

### Test scenarios, derived from the descriptions

From the feature description, never from the implementation. A scenario derived from the code cannot
fail; one derived from the description can, which is the entire point of having it.

## 3 · Development

### ADRs: decide everything, or the agent decides it for you

Project, module and library level decisions — technical, functional and non-functional. The list of
what has to be decided explicitly is longer than people expect:

- Version control, and the repository layout. Be prepared for several MRs a day: **a monorepo is
rarely the right answer** under that load.
- Base architecture and technology. Coding practices. SCM flows. SAST rules.
- Non-functional requirements: observability, performance, security.
- Unit testing rules **and their exclusions** — Lombok-style generated code being the usual case.
- How features get implemented.
- **The API, where there is one.** Define the specification from established standards rather than
letting it emerge. I used JSON:API for a long time and teams struggled to deliver every requirement
— but switching to bare OpenAPI and letting the developer or the agent decide freely is much worse.
Deciding the spec first turns API generation into a fully automated step.

Two rules around them. **ADRs are hierarchical** — project root, then subproject, then below. And
**do not leave any requirement unattended**: the agent will decide it otherwise, quietly and
plausibly. Better to tell it explicitly what it is allowed to decide.

On form: keep them human-first and clean. Sequence diagrams are fine. Block diagrams mislead more
often than they explain. Mermaid or draw.io both work, and recent models read diagrams far better
than they did a year ago. Do not shy away from decisions about the interface either — WCAG
conformance is a decision an agent can now check, because it can see and measure the UI.

There are several ADR conventions published; you will end up with your own. Mine grew one addition
that earns its keep: **automatic research into existing solutions, because reuse beats
implementation** and the research is now cheap.

**ADRs are not append-only**, which is the one place this practice parts company with every
published convention. While a decision is draft it gets consolidated — merged, amended, collapsed
into one clean statement. Once it is Approved or Accepted the strict rules take over: superseded
rather than edited, with the history kept.

The reasoning is worth more room than a survey can give it, so it has its own piece: [consolidate,
then freeze]({{ "/posts/consolidate-then-freeze/" | relative_url }}).

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

### Smoke scenarios, against something real

The scenarios written in design now run against a deployed system rather than a mocked one, at
browser and protocol level. That is where a functional flow belongs once it has to prove anything —
and it is a different job from the unit tests in development, which never leave the build.

### Coverage of requirements, as an artifact

Not line coverage — coverage of requirements, rendered as something a person can look at and argue
with. It is generated from the feature descriptions, the scenarios and the ADR-to-source mapping,
which is the only reason it stays true.

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
about evidence. Keep it in the CI artifact store, not in the repository: the repository holds what
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
artifact store.

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
