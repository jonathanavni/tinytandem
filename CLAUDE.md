# CLAUDE.md — <Your Project>

> This is a starter `CLAUDE.md` for the two-model (Claude + Codex) workflow. Replace the angle-bracket placeholders with your project's specifics and delete guidance you don't need. The principles, workflow, session, and state-file sections are the reusable spine — keep them.

## Project

<One paragraph: what this project is, who it's for, the core constraint. Keep it short — this is orientation, not a spec.>

## Core Principles

- **Verification is the #1 lever** — give every task a way to prove it worked (a test, a command, a check). This is the single biggest quality multiplier.
- **Simplicity first** — prefer the boring, obvious solution. Can this be fewer lines? Are the abstractions earning their complexity?
- **Push back when warranted** — if an approach has clear problems, say so directly, propose an alternative, accept an override. Sycophancy is a failure mode.
- **No over-engineering** — don't add features, abstractions, or error handling beyond what's asked. Don't touch code you weren't asked to touch.
- **Naive-then-optimize** — implement the obviously-correct version first, verify correctness, then optimize. Never skip step 1.
- **Compaction-safe artifacts** — write important outputs (schemas, decisions, data) to files immediately. Don't rely on conversation history surviving.
- **Silent-wrong is an observability gap** — a job that runs green but is quietly wrong is two bugs: the wrong behavior and the missing way to detect it. For each component, ask "what's the absence-detection signal?"

## Workflow

- **Assess before each task** — handle directly, delegate to a subagent, or hand off to Codex. Delegate when the task benefits from fresh context, can run in parallel, or when context is getting tight. Simple sequential work with spacious context → do it directly.
- **When delegating to a subagent, default to a strong reasoning model.** Hold the plan yourself; delegate precise task specs; receive reports back. Workers get fresh context windows.
- **Always delegate research and reviews** — they benefit from isolation regardless of context pressure (see `/review`).
- **For cross-model work — adversarial review and scoped implementation slices — hand off to Codex.** See [`docs/handoff-pattern.md`](docs/handoff-pattern.md).
- **For high-risk-surface work** (schema/migrations, auth, payments, public API contracts, bulk data writes, anything touching a locked gate): default to the full plan → implement → review ladder in [`docs/handoff-pattern.md`](docs/handoff-pattern.md) §4. Claude is continuity owner; Codex implements the bounded slice. Claude implementing high-risk code in parallel collapses the cross-model coverage the ladder exists to provide.
- Enter plan mode for any non-trivial task (3+ steps or an architectural decision).
- Use `/start` at session start, `/wrapup` at session end, `/review` after completing a milestone.

## Session Management

- `/clear` between unrelated tasks; `/compact` to keep focus while clearing noise.
- **Two-correction rule**: if wrong twice on the same thing, `/clear` and write a sharper prompt.
- Feed raw data (logs, errors, API responses) instead of your interpretation of them.
- Use neutral prompts — "read this code, follow the logic, report findings" beats "find the bug."

## Project State Files

Each fact has ONE home. Keep these from drifting:

- **`PLAN.md`** — active work: current-milestone tasks, technical design, verification, the session-by-session narrative, and the **cumulative Decisions Log** ("X over Y because Z" — never archived). Read at session start, update at session end. On larger projects, keep the launch/scope checklist in a canonical roadmap doc under `docs/` that `PLAN.md` *points to* rather than re-lists, so the two can't drift; `/start` reads it alongside `PLAN.md`.
- **`PLAN-archive.md`** — full detail of completed / no-longer-load-bearing work, moved out of `PLAN.md` so Current State stays lean. Reviewed every `/wrapup`. The test: "still needed to understand current/next work?"
- **`docs/`** — project documentation: the permanent methodology doc (`handoff-pattern.md`) plus your planning docs and draft specs. Superseded docs move to `docs/archive/` (see `docs/README.md`).
- **`SCHEMA.md`** *(optional)* — the canonical data-model / contract doc, if your project has one. Update it in the same commit as any schema change.
- **`.claude/memory/`** — topic-based memory files indexed by `MEMORY.md`: standing product/architecture decisions (`decisions_product.md`), `gotchas`, `conventions`, and a one-line-per-session history index (`sessions-archive.md`). Portable — copy the folder to onboard another agent.
- **`BACKLOG.md`** — idea funnel: unprioritized potential work; most never ships. Prioritized items graduate into `PLAN.md`. If you work across git worktrees, gitignore it and treat the primary worktree's copy as canonical, since copies drift.

`/start` reads these (read-only) to orient and propose; `/wrapup` updates them.

## Tools

- **Codex plugin** — a fresh-context, different-model-family channel for (1) adversarial plan review pre-impl, (2) scoped implementation slices on a `codex/<task>` branch, (3) post-impl adversarial review, (4) rescue / deeper investigation when stuck. Claude dispatches Codex directly via bash; don't wait for the user to type a slash command. Canonical workflow + handoff packet templates: [`docs/handoff-pattern.md`](docs/handoff-pattern.md). Run it after `/review` for gating / correctness / security / payment code — cross-model catches blind spots same-model review shares.

## Key Documents

| Document | When to read |
|----------|--------------|
| [`docs/handoff-pattern.md`](docs/handoff-pattern.md) | Before any Codex handoff — dispatch, role split, the ladder, synthesis discipline, templates |
| `PLAN.md` | Session start — current state and decisions log |
| `.claude/memory/MEMORY.md` | Index of standing decisions, gotchas, conventions |
| [`docs/README.md`](docs/README.md) | To find a planning doc or draft spec |
| `SCHEMA.md` *(optional)* | Before writing schema- or contract-touching code |
