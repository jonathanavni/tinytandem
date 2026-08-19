---
description: Session close — update project state and capture decisions
---

# /wrapup

End every productive session by persisting what happened. This is the other half of the continuity loop: `/wrapup` writes the state down so the next session's `/start` reads it back in and picks up exactly where this one left off — with no re-explaining.

## Steps

1. **Update `PLAN.md` "Current State":**
   - What was accomplished this session
   - What's blocked or needs attention
   - What to do next session
   - Check off completed milestone tasks
   - **Archive review — every session, not just on milestone completion:** scan `PLAN.md` for detail no longer load-bearing for current/next work and move it to `PLAN-archive.md`, collapsing it to a short summary. The test: *"is this still needed to understand current/next work?"* Keep Current State lean, not empty. The **Decisions Log stays in `PLAN.md`** (cumulative, never archived). When unsure, keep.
     - **Session-narrative retention rule:** keep a session narrative only while load-bearing — recent (last ~5 sessions), in-flight, or holding an open thread. Otherwise collapse to a one-line summary + commit/PR ref and move detail to `PLAN-archive.md`.

2. **Append to `.claude/memory/MEMORY.md`** (only if there's something worth remembering):
   - Non-obvious decisions made and why
   - Gotchas discovered
   - Conventions established
   - Don't add obvious things. Don't duplicate what's in `PLAN.md`.

3. **Check `MEMORY.md` size** — if it's getting long (~200 lines), suggest a consolidation pass. A consolidation pass means: merge duplicate entries, fix or delete facts that turned out to be wrong, collapse superseded entries into their topic file, lift decisions that became durable from `PLAN.md`'s log into `decisions_product.md`, and keep the index at one line per entry. Consolidation is its own small session, not a side effect of wrapup.

4. **Doc hygiene (skip if no docs were touched and no milestone completed):** scan your docs for staleness — a new doc that isn't linked from the index, a doc whose body says "superseded" but wasn't moved, broken links to moved files, status lines still saying "pending" for work that shipped. **Surface, don't auto-fix** — the right correction is usually judgment-laden, and a wrong "fix" is worse than a stale doc. The one safe auto-fix is flipping a shipped item's status from "pending" to "shipped (commit/PR ref)".

5. **Falsified-claims grep (don't skip this one — it matters most when you *didn't* touch the docs):** search the canonical docs (`README`, `PLAN.md`, `docs/`) for statements today's work made false — a "no CI yet" after CI shipped, a "pending" for something that landed, a count or version the session moved. Fix the flips that are mechanical; surface the ones that need judgment.

6. **Brief summary** — 3-5 sentences of what happened and what's next.

## Rules

- Don't skip step 2 even if the session was short — small decisions compound.
- Write decisions with enough context for future-you to understand *why*.
- This should take under 2 minutes. If it's taking longer, you're over-documenting.
