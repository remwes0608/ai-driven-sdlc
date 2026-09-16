---
title: "Why the agent seems scatterbrained"
subtitle: "A popular test measures the wrong thing, and the file that would have passed it"
audience: "anyone whose agent keeps dropping an instruction, whose instruction files have quietly tripled, or who works across more than one machine. No prior reading needed."
description: "Developers type a rule at the start of a session, watch the agent drop it, and conclude the tool forgets. The observation is right and the inference is wrong — and the fix is upstream of every prompt."
---

**A developer opens a session and types a standing rule. The one I keep meeting is *address me
formally* — trivial, unambiguous, easy to check. It holds for a while. Then it stops holding, and
the conclusion is drawn: the thing forgets, so it cannot be trusted with anything that matters.**

The observation is correct. The behaviour really does lapse. The test is cheap, repeatable and
needs no tooling, which is why it spreads.

The subject underneath is keeping an agent pointed at the task instead of losing the thread:
re-opening settled questions, dropping a constraint it had ten minutes ago, working from a version
of your setup that stopped being true last quarter. That is not forgetfulness, and treating it as
forgetfulness is what keeps it unfixed.

**And that is the easy version, because it assumes one machine.** Mine is several workstations and a
laptop, and a session does not fly from one to the other by itself. Neither does anything the agent
picked up along the way, nor the file you fixed on Tuesday on the other desk. Every answer below has
a second half: making it true in more than one place.

## The answer I reached for first was the wrong one

My reflex was to explain the mechanism. Context window, prefill, what a turn costs, why the earliest
message is the first thing to go when the window fills.

I had been building an agent from the ground up, so that machinery was what I had been living in for
months. **Knowing more about it made my answer worse.** Everything I said was true and it answered a
question nobody had asked.

The useful answer is smaller and duller. A message expires; a file does not. The rule was typed into
a conversation, so it had the lifetime of a conversation.

**Nothing was forgotten, because nothing was ever stored.**

## Why this is not a matter of configuring a tool

I built the context assembly myself — what to keep, what to drop, what to re-read, what has to
survive a turn. Starting from that end, the design I arrived at is closely analogous to the one
Claude Code uses: something always present, something fetched on demand, something scoped to where
it applies, something written by the person and something written by the assistant.

