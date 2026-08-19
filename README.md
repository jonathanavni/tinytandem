# tinytandem

A **tiny, minimalist two-agent coding harness**. A drop-in skeleton for running coding projects with two AI models instead of one: an *orchestrator* that plans and holds context (Claude Code), and an *adversary* that implements bounded slices and reviews everything before it merges (OpenAI's Codex).

No framework, no runtime, no dependencies. It's about a dozen markdown files you can read in one sitting and own every line of: the commands, the memory layout, the planning docs, and the cross-model review ladder, without rebuilding them from scratch. Fork it, read it, bend it to your project.

This repo is the companion to the essay **[Two Models Today, Meta-Harnesses Tomorrow](https://jonathanavni.com/blog/two-models-today-meta-harnesses-tomorrow)**, which explains the *why*. This README is the *how*.

---

## The idea

One agent owns continuity. The other owns bounded challenge.

- **Claude (the orchestrator)** runs the session, holds the plan, owns the project's memory, sequences the work, and integrates everything. It's the agent that remembers *why* a thing was built the way it was.
- **Codex (the adversary)** gets handed two kinds of jobs: implement a tightly-scoped slice on its own branch, or review a plan or a diff adversarially. It works from a *contract*, never the orchestrator's full history.

The one rule that makes it work: **the model that didn't write the code is the one that reviews it.** Across sessions, a same-family review and a cross-family review catch largely *disjoint* issues. Running both is coverage, not redundancy, and it's the cheapest way to shrink the blind spots a single model ships with.

This is **model-agnostic.** Claude and Codex are what this skeleton is wired for, but the roles matter more than the brands. You could run the orchestrator on one frontier model and the adversary on another (Gemini, GLM, and so on). What makes it work is that the reviewer is a *different family* than the implementer.

---

## The review ladder

For low-stakes work, the orchestrator just implements directly. For high-risk surfaces (schema migrations, auth, payments, public API contracts, bulk data writes, anything touching a locked invariant), run the full ladder:

1. The human gives high-level requirements and acceptance criteria.
2. **Claude** drafts the plan.
3. **Codex** reviews the plan adversarially, before any code is written.
4. **Claude** synthesizes the findings and locks the plan.
5. **Codex** implements one bounded slice on its own branch.
6. **Claude** reviews the diff (fresh-context QA, defaults to rejection).
7. **Codex** reviews the same diff adversarially.
8. **Claude** integrates, merges, and updates the plan and memory.

The full pattern (dispatch mechanics, build-vs-audit framing, round-2 iteration rules, synthesis discipline, and the handoff packet templates) lives in **[`docs/handoff-pattern.md`](docs/handoff-pattern.md)**. That doc is the heart of this repo; the rest is scaffolding around it.

---

## The continuity loop

The other half of the system is keeping context across sessions without re-explaining anything:

- **`/start`** reads `PLAN.md` and `.claude/memory/` and proposes priorities. It does not start work.
- **`/wrapup`** records what got done, the decisions and the reasoning behind them, and updates the memory files and planning docs.
- Because `/wrapup` writes the state down, the next session's `/start` reads it back in and picks up exactly where the last one left off.

---

## What's in here

```
.
├── CLAUDE.md                     # starter project memory: encodes the workflow, fill in the placeholders
├── PLAN.md                       # active work + cumulative Decisions Log
├── PLAN-archive.md               # completed / no-longer-load-bearing detail
├── BACKLOG.md                    # idea funnel for unscoped, unprioritized work
├── SCHEMA.md                     # optional: canonical data-model / contract doc
├── docs/
│   ├── README.md                 # what lives in docs/ (planning docs + draft specs)
│   ├── handoff-pattern.md        # the centerpiece: how Claude hands off to Codex
│   └── archive/                  # superseded planning docs and specs
├── templates/
│   ├── implementation-handoff.md # fill-in-the-blank packet for an implementation slice
│   └── review-handoff.md         # fill-in-the-blank packet for an adversarial review
├── guides/
│   ├── delegation.md             # in-Claude subagent delegation: when, the loop, prompt template
│   └── verification.md           # quality gates, task contracts, test-first fixing
└── .claude/
    ├── settings.json             # permissions + a format-on-write hook
    ├── commands/
    │   ├── start.md              # session kickoff (read-only orientation)
    │   ├── wrapup.md             # session close (persist state + decisions)
    │   └── review.md             # fresh-context QA reviewer that defaults to rejection
    ├── rules/
    │   └── core.md               # code-quality, behavior, safety, context-hygiene rules
    └── memory/
        ├── MEMORY.md             # index of the topic-based memory files
        ├── decisions_product.md  # standing product / architecture decisions
        ├── gotchas.md            # sharp edges discovered the hard way
        ├── conventions.md        # naming / structure / workflow conventions
        └── sessions-archive.md   # one-line-per-session history index
```

---

## Quickstart

1. **Copy the skeleton** into your project (or clone and build on top of it).
2. **Install the [OpenAI Codex plugin for Claude Code](https://github.com/openai/codex-plugin-cc)** and authenticate it. That's what lets the orchestrator dispatch Codex. Pin the working dispatch invocation for your machine in `.claude/memory/gotchas.md`.
3. **Fill in `CLAUDE.md`.** Replace the `<placeholders>` with your project's specifics. The principles, workflow, session, and state-file sections are the reusable spine, so keep them.
4. **Adjust `.claude/settings.json`.** The format hook defaults to Prettier; swap it for your language's formatter, or remove it.
5. **Run `/start`** and go.

---

## Honest scope

- **This is a methodology repo, not a runtime.** It's the templates, commands, and the handoff pattern, not an installable tool. The actual agents are Claude Code and the Codex plugin; this repo is how you make them work together.
- **The Codex plugin is OpenAI's**, not vendored here. This skeleton references it and shows the wiring.
- **Not every change needs the full ladder.** It's for the small set of surfaces where a silent bug is expensive. Knowing when to spend the overhead is half the skill.

---

## License

MIT, see [LICENSE](LICENSE).
