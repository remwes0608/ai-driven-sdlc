---
layout: page
title: About
description: Who is writing this, what the current problem is, and what has actually been built with the answer so far.
toc: true
background: /img/bands/about-path.jpg
avatar: /img/portrait.jpg
avatar_alt: "Remigiusz Weska"
---

Twenty years of building and delivering software across medical systems, ERP, financial services,
energy and telecoms — telecoms the largest and the most recent of them. Identity and access
management, transaction processing, real-time platforms: systems other systems depend on, where
being wrong is expensive and visible.

I was writing games before I went to university and had a job in the industry while I was still
studying, and I have held most of the roles between writing the code and answering for it since. The
titles are the least interesting part; the roles taught one thing worth repeating. I have been the
engineer holding on to a process that worked right up until it stopped working, and years later the
one arguing with a colleague who was doing exactly that with the process which had replaced it.
Both times the person holding on was right about the method and wrong about the moment. It is not
a mistake you make once and then recognise for ever.

What I trust is what I measured and what I was accountable for. Most of what gets written about
AI-assisted delivery is neither, which is roughly why this site exists.

The last five years have been transformation work from both sides of the table: legacy estates onto
cloud-native, and the delivery process itself from Waterfall, through Agile, to building with AI
agents. I work from Poland, building and advising in roughly equal measure.

## Why AI has my attention

Because it is the largest lever currently available for turning an idea into a working thing, and
that is what has held me since I was a teenager writing games. The technology changes; the appetite
does not. I want to know how a thing is built, I learn whatever is new when it arrives, and I judge
it by what it produced rather than what it promised.

That appetite was not originally mine. Asimov, Lem and Clarke put it there, and less as stories than
as context. Their books came from people with access to a world I had no way into before university
— research, and the conversations that happen around it — and reading them was the closest I could
get to how that world thinks: what is worth asking, what can be taken as given, how far you can
extrapolate before it stops being interesting.

Some of them read less like fiction than like a dare. *This could be built — go and try.* And it
helped, at that age, to find that ideas I already had were also held by people with far more behind
them than I had. That is still why a new capability makes me want to try it rather than comment on
it.

*The Caves of Steel* is from 1953. Read it now and the interesting part is not the robot; it is the
negotiation about what the human is still for, and who is accountable when the pair of them gets it
wrong. That question is on my desk most weeks.

I also have a mathematical turn of mind and the training that goes with it, so a model looks to me
like probability, linear algebra and numerical methods — a tool with a shape, a cost and known
failure modes. The genre of *we have AGI and it will end the world* earns a smile, which is not the
same as not seeing risk. The risks I take seriously are duller and much closer: a decision nobody
made, reaching production quietly.

Automation is not optional if you intend to move at all. And like every tool before it, it serves
whatever it is pointed at — the tool itself is neither good nor evil.

## The problem this site is about

Generating a working implementation from a precise description of behaviour is now cheap. That one
change breaks conventions that were all shaped by it being expensive — when we review and what we
review, where the source of truth lives, what counts as done.

The uncomfortable part is not that AI writes bad code. Generated code is usually fine. It is that
**the implementer stopped absorbing ambiguity.** A person hitting an underspecified requirement
notices and resolves it, hundreds of times per feature, almost never writing any of it down. An
agent does not stop. It resolves the gap too, plausibly and silently, and the resolution reaches
production without ever having been a decision anyone made.

So the failure mode is not bad code. It is code that correctly implements something nobody
specified.

And the industry has no settled answer yet. There are several serious, independent proposals for
what to do about it, and they do not share a vocabulary — which is a phase software engineering has
been through before, with a known shape and a known ending.

## What I have actually built with this

Three situations, because a practice that only works in one of them is not a practice.

**A major-version migration** of an identity and access management platform, delivered in an agentic
model: every feature tracked one by one against the legacy source, every recovered behaviour written
down as a decision record, and a suite that fails when one of them stops being true. Here the
specification had to be recovered from a running system before anything could move.

**A greenfield product** run under AI SDLC from the first day, where agents generate the whole of it
from the specification — interface, API, data layer, IAM integrations — to telecoms standards for
maintainability, observability, scalability and security. Here the specification is the only source,
and every gap in it shows up immediately.

**Transformation, which is the hardest of the three.** Neither of those is where most teams actually
are. Most are running Agile, delivering, and adopting AI a piece at a time — no rewrite, no big
bang, nothing bet on a single day. To a manager that looks safer, and the first read is correct:
there is no date on which it can all go wrong.

The cost arrives later and quietly. An organisation delivering seriously already knows how to
explain what it ships — extra documentation, extra tests, the mapping from what was delivered back
to what was asked for. That works, and it is built by hand, every release, at full price. Without a
strategy for what the agent may decide, what gets written down and what a release has to prove,
incremental adoption speeds up the generating and leaves that bill exactly where it was. Rolled out
properly, the same automation produces the mapping too, at a fraction of the cost and the time.

Keep paying the manual price while that is on the table and it ends somewhere: a cost the
organisation can no longer justify against what the delivery is worth. It is a dead end reached
slowly, which is exactly what makes it hard to argue against early, while it still looks like
progress.

The clients stay unnamed. The figures behind all three are in the posts.

## What gets written up here, and what does not

Practice that survived contact with those projects: what to write down, when, what an agent may
decide on its own, and what has to be decided by someone accountable. Where an article claims
something works, it shows the numbers or the text that came out of it.

Not here: model comparisons and tool benchmarks, which date in weeks; prompt technique, because a
specification is not a prompt — it outlives the session, people review it, and it is versioned with
the code; and the autonomy debate, which is a real question and a different one. Everything here
assumes a human decides what gets built.

## Who it is for

Engineers and leads introducing this into work that has consequences — production systems, regulated
domains, code other teams depend on. The assumed reader has already generated a feature, seen it
work, and noticed that the process around it no longer quite fits.

If you are evaluating whether any of this applies to your organisation, or want to argue with a
figure, the links in the footer reach me.