That convergence is the interesting part. Two starting points, similar answers, which suggests the
shape is forced by the problem rather than chosen by a vendor. The same claim, reached from the
inside, is on the [other blog's front
page](https://remwes0608.github.io/ai-agents-field-notes/): *the context builder decides what a turn
costs. Nothing reaches the model that it has not assembled.*

So what follows is not "how to configure a product". It is the set of questions any agent has to
answer about its own context, in the form one of them happens to give them.

## Which file answers which question

The rows worth your time are the counter-intuitive ones. A model can generate a table of file
locations; what it cannot generate is the list of things people get wrong about them.

| File | Where | When it enters context |
|---|---|---|
| `CLAUDE.md` | user, project, and every directory above the working one | every session, at launch |
| `CLAUDE.local.md` | project root, gitignored | every session, after `CLAUDE.md` |
| `AGENTS.md` | project root | **never on its own** |
| `MEMORY.md` + topic files | per-project memory directory | index every session, topics on demand |
| `SKILL.md` | `~/.claude/skills/<name>/` or `.claude/skills/<name>/` | description always, body on first use |
| `.claude/rules/*.md` | user or project | at launch, or when a matching file is read |
| `.claude/agents/*.md` | user or project | description always, body when that agent runs |

Seven things that are not obvious from that table and cost something to learn:

**`AGENTS.md` is a convention, not a guarantee.** It is the cross-tool name and the tools do not all
honour it. Claude Code reads `CLAUDE.md` and the rules files beside it, and ignores `AGENTS.md`
unless you point at it — a one-line `CLAUDE.md` containing `@AGENTS.md`, or a symlink. Nothing errors
when you get that wrong. The file sits in the root looking authoritative and is never loaded.

**An import does not save context.** `@path` expands at launch exactly as if you had pasted the
file. It is organisation, not reduction.

**A skill's body stays for the rest of the conversation.** The description is always in context and
costs almost nothing; the body enters on first use and does not leave. So a skill is cheap to own
and not cheap to invoke — which changes what belongs in one. A skill you reach for every session has
no advantage over a rule. A long reference you need once a month has an enormous one.

*Cheap to own* holds at a handful and stops holding well before a catalogue. Every description sits
in context before you have asked for anything, so a set of eight hundred is paid for in full, every
session, whether or not one of them is used. Add the body of everything you do invoke, set the total
against whatever the context window is, and then ask where the prompt is supposed to go. So when
somebody tells me proudly how many skills they have, I smile.

**The memory index has a hard limit and no warning.** The first 200 lines or 25KB load; the rest is
dropped. An index that outgrows it loses its tail silently.

**Auto memory is machine-local by design.** It is not shared between machines, which matters if you
work on more than one.

**A symlinked user `CLAUDE.md` is skipped in some desktop session types.** Silently, again.

**Never let the agent maintain its own instruction file.** A file the agent writes agrees with
whatever the agent already believes, which makes it useless as a constraint at the moment you need
one.

## Two machines, one project

<figure>
  <img src="{{ '/img/posts/two-machines-one-project.jpg' | relative_url }}"
       alt="One developer between two computers. Both are wired to the same server in a cloud, and
            both screens show the same prompt. The left one has produced a tidy application; the
            right one has produced a broken layout with a pile of something in the middle of it.">
  <figcaption>Same repository, same decisions, same request. The difference is everything the
  repository does not carry.</figcaption>
</figure>

A workstation and a laptop, one repository. The code is identical on both, and so are the ADRs and
the history, because that is what git is for and it does that job perfectly.

Nothing else was identical. The skills were not in the repository. Most of the agent's own files
were not. And the user-level rules on the two machines had drifted apart, because nothing had ever
compared them and nothing ever would.

So the same request produced different work depending on which desk I was sitting at. Not different
because the decisions differed — the decisions were version-controlled and identical. Different
because what the agent knew **before it read a line of the project** was different.

**The repository makes the code the same and does nothing at all about the agent.** Obvious once
it is written down, and easy to lose track of when every release changes what these files do.

What a pass turns up, counted rather than recalled:

| | |
|---|---|
| Project memory directories | 6 |
| Holding personal notes that belonged elsewhere | 2 |
| Files duplicated between two of them | 5 |
| Stored facts that were wrong and load-bearing | 2 |
| Lines loaded into every session, before | 297 |
| Lines loaded into every session, after | 61 |
| Size of the config directory | 224 MB |
| Of which has to exist on every machine | ~28 KB |

**None of it stays fixed.** Entropy restarts the day a pass ends: a note added in the wrong
place, a fact corrected in conversation and not in the file, a rule that was right last quarter and
is merely plausible now. A cleanup is not a state you arrive at. It decays from the moment it
finishes, at a rate set by how much you are using the thing — which means the more useful your setup
is, the faster it goes out of date.

So the useful output of a pass is not the tidy directory. It is knowing what to look at during the
next one, and having made that cheap enough that it happens.

## It has been moving since I started, and it has not stopped

Two wrong assumptions from the early days, both about cost.

*I did not know a skill stays once loaded.* "Loaded on demand" suggests you pay per use. Half right,
and the half I had wrong is the expensive one.

*And the instruction file swelled.* Every correction became a line and nothing was ever taken out.
It grew **because** I was being diligent — each addition was individually justified — and past some
size adherence drops, which is the opposite of what the additions were for.

This is not a beginner's mistake you outgrow. It happened again while I was writing the rules in
this article: the file reached 297 lines in a single session before it was split back to 61. The
artefact's default behaviour is to accumulate, and knowing that does not stop it.

It is also the same mechanism that ends methodologies, running at a smaller scale and much faster:
**mass, not error.** Nothing in the file was wrong. There was simply too much of it for any of it to
work.

Then there is the other direction, which is harder. The tools change and the configuration does not
move with them. Custom commands were their own mechanism and are now merged into skills — the
command files still work, and the documentation describes them as the older format. That is how this
kind of change arrives: nothing breaks, the thing you built simply stops being the way it is done,
and nobody tells you.

The second instance is better, because I did not find it myself. Someone else raised switching
models part-way through a session. In the agent I built, carrying the context across and staying
independent of any one model was a design goal from the start, so it had never occurred to me that
this could be a constraint anywhere else. **Having solved a problem by design made me blind to it
being a problem at all.**

Which is the same lesson as the context-window explanation, inverted. The view is partial from every
position, including the well-informed ones.

## The fix is a practice, not a tidy-up

**One source per fact, chosen by where it has to be true** — not by which project you happened to be
in when you learned it.

**Split by failure mode, not by topic.** What must load every session is what leaks if it is missed.
Everything else loads on demand, and the worst case there is that the agent lacks a fact and asks
for it. That asymmetry is the whole design: a missing fact costs a question, a missing rule costs a
disclosure.

**Anything a tool can enforce belongs to the tool.** Instructions are context, not configuration. A
formatter plus a hook is the enforcement layer; describing in prose what a linter already does is
cost plus a guaranteed future contradiction.

**Check what a new version changed before assuming your setup still means what it meant.** At this
pace that belongs in the calendar, not in the reaction to a problem.

### Getting it onto every machine

The store that matters is small. On this machine it is the always-on file, the skills and the
user-level rules: about 28 KB out of 224 MB, because everything else is session transcripts, caches
and plugins.

Where each thing lives, and which of them travels:

```
~/
├── .claude/              → private config repo
│   ├── CLAUDE.md         sync    every session
│   ├── skills/           sync    on demand
│   ├── rules/            sync    user-wide
│   ├── machine.md        local   this machine only
│   └── projects/
│       └── <p>/memory/   local   never leaves
│
└── Projects/
    └── product/          → the product repo
        ├── .claude/      rides the repo, if private
        │   ├── CLAUDE.md
        │   ├── rules/    scoped with paths:
        │   └── skills/
        ├── adr/
        ├── spec/
        ├── features/
        └── modules/
            └── service/  → submodule, own repo
                ├── .claude/
                └── adr/
```

Three rules come out of that layout.

**Anything in `~/.claude` is yours and syncs**, except the per-machine file and the memory
directory.

With one qualification: yours is not necessarily the top of the stack. Depending on the version and
how it was installed there can be a layer above it — a machine-wide configuration, or one your
organisation deploys that you cannot switch off from your own settings. If behaviour turns up that
none of your files explain, that is the first place to look, and it is why *check what actually
loaded* keeps being the useful instruction rather than *read your config*.

**Anything under a project rides that project's repository**, which works only while the repository
is private. A public one cannot carry it, and the material has to move up to user scope instead.

**A submodule carries its own.** A nested `CLAUDE.md` or rules directory loads when the work moves
into that module rather than at launch, so a module's conventions arrive with the module.

- **What can sync:** the entries marked `sync` in the tree, in one private repository.
- **What cannot:** auto memory, which is machine-local by design and written mid-session, so version
  control would conflict constantly. Durable knowledge belongs in a skill; the store keeps what is
  genuinely local.
- **What must not:** anything private, into anything public. House rules for a public repository
  live at user scope for exactly that reason.
- **How:** an **allowlist** `.gitignore` — ignore everything, unignore what travels. A denylist
  commits every new kind of file that appears until somebody writes a rule for it; an allowlist
  excludes it until somebody decides.
- **Real files, not symlinks**, because a symlinked user file is skipped in some session types, and
  because symlinks need administrator rights on Windows.
- One untracked file per machine for what is only true there, pulled in with an import.

The whole of it — seven files, three empty slots, and the reason the figure earlier is 28 KB
rather than a backup:

```
claude-config/            private, cloned into ~/.claude
├── .gitignore            the allowlist
├── README.md             what is here and what is not
├── CLAUDE.md             always on: the hard rules, and pointers
├── skills/
│   ├── author/           who is writing, and in what voice
│   ├── thesis/           the argument the writing rests on
│   ├── house-rules/      conventions for one publication
│   └── illustrations/    how drawings get commissioned
├── rules/                allowed, empty: user-wide conventions
├── agents/               allowed, empty: subagent definitions
└── output-styles/        allowed, empty: voice and format
```

Nothing else is in it. No settings, no transcripts, no memory, no credentials — the allowlist keeps
them out by default rather than by remembering to exclude them.

What it buys, as the measurement: the same rules on every machine I work from, and 61 lines in
every session instead of 297.

## So, about addressing you formally

If you want the agent to address you formally, the instruction goes in your user-level `CLAUDE.md`,
and it will still be there next year.

As a **detector for a context reset**, though, the trick is fine. It is a canary, and a canary that
costs one line is a good trade. It just was never a measure of the model.

Everyone working with an agent has the same problem, and each of us stands in a different part of
it: the developer running the formality test, the person whose instruction file has quietly tripled,
the one building the context window from the inside.

Three vantage points on one question — *what does the model know at this token, and how did it get
there.*

Nobody in that list is short of knowledge. They are standing somewhere specific, and the view from
there is partial for everyone — including me, twice in this article: once by knowing too much, and
once by having designed a problem away.
