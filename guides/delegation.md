# Delegation Guide

This guide covers in-Claude subagent delegation (Explore, Plan, general-purpose, etc.). For Codex handoffs — a separate runtime, different model family, with its own packet templates and branch discipline — see [`docs/handoff-pattern.md`](../docs/handoff-pattern.md).

## When to Delegate

Before each task, assess: handle directly or delegate?

**Delegate when any of these apply:**
- The task benefits from fresh context (complex reasoning, independent scope)
- You can work on something else in parallel
- Context is getting tight (over ~50%)
- The task is research or review (always delegate these)

**Work directly when:**
- It's a simple sequential task and context is spacious
- Faster iteration matters (tight feedback loop with the user)
- The task requires context from the current conversation that's hard to summarize

**Default the worker to a strong reasoning model.** Don't downgrade unless there's a specific reason.

## The Delegation Loop

```
ORCHESTRATOR (main session — holds the plan, assesses each task)
     │
     │ 1. Write the task spec (goal, files owned, boundaries, how to verify)
     │
     ├──► WORKER (fresh context, strong model)
     │         │
     │         │ Returns: status + summary + concerns + files changed
     │         │
     │    2. Validate the report — sections present? Status DONE or BLOCKED?
     │         │
     │         │ if BLOCKED → unblock and re-dispatch
     │         │ if DONE ──►
     │
     ├──► /review (fresh context — see .claude/commands/review.md)
     │         │
     │         │ Returns: PASS / NEEDS_WORK with structured issues
     │         │
     │    3. Act on the review
     │         │ NEEDS_WORK → fix critical issues, re-review if needed
     │         │ PASS → integrate and move to the next task
     │
     └── Next task
```

**Scale the loop to the task.** A simple bug fix: implement directly, spot-check. A milestone completion: implement (directly or delegated), then `/review` with fresh context.

## Handoff Format

Every subagent should receive:

1. **What to change** — the specific goal
2. **Which files it owns** — explicit scope
3. **What not to touch** — boundaries
4. **How to verify** — the test, command, or check that confirms it works

**Context discipline:** paste relevant context into the prompt. Don't say "read CLAUDE.md" — the subagent can't see your conversation history.

## Implementer Prompt Template

```
You are implementing a specific task for the <project> project.

## Task
[What to build/change — a specific deliverable]

## Context
[Relevant code, conventions, schema, constraints.
Paste what the agent needs — don't reference files it can't see.]

## Scope
- Files you own: [explicit list]
- Do NOT touch: [boundaries]

## Rules
1. Implement exactly what is described. Do not add features or refactor surrounding code.
2. If the spec is ambiguous, report NEEDS_CONTEXT with specific questions.
3. BLOCKED is always better than wrong.
4. Your output will be reviewed by a separate agent.

## Report Format
Status: DONE | DONE_WITH_CONCERNS | BLOCKED | NEEDS_CONTEXT
Summary: [What was accomplished, in 2-3 sentences]
Concerns: [Anything the reviewer should look at, or "None"]
Files changed: [List with one-line descriptions]
```

## Reviewer

Use the `/review` command (`.claude/commands/review.md`). It spawns a fresh-context reviewer with a structured checklist, depth scaling based on change magnitude, and a structured PASS / NEEDS WORK report. Don't duplicate the reviewer prompt here — the command is the source of truth.

## Before Dispatching

- [ ] The task description is self-contained (the agent doesn't need conversation history)
- [ ] Context is pasted, not referenced
- [ ] The report format is included in the prompt
- [ ] The model is set to a strong reasoning model

## After Receiving

- [ ] All required report sections are present
- [ ] If sections are missing, push back — don't accept incomplete reports
- [ ] For reviews: fix critical issues before proceeding
